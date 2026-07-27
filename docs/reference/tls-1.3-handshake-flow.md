# `curl -v`で観察するTLS 1.3ハンドシェイク

## この文書の目的

この文書では、`curl -v`に表示された次の部分を、TLS 1.3の時系列に沿って説明します。

```text
* (304) (OUT), TLS handshake, Client hello (1):
*  CAfile: /etc/ssl/cert.pem
*  CApath: none
* (304) (IN), TLS handshake, Server hello (2):
* (304) (IN), TLS handshake, Unknown (8):
* (304) (IN), TLS handshake, Certificate (11):
* (304) (IN), TLS handshake, CERT verify (15):
* (304) (IN), TLS handshake, Finished (20):
* (304) (OUT), TLS handshake, Finished (20):
```

ここで確認するのは、次の流れです。

```text
通信条件を提案する
  ↓
通信条件を決定する
  ↓
サーバー証明書を受け取る
  ↓
接続相手を検証する
  ↓
ハンドシェイク全体を相互に確認する
  ↓
暗号化されたHTTP通信を開始する
```

暗号アルゴリズム内部の計算をすべて理解することではなく、それぞれのメッセージが何を決め、何を確認しているのかを区別することを目的とします。

---

## 1. 表示の読み方

### 行頭の記号

`curl -v`の行頭にある記号には、次の意味があります。

| 記号 | 意味 |
|---|---|
| `*` | curlが表示する接続・TLSなどの補足情報 |
| `>` | curlから送信したHTTPリクエスト |
| `<` | サーバーから受信したHTTPレスポンス |

この文書で扱う行は、すべて `*`から始まっています。HTTPメッセージそのものではなく、curlが通信の進行状況を人間向けに表示したものです。

### `IN`と`OUT`

方向はcurl、つまりクライアント側を基準にします。

| 表示 | 方向 |
|---|---|
| `OUT` | curlからサーバーへ送信 |
| `IN` | サーバーからcurlが受信 |

```text
OUT
curl ─────────────────────────→ サーバー

IN
curl ←───────────────────────── サーバー
```

### `(304)`の意味

次の `(304)`はHTTPステータスコードではありません。

```text
* (304) (IN), TLS handshake, Server hello (2):
```

この表示では、TLS 1.3のバージョン値 `0x0304`が16進数の`304`として表示されています。

```text
(304)
  ≠ HTTP 304 Not Modified

(304)
  = TLSの内部バージョン値0x0304
  = TLS 1.3
```

### 末尾の番号

メッセージ名の後ろにある番号は、TLSで定義されたハンドシェイクメッセージの種類です。

| 番号 | メッセージ |
|---:|---|
| `1` | ClientHello |
| `2` | ServerHello |
| `8` | EncryptedExtensions |
| `11` | Certificate |
| `15` | CertificateVerify |
| `20` | Finished |

処理の回数、HTTPステータスコード、エラー番号ではありません。

---

## 2. TLSハンドシェイク全体の流れ

今回観察した通信では、クライアント証明書を使わない一般的なHTTPSのTLS 1.3ハンドシェイクが行われています。

```text
curl                                          サーバー
  │                                              │
  │ ─── ClientHello ──────────────────────────→ │
  │     利用可能なTLS条件を提案                   │
  │                                              │
  │ ←── ServerHello ─────────────────────────── │
  │     TLS条件を選択し、鍵の材料を返す           │
  │                                              │
  │     双方がハンドシェイク用の鍵を導出           │
  │                                              │
  │ ←── EncryptedExtensions ─────────────────── │
  │     ALPNなどの拡張機能の選択結果              │
  │                                              │
  │ ←── Certificate ─────────────────────────── │
  │     サーバー証明書と中間CA証明書を提示         │
  │                                              │
  │     curlが証明書チェーン、                    │
  │     ホスト名、有効期間などを検証               │
  │                                              │
  │ ←── CertificateVerify ───────────────────── │
  │     サーバーが秘密鍵を持つことを証明           │
  │                                              │
  │ ←── Finished ────────────────────────────── │
  │     サーバー側のハンドシェイク確認値           │
  │                                              │
  │ ─── Finished ─────────────────────────────→ │
  │     クライアント側のハンドシェイク確認値       │
  │                                              │
  │     双方がアプリケーション通信用の鍵を確立      │
  │                                              │
  │ ═══ 暗号化されたHTTP通信 ═══════════════════ │
```

`CAfile`と`CApath`は、この図の中を流れるTLSメッセージではありません。証明書を検証するときにcurlが参照する、クライアント側の設定です。

---

## 3. `Client hello (1)`

