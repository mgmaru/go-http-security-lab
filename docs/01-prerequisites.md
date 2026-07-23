# 前提環境

この文書では、プロジェクトを開始するために必要な環境と、各ツールを導入する時期を整理します。

## 必須

| ツール | 用途 | 使用開始 |
|---|---|---:|
| Go | API実装、テスト | Phase 1 |
| Git | バージョン管理 | Phase 0 |
| `curl` | HTTP通信の確認 | Phase 0 |
| Webブラウザ | Networkタブ、ブラウザセキュリティ | Phase 0 |
| テキストエディターまたはIDE | コーディング | Phase 0 |

## Phaseが進んだら必要になるもの

| ツール | 用途 | 使用開始 |
|---|---|---:|
| SQLite | 永続化、SQL、トランザクション | Phase 4 |
| Bruno | APIリクエストの保存・整理 | Phase 2 |
| Burp Suite Community Edition | HTTP通信の改変・手動検査 | Phase 9 |
| GitHub Actions | CI | Phase 14 |
| `staticcheck` | 静的解析 | Phase 14 |
| `govulncheck` | Go依存関係の脆弱性検査 | Phase 14 |

## 任意

- Docker
- Make
- API仕様閲覧ツール
- DB GUIクライアント
- VS Code、Zedなどの任意のエディター

任意ツールは、現在の問題を解決する必要が生じてから導入します。

## バージョンの記録

プロジェクト開始時に、実際に動作確認したバージョンを記録します。

```text
Go：
Git：
SQLite：
Bruno：
Burp Suite：
OS：
```

Goの最低バージョンは`go.mod`を基準にします。

## 環境確認

```bash
go version
git --version
curl --version
```

SQLite、Bruno、Burp Suiteは使用するPhaseに到達した時点で確認します。

## 環境構築でAIへ聞いてよいこと

環境構築やツール操作は、このプロジェクトの中心的な設計・実装課題ではないため、早い段階からAIへ相談して構いません。

ただし、AIが示したコマンドの意味、変更対象、影響範囲を確認してから実行します。
