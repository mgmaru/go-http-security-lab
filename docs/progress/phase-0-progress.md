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
#### 1. `curl -v`でGETリクエストを送る
- やったこと：URLにGETリクエストを送る。
- コマンド：`curl -v [URL]` -> `-v`は`vervose`（詳細情報）の略であり、以下の情報がえられる。
  - `*`：curlによる接続・TLSなどの情報
  - `>`：curlが送信したHTTPリクエスト
  - `<`：サーバーから受信したHTTPレスポンス
- 対象URL：`https://www.pokemon.co.jp/`
- 結果
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
- 観察：
2. `curl -i`でレスポンスヘッダーを確認する
- やったこと：
- コマンド：
- 対象URL：
- 結果：
- 観察：
## 反省
---