```text
* (304) (OUT), TLS handshake, Client hello (1):
```

curlからサーバーへ `ClientHello`を送信しています。

ClientHelloでは、クライアントが利用できる条件をサーバーへ提案します。

主な内容は次のとおりです。

- 利用できるTLSバージョン
- 利用できる暗号スイート
- 鍵交換に必要な情報
- 接続したいサーバー名
- ALPNで利用できるアプリケーションプロトコル
- ハンドシェイクに使用するランダム値

今回のALPN候補は、別の行で次のように表示されています。

```text
* ALPN: curl offers h2,http/1.1
```

これは、curlが次を提案しているという意味です。

```text
h2        = HTTP/2
http/1.1  = HTTP/1.1
```

ClientHelloは「この条件を必ず使用する」という決定ではありません。curlが利用可能な候補をサーバーへ提示する処理です。

---

## 4. `CAfile`と`CApath`

```text
*  CAfile: /etc/ssl/cert.pem
*  CApath: none
```

この2行は、サーバーへ送信されたTLSメッセージではありません。サーバー証明書を検証するときに使う、curl側の信頼情報を示しています。

### `CAfile: /etc/ssl/cert.pem`

`/etc/ssl/cert.pem`というCAバンドルを、証明書検証に使用します。

```text
/etc/ssl/cert.pem
├── 信頼するルートCA証明書
├── 信頼するルートCA証明書
└── ...
```

これは信頼できるサーバー名の一覧でも、クライアント証明書でもありません。サーバー証明書の署名を検証するための、信頼済みCA証明書の集合です。

### `CApath: none`

CA証明書を個別ファイルとして格納したディレクトリは、別途設定されていないことを示します。

```text
CAfile
  複数のCA証明書を1つのファイルにまとめる

CApath
  CA証明書をディレクトリ内の個別ファイルとして管理する
```

今回のcurlは `CAfile`を利用するため、`CApath: none`でも証明書を検証できます。`none`はエラーではありません。

ログ上ではClientHelloの直後に表示されていますが、`CAfile`や`CApath`がClientHelloの一部としてサーバーへ送られたわけではありません。curlがローカル設定をこの位置で表示しているだけです。

---

## 5. `Server hello (2)`

```text
* (304) (IN), TLS handshake, Server hello (2):
```

サーバーから `ServerHello`を受信しています。

サーバーは、ClientHelloで提示された候補を確認し、実際に使用する条件を選択します。

主な内容は次のとおりです。

- 使用するTLSバージョン
- 使用する暗号スイート
- 鍵交換に必要なサーバー側の情報
- ハンドシェイクに使用するランダム値

```text
curlが候補を提示
  ├── TLS 1.3
  ├── TLS 1.2
  └── 複数の暗号スイート
        ↓
サーバーが選択
  ├── TLS 1.3
  └── 1つの暗号スイート
```

ClientHelloとServerHelloで交換した情報を利用して、クライアントとサーバーは同じハンドシェイク用の鍵をそれぞれ導出します。

秘密の通信用鍵そのものをネットワーク上で送っているわけではありません。双方が交換した情報と、それぞれが持つ秘密の値から同じ鍵を計算します。

この段階では通信条件と鍵の材料が決まりましたが、サーバー証明書の検証はまだ完了していません。

---

## 6. `Unknown (8)`、EncryptedExtensions

```text
* (304) (IN), TLS handshake, Unknown (8):
```

番号 `8`は、TLS 1.3の `EncryptedExtensions`メッセージです。

curlとTLSライブラリの組み合わせが番号8に対応する表示名を持っていないため、`Unknown`と表示されています。エラーや不明な攻撃を意味するものではありません。

EncryptedExtensionsでは、ClientHelloで提案されたTLS拡張について、サーバーの選択結果などが通知されます。

今回の場合、代表的なものがALPNです。

```text
curl
  h2とhttp/1.1を提案
        ↓
サーバー
  h2を選択
        ↓
* ALPN: server accepted h2
```

TLS 1.3では、ServerHelloの後に双方がハンドシェイク用の鍵を導出します。そのため、EncryptedExtensions以降の多くのハンドシェイクメッセージは暗号化して送られます。

---

## 7. `Certificate (11)`

```text
* (304) (IN), TLS handshake, Certificate (11):
```

サーバーから証明書を受信しています。

通常、サーバーは次を送ります。

- 接続先ドメインのサーバー証明書
- 証明書チェーンの構築に必要な中間CA証明書

ルートCA証明書は、通常サーバーから送りません。クライアントは、サーバーから送られたルートCAをそのまま信頼するのではなく、自分の信頼ストアにあらかじめ登録されたルートCAを信頼の起点にします。

