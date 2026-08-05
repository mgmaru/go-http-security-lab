# Phase 0 進捗

## フェーズ基本情報
- 開始日： 2026/7/26（日）
- 完了予定日： 2026/7/28（火）
- 完了日：
- 予定期間：
- 実際の経過期間：
- 実際の作業時間：
- 現在の状態：未着手 / 進行中 / 保留 / 完了
- 予定との差：
- 差が生じた理由：
- 主な障害：
- 次の調整：

---
## メモ
### 2026/7/26
### やったこと
- `curl -v`でGETリクエストを送る
- `curl -i`でレスポンスヘッダーを確認する
### 学んだこと
#### `curl -v`でGETリクエストを送る
1. やったこと：URLにGETリクエストを送る。
2. コマンド：`curl -v [URL]` -> `-v`は`vervose`（詳細情報）の略であり、以下の情報がえられる。
  - `*`：curlによる接続・TLSなどの情報
  - `>`：curlが送信したHTTPリクエスト
  - `<`：サーバーから受信したHTTPレスポンス
3. 対象URL：`https://www.pokemon.co.jp/`
4. 結果
```curl
hiroaki@HiroakinoMacBook-Air progress % curl -v https://www.pokemon.co.jp/
* Host www.pokemon.co.jp:443 was resolved.
* IPv6: (none)
* IPv4: 18.65.168.61, 18.65.168.101, 18.65.168.128, 18.65.168.91
*   Trying 18.65.168.61:443...
* Connected to www.pokemon.co.jp (18.65.168.61) port 443
* ALPN: curl offers h2,http/1.1
* (304) (OUT), TLS handshake, Client hello (1):
*  CAfile: /etc/ssl/cert.pem
*  CApath: none
* (304) (IN), TLS handshake, Server hello (2):
* (304) (IN), TLS handshake, Unknown (8):
* (304) (IN), TLS handshake, Certificate (11):
* (304) (IN), TLS handshake, CERT verify (15):
* (304) (IN), TLS handshake, Finished (20):
* (304) (OUT), TLS handshake, Finished (20):
* SSL connection using TLSv1.3 / AEAD-AES128-GCM-SHA256 / [blank] / UNDEF
* ALPN: server accepted h2
* Server certificate:
*  subject: CN=pokemon.co.jp
*  start date: Jun 12 00:00:00 2026 GMT
*  expire date: Dec 26 23:59:59 2026 GMT
*  subjectAltName: host "www.pokemon.co.jp" matched cert's "*.pokemon.co.jp"
*  issuer: C=US; O=Amazon; CN=Amazon RSA 2048 M01
*  SSL certificate verify ok.
* using HTTP/2
* [HTTP/2] [1] OPENED stream for https://www.pokemon.co.jp/
* [HTTP/2] [1] [:method: GET]
* [HTTP/2] [1] [:scheme: https]
* [HTTP/2] [1] [:authority: www.pokemon.co.jp]
* [HTTP/2] [1] [:path: /]
* [HTTP/2] [1] [user-agent: curl/8.7.1]
* [HTTP/2] [1] [accept: */*]
> GET / HTTP/2
> Host: www.pokemon.co.jp
> User-Agent: curl/8.7.1
> Accept: */*
>
* Request completely sent off
< HTTP/2 200
< content-type: text/html; charset=UTF-8
< date: Fri, 24 Jul 2026 05:07:23 GMT
< strict-transport-security: max-age=31536000; includeSubDomains; preload
< x-content-type-options: nosniff
< server: Apache
< x-cache: Hit from cloudfront
< via: 1.1 a0c8ca5c55854408aacaabfb864516d0.cloudfront.net (CloudFront)
< x-amz-cf-pop: NRT57-P1
< x-amz-cf-id: stynVAgnn9bWjLQaS9fHmfDU4AjzOZzJcrpQ4bZO471Lhvn6Z91dzA==
< age: 171510
<
<!DOCTYPE html>
〜　省略〜
</body></html>% 
```
5. 観察：
#### 接続、TLS情報「*」について
- `ALPN: curl offers h2,http/1.1`：TLS接続時に「この後どのアプリケーションプロトコルを使うか」を決める仕組み。クライアントが`h2(HTTP/2)`もしくは`http/1.1(HTTP/1.1)`をサーバに提案している。サーバがその中からプロトコルを選択する。
- TLSハンドシェイク：暗号化方式を決め、証明書を確認し、通信に使う鍵を安全に共有する一連の手続きです。
```
* (304) (OUT), TLS handshake, Client hello (1): // curlからサーバにClientHelloを送信
*  CAfile: /etc/ssl/cert.pem // クライアント側にある信頼する認証局（CA）の一覧が記載されているファイル（複数のCA証明書をまとめたファイル）
*  CApath: none
* (304) (IN), TLS handshake, Server hello (2):　// サーバからServerhelloを受信
* (304) (IN), TLS handshake, Unknown (8): // `EncryptedExtensions`で拡張情報を受信
* (304) (IN), TLS handshake, Certificate (11): // サーバから証明書を受信
* (304) (IN), TLS handshake, CERT verify (15):　// サーバーからCertificateVerifyメッセージを受信
* (304) (IN), TLS handshake, Finished (20): // サーバーからFinished`メッセージを受信
* (304) (OUT), TLS handshake, Finished (20):　// クライアント側のFinishedメッセージをサーバに送信
```
- TLS完了後
```
* SSL connection using TLSv1.3 / AEAD-AES128-GCM-SHA256 / [blank] / UNDEF // TLS接続が確立し、使用する暗号方式が決まったことを示す。
* ALPN: server accepted h2 // サーバがh2(HTTP/2)を選択
* Server certificate: // 以下にサーバが提示した証明書の概要が始まる
*  subject: CN=pokemon.co.jp
*  start date: Jun 12 00:00:00 2026 GMT // 証明書が有効になる日時
*  expire date: Dec 26 23:59:59 2026 GMT // 証明書の有効期限
*  subjectAltName: host "www.pokemon.co.jp" matched cert's "*.pokemon.co.jp" // URLのホスト名と、証明書のSANが一致したことを示す。
*  issuer: C=US; O=Amazon; CN=Amazon RSA 2048 M01 // サーバー証明書を発行・署名した認証局の情報
*  SSL certificate verify ok. // 証明書検証完了
```
- curlがHTTP/2でGETリクエストを送信する準備
```
* using HTTP/2 // TLS確立後、HTTP/2を使用して通信を行うことを示す
* [HTTP/2] [1] OPENED stream for https://www.pokemon.co.jp/　// GETリクエストを処理するためのストリームを開いたことを示す
* [HTTP/2] [1] [:method: GET]　// ストリーム１でGETのHTTPメソッドを送信する
* [HTTP/2] [1] [:scheme: https] // リクエストURLのスキームがhttpsであることを示す
* [HTTP/2] [1] [:authority: www.pokemon.co.jp] // リクエストの送信対象となるホストを示す
* [HTTP/2] [1] [:path: /] // サーバ内のどのリソースを要求するのかを示す
* [HTTP/2] [1] [user-agent: curl/8.7.1] // リクエストを送信するクライアントがcurl8.7.1であることを示す
* [HTTP/2] [1] [accept: */*] // curlがどのメディアタイムのレスポンスでも受け入れることを示す
```
- メディアタイプ：`text/html`, `aplication/json`, `image/png`, `text/css`など
#### リクエスト「>」について
```
--- リクエストの基本情報
> GET / HTTP/2
--- リクエストヘッダ
> Host: www.pokemon.co.jp
> User-Agent: curl/8.7.1
> Accept: */*
--- リクエストボディ 
>
```
#### レスポンス「<」について
```
--- ステータス
< HTTP/2 200 # 使用しているHTTPバージョン, HTTPステータスコード
--- レスポンスヘッダー
< content-type: text/html; charset=UTF-8 # レスポンスボディのデータ形式と文字コード
< date: Fri, 24 Jul 2026 05:07:23 GMT # レスポンスが生成された日時
< strict-transport-security: max-age=31536000; includeSubDomains; preload # サイトへHTTPではなく、HTTPSで接続するように指示
< x-content-type-options: nosniff　# ブラウザによるMIMEタイプの推測を禁止するセキュリティヘッダー
< server: Apache  # レスポンスを処理したサーバーソフトウェアの情報
< x-cache: Hit from cloudfront # CloudFrontのキャッシュからレスポンスが返されたことを示す
< via: 1.1 a0c8ca5c55854408aacaabfb864516d0.cloudfront.net (CloudFront)　# レスポンスが途中で経由した中継サーバーの情報
< x-amz-cf-pop: NRT57-P1 # リクエストを処理したCloudFrontのエッジロケーションを識別する情報
< x-amz-cf-id: stynVAgnn9bWjLQaS9fHmfDU4AjzOZzJcrpQ4bZO471Lhvn6Z91dzA== # CloudFrontがリクエストへ割り当てた識別子
< age: 171510 # レスポンスが共有キャッシュに保存されてからの、おおよその経過時間
--- 空行
<
--- レスポンスボディ
<!DOCTYPE html>
〜　省略〜
</body></html>% 
```
---
#### `curl -i`でレスポンスヘッダーを確認する
1. やったこと：`curl -i`でGETリクエストを送信し、レスポンスヘッダを見る。
2. コマンド：`curl -i [URL]`
3. 対象URL：`https://www.pokemon.co.jp/`
4. 結果：
```
HTTP/2 200
content-type: text/html; charset=UTF-8
date: Fri, 24 Jul 2026 05:07:23 GMT
strict-transport-security: max-age=31536000; includeSubDomains; preload
x-content-type-options: nosniff
server: Apache
x-cache: Hit from cloudfront
via: 1.1 6b3df82b11020ffd9f07adedfc60be70.cloudfront.net (CloudFront)
x-amz-cf-pop: NRT57-P1
x-amz-cf-id: VvmOIdlakIl0eL85R30r5WkdW--28JdWELZcIGG_h5gtkh1WfJNvkg==
age: 293097

<!DOCTYPE html>
<html lang="ja">
〜省略〜
</body></html>% 
```
5. 観察：
- 「`curl -v`でGETリクエストを送る」でみたところのレスポンスとほとんど同じ。
- `x-amz-cf-pop: NRT57-P1`（リクエスト処理をしたCloudFrontのエッジロケーション）は、前の課題と同じ。 -> リクエストを送る場所によるので、変わらないのではないか？
- レスポンスが中継したサーバー情報（`via`）は、変わっている。　-> これはcloudfrontは変わっているが、中継サーバは変わっていない。
- `age`（レスポンスがキャッシュに保存されてからの経過時間）は、前の課題に比べて増えた（171510 -> 293097）
6. 疑問
- そもそも、cloudfrontは中継サーバではない？キャッシュサーバ？
- cloudfrontとレスポンスの中継サーバは違う？
- `age`：これは同一のクライアントごとに管理しているのではなく、同じリクエストに対して同じレスポンスを返す場合にキャッシュを活用している？
## 反省
- 公開鍵と秘密鍵とは何かの理解が乏しい。
---
### 2026/7/31
### やったこと
1. NetWorkタブでリクエストを観察
2. クエリ付きGETを観察
3. GETとPOST（リクエストおよびレスポンス）を比較
### 学んだこと
#### NetWorkタブでリクエストを観察
1. やったこと
2. 対象URL：`https://www.pokemon.co.jp/`
3. ブラウザ：Google Chrome
4. 見られるもの①
- 一覧：`Name` / `Status`  / `Type` / `Initiator` / `Size` / `Time`　カラムがある。
  - `Name`：リクエストのファイル名またはパスの末尾
  - `Status` ：HTTPステータスコード
  - `Type`：リソースの種類
    - `document`：ページ本体となるHTML文書
    - `stylesheet`：CSSスタイルシートとして読み込まれたリソース
    - `script` ：JavaScriptとして読み込まれたリソース
    - `fetch`：JavaScriptのFetch APIによって発生したリクエスト
    - `xhr`：`XMLHttpRequest`によって発生したリクエスト
    - `png`：レスポンスがPNG画像として扱われた
    - `font`：Webフォントとして読み込まれたリソー
    - `ping`：ページ表示の中心的な処理とは別に、アクセス解析や利用状況の通知などを送る通信
  - `Initiator`：その通信を発生させた場所。スクリプト名と行番号がリンクになっていて、クリックするとSourcesパネルの該当箇所に飛べる。
  - `Size`：上段が転送量（ヘッダー込み、圧縮後）、下段が展開後の実サイズ。
    - `memory cache`：リソースをネットワークから取得せず、メモリ上のブラウザキャッシュから読み込んだことを示す。
      - ブラウザのメモリ -> リソースを再利用　-> ネットワーク通信を省略
    - `disk cache`：リソースをネットワークから取得せず、ディスク上のブラウザキャッシュから読み込んだことを示す。
  - `Time`：リクエスト開始からレスポンス受信完了までの所要時間。
5. 疑問
- `memory cache`は、PC上のメモリ上に保存されているものか？　-> Yes
- `disk cache`は、PCのHDDまたはSSDに保存されているものか？　-> Yes
- 上記のようなキャッシュ機能はブラウザが持つものか？それともアプリ側の機能か？ -> ブラウザの機能
  - ブラウザ機能：
    - キャッシュをメモリやディスクへ保存する。
    - 保存済みのキャッシュを探す。
    - memoryかdiskどちらを使うのかを決める。
    - 不要なキャッシュを削除する。
  - アプリ機能（アプリ開発者が制御できる機能）：
    - キャッシュして良い期間を決める。
    - 機密情報をキャッシュさせないように指示する。
    - 更新確認用の情報を返す。
- リクエスト詳細：カラム`name`のリクエストをクリックするとリクエストおよびレスポンスの詳細を表示することができる。以下のカラムがある。
  - `Header`：URL、メソッド、ステータス、送信ヘッダの情報が記載。
    - `General`：
      - `Request URL`：https://www.pokemon.co.jp/
      - `Request Method`：GET
      - `Status Code`：Status Code
      - `Remote Address`：18.65.168.61:443
      - `Referrer Policy`：strict-origin-when-cross-origin
  - `Preview`：ブラウザが解釈したレスポンス
    - 観察結果：CSSが当たっていないように見えた。レイアウトが崩れている。
  - `Response`：レスポンスボディ（実際に返されたJSONやHTML）
    - `Queued at ⚪︎μs`：ブラウザがリクエストを発生させ、処理待ちに入れた時刻（相対時刻）
      - 基準時刻から⚪︎秒後にリクエストがキューへ入った。
    - `Started at ⚪︎ms`：リクエストのネットワーク処理を開始した時刻（相対時刻）
      - ブラウザのネットワーク処理が開始された時間
    - `Resource Scheduling`：ブラウザ内でのリクエストのスケジューリングに関する時刻
      - `Queueing`：リクエストが開始可能になるまでブラウザ内部でまった時間（所要時間）
        - `Queueing時間　≒ Start at - Queued at` -> ブラウザがリクエストを処理せず、開始可能になるまで待った時間。主な原因は以下。
          - より優先度の高いリクエストが存在する。
          - HTTP/1.1で同一オリジンへの接続数が上限に達している。
            - オリジン：スキーム + ホスト + ポート
            - `https://api.example.com/users` , `https://api.example.com/products` , `https://api.example.com/orders`は全て同じオリジン
            - 上記は全て、以下の組み合わせ
              - スキーム：`http`
              - ホスト：`api.example.com`
              - ポート：`443`
            - `http://api.example.com`, `https://www.example.com`, `https://api.example.com:8443`は全て違うオリジン
          - ディスクキャッシュ用の領域を確保している。
          - 多数の画像やJavaScriptなどが同時にリクエストされている。
    - `Connection Start`：接続の準備に関する時間
      - `Stalled`：接続や送信処理を実際に進められるまでに待った時間（所要時間）
      - `DNS Lookup`：ドメイン名からIPアドレスを調べる期間
      - `Initial connection`：TCP接続を確立する時間
      - `SSL`：TLS接続を確立する時間
      - `Proxy negotiation`：プロキシサーバーと接続を調整する時間
      - すでに確立済みの接続を再利用した場合、DNS、TCP、TLSは表示されないことがある。
    - `Request/Response`：HTTPリクエストの送信からレスポンス受信までの時間
      - `Request sent`：リクエストヘッダーやリクエストボディを送信した時間（所要時間）送信対象は以下。
        - HTTPメソッドやURL
        - リクエストヘッダ
        - POSTやPUTなどのリクエストボディ
      - `Waiting for server response（TTFB）`：送信完了からレスポンスの最初の１バイトを受け取るまでの時間（所要時間）
        - リクエスト送信完了　-> サーバーまで届く　-> サーバーが処理する -> レスポンスの最初の1バイトが届く
        - 以上、全体にかかった時間なので、主に以下の両方が含まれる。
          - ネットワークの往復時間
          - GoアプリケーションやDBなどのサーバー側の処理時間
      - `Content Download`：レスポンスの最初の１バイトを受け取ってから、レスポンスボディを最後まで受信した時間
        - 長くなる主な原因は以下の通り。
          - JSON、HTML、画像などのレスポンスサイズが大きい。
          - ネットワークが遅い。
          - ブラウザが他の処理で忙しい。
    - `Explanation`：各タイミングの公式説明を開くリンク
  - `Initiator`：通信を発生させた場所と経路（どのJavaScriptがAPIを読んだか）
  - `Timing`：通信内容の内訳（DNS、TLS、TTFBのどこが遅いかを調べる）
  - `Cookies`：送信Cookieと受信Cookie（セッションCookieやブロック理由を調査する）
    - `Name`：Cookieの名前
    - `Value`：Cookieに保存されてる値
    - `Domain`：Cookieを送信できるホストの範囲
    - `Path`：Cookieを送信するURLパスの範囲
    - `Expires / Max-Age`：Cookieの有効期限
    - `Size`：Cookieのサイズ（バイト）
    - `Http Only`：JavaScriptから読み書きできないか
    - `Secure`：　HTTPS通信だけで送信するか
    - `SameSite`：クロスサイト通信での送信制限（`Strict`、`Lax`、`None`）
    - `Partition Key Site`：Partitioned Cookieを分離するトップレベルサイト
    - `Cross Site`：パーティションキーにクロスサイト祖先が含まれるか（`true`、`false`）
    - `Priority`：Cookie削除時の保持優先度（`Low`、`Medium`、`High`）
6. アプリ開発者（インフラ含む）がよく参照するプロパティ
- `Status`および`Waiting for server response(TTFB)`
- 実務上の優先順位：
  - 1. `Status`：成功、認証エラー、サーバーエラーなどを確認できる。
    - `200`：成功
    - `400`：リクエスト内容が不正
    - `401`：認証されていない
    - `403`：権限がない
      - そのリソースを閲覧する権限がない。
      - 一般ユーザが管理画面へアクセスした。
      - 他人のデータを閲覧・更新・削除しようとした。
      - 所属していない組織のデータへアクセスした。
      - 契約プランで許可されていない機能を使用した。
      - IPアドレスやセキュリティポリシーで拒否された。
    - `404`：URLやリソースが存在しない（Not Found）
    - `429`：レート制限 -> `レート = リクエスト数 ÷ 時間`：一定時間あたりの回数
      - 1ユーザにつき、1分間に100回
      - 1つのIPアドレスにつき、1秒間に10回
      - 1つのAPIキーにつき、1日1000回
    - `500`：アプリケーション内部エラー（Internal Server Error）-> 　通常は、HTTPリクエストを処理したサーバー側で発生した予期しないエラー。
      - Goでpanicが発生した。
      - DB処理で予期しないエラーが発生した。
      - 必要な設定値が存在しなかった。
      - 外部APIのエラーを適切に処理できなかった。
      - ただし、500を返したサーバーがバックエンドなのかリバースプロキシなのかはレスポンスと各サーバーのログから判断する。
    - `502`：プロキシやロードバランサから接続した先で問題　-> 主に、アプリケーションサーバー（DBサーバへ直接接続するケースは通常ない。）
      - 例えば、ロードバランサがGoアプリへ接続したものの、正常なHTTPレスポンスが受け取れなかった場合に502となる。
        - Goアプリが異常終了した。
        - 接続が途中で切れた。
        - 不正なHTTPレスポンスが返された。
        - 接続先やポート設定が間違っている。
    - `503`：サービス停止中、過負荷、正常な接続先がない 
    - `504`：プロキシやロードバランサでタイムアウト
  - 2. `Time/Waterfall`：どのリクエストが全体を遅くしているか。
    - `Time`：リクエスト開始からレスポンス受信完了までの合計時間
    - `Waterfall`：複数のリクエストを比較できる
      - 特定のAPIだけ遅い
      - 全てのAPIが遅い
      - 同じAPIを重複して呼んでいる
      - 前のリクエストの完了待ちになっている
      - ページ読み込み直後にリクエストが集中している
      - 「どのリクエストから詳しく調べるべきか」を決めるために使用する。
  - 3. `Wating（TTFB）`：サーバー側、インフラ側が遅い可能性（CDN、ロードバランサ、プロキシの処理 / Goアプリの処理 / DB、キャッシュ、外部APIの処理 /ネットワーク復路の一部）。
    - TTFBが長い場合にアプリ側で見るもの
      - 1. Goのハンドラー処理時間
      - 2. SQLの実行時間
      - 3. DB接続プールの待ち時間 
        - 「DB接続プール」とは、DB接続を毎回作り直さず、複数の接続を保持して再利用する仕組み。
        - SQL実行時には、プールから空いている接続をかり、処理が終わるとプールへ返す。
        - 全ての接続が使用中の場合、新しいDB処理は空きが出るまで待つ。 -> これによってTTFBが長くなる場合がある。
        - DB接続プールは、アプリケーションサーバとDBサーバ間の接続。（ブラウザ <-> Goアプリケーション <-> DBサーバ , ブラウザGoアプリケーション間：HTTP通信 , GoアプリケーションDBサーバ間：DB接続）
        - HTTP接続が6本の場合、DB接続も6本か？　-> No
          - 静的ファイルを返す場合 -> DB接続0本
          - キャッシュだけで応答する　-> DB接続0本
          - 6HTTPリクエストが順番にDBを使う　-> DB接続1本でも処理可能
          - 6HTTPリクエストが同時にDBを使う -> 最大6本を使う可能性
          - DBプールの上限が3本 -> 3本を使い、残りは空きを待つ
          - 他のユーザやバックグラウンド処理も存在　-> 6本より多い可能性
        - DBとの間にロードバランサは必要か？
          - 小規模な構成では、GoアプリからDBへ直接接続することが一般的
          - 規模が大きくなると、DBプロキシや外部接続プーラーを挟むことがある。
        - 中間コンポーネントの役割
          - 多数のアプリ接続を少数のDB接続へまとめる
          - DB接続を再利用する
          - DBへの接続数を制限する
          - 障害時に接続先DBを切り替える
          - 読み取り用DBと書き込み用DBを振り分ける
        - **接続プール：接続を保存・再利用する**
        - **ロードバランサ：複数の接続先へ処理を振り分ける**
        - **RDS Proxy：接続プール、接続の集約、障害時の切り替え**
        - **DB接続プールは、Goアプリケーションで制御**する。
          - Goアプリ側：DB接続を何本作るか制御
          - DBサーバ側：全クライアントから最大何本受け入れるか制御
      - 4. 外部APIの呼び出し時間
      - 5. ロックやgoroutineの詰まり
      - 6. CPU使用率
      - 7. メモリ使用量やGC
      - 8. キャッシュヒット率 = ヒット数 ÷ (ヒット数 + ミス数)　×　100
        - 100回確認して80回のキャッシュから取得できた場合、ヒット率は80%
        - アプリ側で見るキャッシュ率は主に次のキャッシュを示す。
          - Goアプリ内のメモリキャッシュ
          - Redisなどのキャッシュサーバー
          - CDNのキャッシュ
          - DB内部のキャッシュ
      - 9. リクエスト数と同時実行数
    - インフラ側で見るもの
      - 1. ロードバランサーの応答時間
      - 2. リバースプロキシの待ち時間
      - 3. コンテナやVMのCPU、メモリ
      - 4. Podやインスタンス数
        - `Pod`：Kuberbetesをアプリで動かす最小単位。1つのPodの中で１つのアプリケーションコンテナを動かす。
        - インスタンス：稼働しているサーバーやアプリの実体、コピー。クラウドでは、EC2のような仮想サーバーのこと。
      - 5. オートスケーリングの動作
        - オートスケーリング：負荷に応じて、アプリケーションを動かす数や性能を自動調整する機能。
          - 水平スケーリング：Podやサーバーの数を増減する
          - 垂直スケーリング：CPUやメモリを増減する
      - 6. 再起動やヘルスチェック失敗
      - 7. CDNのキャッシュヒット数
      - 8. DBや外部サービスへのネットワーク遅延
  - 4. `Size/Content Download`：レスポンスが大きすぎないか、圧縮されているか。
    - TTFBが短いのに、全体のTimeが長い場合はここを見る。確認する内容は以下。
      - 1. JSONに不要なデータが含まれれていないか
      - 2. 大量の一覧を一度に返していないか
      - 3. ページネーションが必要でないか
        - ページネーション：大量のデータを一度に返さず、複数ページに分割して返す仕組み。
        - 例えば、ユーザが1万件存在しても、最初のリクエストでは50件だけ返す。
        - ページネーションの利点：
          - レスポンスサイズを小さくできる
          - DBやGoアプリの負荷をへらせる
          - Content Downloadを短縮できる
          - クライアントのメモリ消費を減らせる
      - 4. gzipやBrotliで圧縮されているか
      - 5. CNDを利用できないか
      - 以下、レスポンスヘッダで確認。
      - 6. `Content-Length`
      - 7. `Content-Encoding`
      - 8. `Content-Type`
      - 9. `Cache-Control` 
  - 5. `DNS Lookup / Initial connection / SSL`：DNS、TCP、TLS、ロードバランサ周辺の問題。
    - これらが長い場合は、HTTP Keep-Alive、HTTP/2、HTTP/3による接続再利用も確認対象になる。
  - 6. `Queueing / Stalled`：ブラウザ側でリクエストが待たされていないか。
    - これらが長い場合は、最初からアプリを疑うべきではない。
    - 主に次の原因がある。
      - 1. ブラウザが複数のリクエストを同時に処理している
      - 2. 優先度の高いリクエストを先に処理している
      - 3. HTTP/1.1の同時接続制限数(ブラウザ側の制限)
        - HTTP/1.0およびHTTP/1.1は6本のTCP接続が開かれていると、追加のリクエストが待機することがある。
      - 4. 利用可能な接続を待っている
      - 5. キャッシュ関連の準備をしている
  - 7. `Request sent`：アップロードや大きなリクエストボディの問題。
    - 長い場合は、以下の可能性がある。
      - 1. 大きなファイルをアップロードしている。
      - 2. 巨大なJSONを送信している。
      - 3. クライアントの上り回線が遅い。
      - 4. リバースプロキシがリクエストボディを読み終えるまで待っている。
      - これらはGETメソッドではあまり問題にならない。
      - POSTやファイルアップロードAPIでは重要な値となる。
- 注意：**DevToolsだけでは原因を確定できない**
  - DevToolsは、「**どの区間が怪しいか**」を見つけるための入り口。
  - **アプリとインフラを切り分けるには、同じリクエストをサーバー側の情報を結びつける。**
  - レスピンスヘッダにIDをつけると調査しやすくなる。
    - `X-Request-ID: 8e1cf8d2-...`
    - `Traceparent: 00-...`
- **`Server-Timing`を利用する**
  - レスポンスヘッダに以下のようなヘッダーを追加する。
```go
w.Header().Set(
	"Server-Timing",
	"app;dur=82, db;dur=37, external-api;dur=20",
)
```
  - 以上により、`Timing`タブで次のような内訳を確認できる。
```
Application     82 ms
Database        37 ms
External API    20 ms
```
  - `Server-Timing`は、サーバーの処理時間をブラウザへ伝えるための標準仕様。
7. 疑問（その２）
### 反省
- リクエスト単位とAPI単位を区別する必要がある。
- 1つのポートには複数のTCP接続を確立することができる。
- ポートは、データの通り道ではなく、OSが「どのアプリケーション宛ての通信か」を判断するための識別番号
