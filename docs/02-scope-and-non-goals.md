# 対象範囲と非目標

## 作成するもの

複数ユーザーに対応したタスク管理Web APIを作ります。

### 主な機能

- ユーザー登録
- ログイン
- ログアウト
- セッション管理
- タスクの作成・取得・更新・削除
- ユーザーごとのデータ分離
- 管理者機能
- プロフィール画像アップロード
- 操作ログ
- 同時更新制御
- セキュリティテスト
- 最小限のWebフロントエンド

### 想定API

```http
GET    /health
GET    /hello
POST   /echo

POST   /api/users
POST   /api/login
POST   /api/logout
GET    /api/me

GET    /api/tasks
GET    /api/tasks/{id}
POST   /api/tasks
PUT    /api/tasks/{id}
PATCH  /api/tasks/{id}
DELETE /api/tasks/{id}

GET    /api/admin/users
GET    /api/admin/tasks
PATCH  /api/admin/users/{id}/role
DELETE /api/admin/users/{id}

POST   /api/profile/image
```

---

## 初期の学習範囲で扱わないもの

次は永久に禁止するのではなく、本体の学習目的を守るために、初期範囲から外します。

- マイクロサービス化
- Kubernetes
- 大規模なクラウドインフラ
- 一般ユーザー向けの本番公開
- 高度なフロントエンドデザイン
- スマートフォンアプリ
- 独自の暗号アルゴリズム
- 独自のパスワードハッシュ
- 独自のTLS実装
- OAuthや外部IDプロバイダー
- 大量アクセスを前提とした分散システム
- OWASPの全項目を一度に網羅すること
- 複数のWebフレームワークの同時比較
- 実際の個人情報や本物の認証情報を扱うこと

## 発展課題を追加する条件

次を満たした場合のみ、対象外の機能を発展課題として検討します。

1. 現在のコースの完了条件を満たしている
2. 追加する目的を自分の言葉で説明できる
3. 学習期間への影響を見積もった
4. 現在の設計を壊す範囲を把握した
5. 「面白そうだから」以外の必要性がある

追加した課題は、ロードマップ本体へ混ぜず、別の発展課題として管理します。