```text
サーバーから受信
  サーバー証明書
        ↓ 署名
  中間CA証明書
        ↓ 署名
クライアント側で保持
  /etc/ssl/cert.pem内の信頼済みルートCA証明書
```

### curlが行う証明書検証

curlは、受信した証明書について主に次を確認します。

| 確認項目 | 内容 |
|---|---|
| 証明書チェーン | 信頼済みルートCAまで署名をたどれるか |
| ホスト名 | URLのホスト名と証明書のSANが一致するか |
| 有効期間 | 現在時刻が証明書の有効期間内か |
| 電子署名 | チェーン内の証明書の署名が正しいか |
| 利用目的 | サーバー認証用として利用できるか |

この検証が成功すると、後で次のように表示されます。

```text
* SSL certificate verify ok.
```

検証できなかった場合、「悪意のあるサーバーだと確定した」という意味ではありません。しかし、curlは接続相手が正しいことを確認できないため、原則として接続を中止します。

---

## 8. `CERT verify (15)`、CertificateVerify

```text
* (304) (IN), TLS handshake, CERT verify (15):
```

サーバーから `CertificateVerify`を受信しています。

サーバーは、ここまでのハンドシェイク内容へ、サーバー証明書の公開鍵に対応する秘密鍵を使って署名します。

curlは、サーバー証明書に含まれる公開鍵を使って、その署名を検証します。

```text
サーバー
  証明書を提示
  ├── ドメイン名
  └── 公開鍵

サーバー
  対応する秘密鍵でハンドシェイク内容へ署名
        ↓
curl
  証明書の公開鍵で署名を検証
```

これにより、サーバーが次を証明します。

> 提示した証明書をコピーしてきただけではなく、その証明書に対応する秘密鍵を実際に持っている。

### `CERT verify`と証明書検証結果の違い

次の2つは、名前が似ていますが意味が異なります。

```text
CERT verify (15)
  TLSのCertificateVerifyメッセージ
  サーバーが対応する秘密鍵を持つことを証明する

SSL certificate verify ok.
  curlによる証明書検証の最終結果
  証明書チェーン、ホスト名、有効期間などを確認した
```

---

## 9. サーバーからの `Finished (20)`

```text
* (304) (IN), TLS handshake, Finished (20):
```

サーバーから `Finished`メッセージを受信しています。

サーバーは、ここまでに送受信したハンドシェイク全体と、導出したハンドシェイク用の鍵から確認値を計算します。

curlも同じ情報から確認値を計算し、受信した値と一致するか検証します。

```text
ClientHello
ServerHello
EncryptedExtensions
Certificate
CertificateVerify
        ↓
ハンドシェイク全体から確認値を計算
        ↓
サーバーFinished
```

Finishedの検証によって、主に次を確認します。

- ハンドシェイク中のメッセージが改ざんされていない
- クライアントとサーバーが同じ鍵を導出できている
- サーバーがここまでの交渉内容を同じものとして認識している

確認値が一致しなければ、curlはTLS接続を中止します。

---

## 10. curlからの `Finished (20)`

```text
* (304) (OUT), TLS handshake, Finished (20):
```

curlからサーバーへ、クライアント側の `Finished`メッセージを送信しています。

今度はサーバーが、クライアントから受け取った確認値を検証します。

```text
サーバー → curl
  Finished
  サーバー側の確認値

curl → サーバー
  Finished
  クライアント側の確認値
```

双方のFinishedを検証できると、TLSハンドシェイクが完了します。その後は、アプリケーションデータ用の鍵を使ってHTTP通信を暗号化します。

このFinishedは、クライアント証明書を提示したという意味ではありません。クライアント証明書を使わない通常のHTTPSでも、クライアントはFinishedを送ります。

---

## 11. TLS確立後のHTTP通信

ハンドシェイクが完了すると、curlには次のような情報が表示されます。

```text
* SSL connection using TLSv1.3 / ...
* ALPN: server accepted h2
* SSL certificate verify ok.
* using HTTP/2
```

それぞれの意味は次のとおりです。

| 表示 | 意味 |
|---|---|
| `SSL connection using TLSv1.3` | TLS 1.3の接続確立に成功した |
| `ALPN: server accepted h2` | HTTP/2を使用することに決まった |
| `SSL certificate verify ok.` | サーバー証明書の検証に成功した |
| `using HTTP/2` | 確立したTLS接続上でHTTP/2を使用する |

その後、HTTP/2のストリームを開き、GETリクエストを送信します。

