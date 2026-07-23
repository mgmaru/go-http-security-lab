# go-http-security-lab

GoでWeb APIを実装しながら、HTTP、設計、テスト、認証・認可、Webセキュリティを実践的に学ぶためのリポジトリです。

完成したAPIだけでなく、設計判断、失敗、迷い、未解決事項、AIとの相談内容も学習成果として残します。

## クイックスタート

### 現在地

- 現在のPhase：`[記入]`
- 状態：`未着手 / 進行中 / 保留 / 完了`
- 次に行うこと：`[記入]`
- 今週使える学習時間：`[記入]`

開始時は、[前提環境](docs/01-prerequisites.md)を確認した後、[ロードマップ](docs/10-roadmap.md)のPhase 0〜1から進めます。

### 最初の手順

1. Go、Git、`curl`を利用できることを確認する
2. `docs/design/`に最初の設計メモを作る
3. Phase 0でHTTP通信を観察する
4. Phase 1の`GET /health`から実装する
5. `curl`と`httptest`で確認する
6. Phase終了時に振り返りと進捗を記録する

## 学習の基本原則

```text
自分で要求を整理する
  ↓
自分で設計・実装する
  ↓
curl・Bruno・httptest・Burp Suiteで検証する
  ↓
疑問を自分の言葉にする
  ↓
必要に応じてAIへレビューを求める
  ↓
最終判断を自分で行う
  ↓
学び・迷い・未解決事項を記録する
```

- 学習対象となる設計・実装は、最初に自分で考えます。
- AIは最初の設計者ではなく、後から参加するレビュアーとして使います。
- `curl`でHTTPを理解した後、Phase 2からBrunoを利用します。
- 暗号やパスワードハッシュなどは独自実装しません。
- 完璧主義で同じPhaseに留まらず、未解決事項を記録して進みます。
- 脆弱な状態のアプリをインターネットへ公開しません。

## 全体ロードマップ

| 区分 | Phase | 内容 |
|---|---:|---|
| HTTP基礎 | 0〜3 | HTTP観察、基本サーバー、CRUD、ミドルウェア |
| バックエンド | 4〜8 | SQLite、認証、認可、管理者、ファイル |
| セキュリティ | 9〜10 | Burp Suite、セキュリティテスト化 |
| ブラウザ・運用 | 11〜14 | フロントエンド、CORS・CSRF・XSS、競合制御、CI |

詳細な課題と完了条件は[ロードマップ](docs/10-roadmap.md)を参照してください。

## ドキュメント一覧

| 文書 | 内容 |
|---|---|
| [プロジェクト概要](docs/00-overview.md) | 目的、到達目標、アプリ概要 |
| [前提環境](docs/01-prerequisites.md) | 必要なツールと導入時期 |
| [対象範囲と非目標](docs/02-scope-and-non-goals.md) | 作るもの、今回は扱わないもの |
| [学習方針](docs/03-learning-policy.md) | コーディング中心、振り返り、注意事項、期間 |
| [設計方針](docs/04-design-policy.md) | 設計メモ、ADR、設計完了条件 |
| [ライブラリ方針](docs/05-library-policy.md) | 標準ライブラリと外部依存の使い分け |
| [APIクライアント方針](docs/06-api-client-policy.md) | `curl`、Bruno、`httptest`、Burp Suite |
| [AI利用ルール](docs/07-ai-policy.md) | AIへ相談する順序と記録方法 |
| [テスト戦略](docs/08-testing-policy.md) | テスト階層、役割、重複を避ける方針 |
| [セキュリティ方針](docs/09-security-policy.md) | 安全な検証範囲と秘密情報管理 |
| [ロードマップ](docs/10-roadmap.md) | Phase 0〜14の課題と完了条件 |
| [開発ワークフロー](docs/11-workflow.md) | 各Phaseの進め方、Git、PR、コミット |
| [最終成果物](docs/12-deliverables.md) | 完成時に揃える成果物 |
| [学習資料](docs/13-resources.md) | 公式資料・学習サイト |
| [大切にする問い](docs/14-guiding-questions.md) | 設計・実装・AI利用の確認項目 |

## 記録用ドキュメント

- [Phase進捗](docs/progress/README.md)
- [振り返り](docs/retrospectives/README.md)
- [未解決事項](docs/questions/unresolved-questions.md)
- [AI相談記録](docs/ai/consultations/README.md)
- [ADR](docs/adr/README.md)
- [テンプレート一覧](templates/README.md)

## 取り組み期間の目安

週10〜15時間の場合、全Phaseは約4〜6か月を目安とします。

- 基礎コース：Phase 0〜3、3〜6週間
- バックエンド・セキュリティコース：Phase 0〜10、3〜4か月
- 完全コース：Phase 0〜14、4〜6か月以上

期間は理解を飛ばすための期限ではなく、計画を調整するための目安です。

## 最初の到達点

まずは次を完成させます。

```http
GET  /health
GET  /hello?name=...
POST /echo
```

実装方法はREADMEに記載しません。要求、設計、コード、テストは自分で考え、完了後に必要なレビューを受けます。

## 重要な注意

このリポジトリでは、学習のために意図的または偶発的な脆弱性が含まれる可能性があります。

- localhostまたは自分が管理する隔離環境で実行する
- 許可のない対象へBurp Suiteや攻撃手法を使用しない
- 本物のパスワード、APIキー、個人情報を使用しない
- 脆弱な状態をインターネットへ公開しない

詳細は[セキュリティ方針](docs/09-security-policy.md)を確認してください。
