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
2. `curl -i`でレスポンスヘッダーを確認する
- やったこと：
- コマンド：
- 対象URL：
- 結果：
- 観察：
### その他
## 反省
- 公開鍵と秘密鍵とは何かの理解が乏しい。
---