HTTP/2ストリームの仕組みとHTTP/1.1との違いは、[HTTP/1.1とHTTP/2のストリーム](http1.1-vs-http2-streams.md)にまとめています。

```text
* [HTTP/2] [1] OPENED stream for https://www.pokemon.co.jp/
* [HTTP/2] [1] [:method: GET]
* [HTTP/2] [1] [:scheme: https]
* [HTTP/2] [1] [:authority: www.pokemon.co.jp]
* [HTTP/2] [1] [:path: /]
```

ここで初めて、TLSの接続確立からHTTPのリクエスト処理へ移ります。

```text
TLS
  接続相手の確認と暗号化通信の確立
        ↓
HTTP
  GET / を送信してリソースを要求
```

---

## 12. 各メッセージが確認していること

| 段階 | 主な処理 | この段階だけで分からないこと |
|---|---|---|
| ClientHello | curlが利用可能な条件を提案する | 実際に採用される条件 |
| ServerHello | TLS条件を選択し、鍵の材料を交換する | サーバー証明書が正しいか |
| EncryptedExtensions | ALPNなどの拡張の選択結果を返す | サーバーの身元 |
| Certificate | サーバー証明書と中間CA証明書を提示する | サーバーが秘密鍵を持つか |
| CertificateVerify | サーバーが秘密鍵を持つことを証明する | ハンドシェイク全体が改ざんされていないか |
| Server Finished | サーバー側からハンドシェイク全体を確認する | クライアント側の確認が完了したか |
| Client Finished | クライアント側からハンドシェイク全体を確認する | HTTPレスポンスの内容 |
| HTTP通信 | 暗号化された接続上でリクエストとレスポンスを交換する | Webサイトの内容が善良か |

TLS証明書によって確認できるのは、「指定したドメインを名乗る資格を持つ相手か」という点です。Webサイトの内容、脆弱性の有無、運営者の善良さまでは保証しません。

---

## 13. どこで失敗する可能性があるか

| 失敗箇所 | 例 |
|---|---|
| TCP接続 | ホストへ到達できない、443番ポートが閉じている |
| ServerHello前後 | 利用可能なTLSバージョンや暗号方式が一致しない |
| 証明書チェーン検証 | 信頼済みルートCAまでたどれない |
| ホスト名検証 | URLのホスト名と証明書のSANが一致しない |
| 有効期間検証 | 証明書が期限切れ、または有効開始前 |
| CertificateVerify | 署名が不正、対応する秘密鍵を証明できない |
| Finished | ハンドシェイク内容が一致しない、改ざんを検出した |

証明書検証を無効化する `curl -k`または`curl --insecure`は、接続相手の確認を省略します。検証用として挙動を確認する場合を除き、証明書エラーの解決方法として使用してはいけません。

---

## 14. 学習メモへ記録する例

```markdown
- TLSハンドシェイクの観察：
  - curlはClientHelloで、利用できるTLS条件とALPNの候補を提示した。
  - `CAfile`は証明書検証に使うCAバンドルであり、サーバーへ送る情報ではない。
  - `CApath: none`はCA証明書ディレクトリを使用していないことを示す。今回は`CAfile`を利用するためエラーではない。
  - サーバーはServerHelloでTLS条件を選択し、双方がハンドシェイク用の鍵を導出した。
  - `Unknown (8)`はTLS 1.3のEncryptedExtensionsであり、エラーではない。
  - サーバーはCertificateでサーバー証明書と中間CA証明書を提示した。
  - curlは証明書チェーン、ホスト名、有効期間などを検証した。
  - サーバーはCertificateVerifyで、証明書に対応する秘密鍵を持つことを証明した。
  - サーバーとcurlはそれぞれFinishedを送り、ハンドシェイク全体が一致していることを確認した。
  - TLS接続の確立後、ALPNで選択されたHTTP/2を使ってGETリクエストを送信した。
```

---

## 15. 参考資料

- [HTTP/1.1とHTTP/2のストリーム](http1.1-vs-http2-streams.md)
- [`curl -v`で観察するHTTPレスポンス](http-response-structure-and-headers.md)
- [TLS証明書と認証局による信頼](tls-certificates-and-ca-trust.md)
- [curl：TLS Certificate Verification](https://curl.se/docs/sslcerts.html)
- [RFC 8446：TLS 1.3](https://www.rfc-editor.org/rfc/rfc8446.html)
- [RFC 7301：ALPN](https://www.rfc-editor.org/rfc/rfc7301.html)
- [RFC 9113：HTTP/2](https://www.rfc-editor.org/rfc/rfc9113.html)
