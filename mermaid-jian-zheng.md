# mermaid検証

## Mermaid 記法検証用サンプル

このドキュメントは、各ドキュメント管理ツールの Mermaid 記法対応状況を検証するためのサンプル図表集です。

### 1. フローチャート（Flowchart）

#### 1.1 基本的なフローチャート

```mermaid
graph TD
    A[開始] --> B{条件分岐}
    B -->|Yes| C[処理A]
    B -->|No| D[処理B]
    C --> E[終了]
    D --> E
```

#### 1.2 複雑なワークフロー

```mermaid
graph TB
    Start([開始]) --> Input[ユーザー入力]
    Input --> Validation{入力値検証}
    Validation -->|有効| Auth[認証チェック]
    Validation -->|無効| ErrorMsg[エラーメッセージ表示]
    ErrorMsg --> Input

    Auth --> AuthCheck{認証OK?}
    AuthCheck -->|Yes| Permission[権限チェック]
    AuthCheck -->|No| LoginForm[ログインフォーム]
    LoginForm --> Auth

    Permission --> PermCheck{権限OK?}
    PermCheck -->|Yes| ProcessData[データ処理]
    PermCheck -->|No| AccessDenied[アクセス拒否]

    ProcessData --> SaveDB[(データベース保存)]
    SaveDB --> Success[成功メッセージ]
    Success --> End([終了])
    AccessDenied --> End
```

### 2. シーケンス図（Sequence Diagram）

#### 2.1 基本的なシーケンス図

```mermaid
sequenceDiagram
    participant User as ユーザー
    participant Frontend as フロントエンド
    participant API as APIサーバー
    participant DB as データベース

    User->>Frontend: ログイン要求
    Frontend->>API: 認証リクエスト
    API->>DB: ユーザー情報照会
    DB-->>API: ユーザー情報返却
    API-->>Frontend: 認証トークン
    Frontend-->>User: ログイン成功
```

#### 2.2 エラーハンドリングを含むシーケンス図

```mermaid
sequenceDiagram
    participant C as クライアント
    participant LB as ロードバランサー
    participant App as アプリケーション
    participant Cache as Redis
    participant DB as PostgreSQL

    C->>LB: APIリクエスト
    LB->>App: リクエスト転送

    App->>Cache: キャッシュ確認
    alt キャッシュヒット
        Cache-->>App: キャッシュデータ返却
        App-->>LB: レスポンス
    else キャッシュミス
        App->>DB: データ取得
        alt DB正常
            DB-->>App: データ返却
            App->>Cache: キャッシュ更新
            App-->>LB: レスポンス
        else DBエラー
            DB-->>App: エラー
            App-->>LB: エラーレスポンス
        end
    end

    LB-->>C: 最終レスポンス
```

### 3. クラス図（Class Diagram）

#### 3.1 基本的なクラス図

```mermaid
classDiagram
    class User {
        +String id
        +String name
        +String email
        +Date createdAt
        +login()
        +logout()
        +updateProfile()
    }

    class Document {
        +String id
        +String title
        +String content
        +User author
        +Date createdAt
        +save()
        +delete()
        +share()
    }

    class Comment {
        +String id
        +String content
        +User author
        +Date createdAt
        +edit()
        +delete()
    }

    User ||--o{ Document : "creates"
    Document ||--o{ Comment : "has"
    User ||--o{ Comment : "writes"
```

#### 3.2 継承関係を含むクラス図

```mermaid
classDiagram
    class BaseEntity {
        <<abstract>>
        +String id
        +Date createdAt
        +Date updatedAt
        +save()
        +delete()
    }

    class User {
        +String name
        +String email
        +UserRole role
        +authenticate()
    }

    class Document {
        +String title
        +String content
        +DocumentType type
        +publish()
        +archive()
    }

    class MediaFile {
        +String filename
        +String mimeType
        +Integer fileSize
        +upload()
        +download()
    }

    BaseEntity <|-- User
    BaseEntity <|-- Document
    BaseEntity <|-- MediaFile

    User "1" --> "0..*" Document : owns
    Document "1" --> "0..*" MediaFile : contains
```

### 4. ER 図（Entity Relationship Diagram）

```mermaid
erDiagram
    USERS {
        uuid id PK
        varchar name
        varchar email UK
        varchar password_hash
        timestamp created_at
        timestamp updated_at
    }

    DOCUMENTS {
        uuid id PK
        varchar title
        text content
        uuid author_id FK
        boolean is_public
        timestamp created_at
        timestamp updated_at
    }

    COMMENTS {
        uuid id PK
        text content
        uuid document_id FK
        uuid author_id FK
        timestamp created_at
        timestamp updated_at
    }

    TAGS {
        uuid id PK
        varchar name UK
        varchar color
    }

    DOCUMENT_TAGS {
        uuid document_id FK
        uuid tag_id FK
    }

    USERS ||--o{ DOCUMENTS : "authors"
    USERS ||--o{ COMMENTS : "writes"
    DOCUMENTS ||--o{ COMMENTS : "has"
    DOCUMENTS }o--o{ TAGS : "tagged_with"
```

### 5. ガントチャート（Gantt Chart）

```mermaid
gantt
    title プロジェクト開発スケジュール
    dateFormat  YYYY-MM-DD
    section 企画・設計
    要件定義           :done,    req,  2024-01-01, 2024-01-14
    基本設計           :done,    design, 2024-01-08, 2024-01-21
    詳細設計           :active,  detail, 2024-01-15, 2024-01-28

    section 開発
    フロントエンド開発  :         frontend, 2024-01-22, 2024-03-15
    バックエンド開発    :         backend,  2024-01-22, 2024-03-08
    API開発            :         api,      2024-01-29, 2024-02-19

    section テスト
    単体テスト         :         unittest, 2024-02-26, 2024-03-12
    結合テスト         :         integration, 2024-03-05, 2024-03-19
    システムテスト      :         systemtest, 2024-03-12, 2024-03-26

    section リリース
    本番環境構築        :         prod,     2024-03-19, 2024-03-26
    リリース準備        :         release,  2024-03-23, 2024-03-29
    本番リリース        :crit,    launch,   2024-03-29, 2024-03-30
```

### 6. パイチャート（Pie Chart）

```mermaid
pie title システムリソース使用率
    "CPU" : 35
    "メモリ" : 28
    "ストレージ" : 22
    "ネットワーク" : 15
```

### 7. Git Graph

```mermaid
gitgraph
    commit id: "Initial commit"
    branch develop
    checkout develop
    commit id: "Add user authentication"
    commit id: "Implement document CRUD"
    branch feature/ai-integration
    checkout feature/ai-integration
    commit id: "Add AI service integration"
    commit id: "Implement auto-summarization"
    checkout develop
    merge feature/ai-integration
    commit id: "Fix merge conflicts"
    checkout main
    merge develop
    commit id: "Release v1.0.0"
    tag: "v1.0.0"
```

### 8. Journey Map

```mermaid
journey
    title ユーザーのドキュメント作成体験
    section ログイン
      サイトアクセス: 5: ユーザー
      ログイン情報入力: 3: ユーザー
      認証完了: 4: ユーザー
    section ドキュメント作成
      新規作成ボタンクリック: 5: ユーザー
      テンプレート選択: 4: ユーザー
      内容入力: 3: ユーザー
      画像アップロード: 2: ユーザー
    section 保存・共有
      下書き保存: 4: ユーザー
      プレビュー確認: 5: ユーザー
      公開設定: 3: ユーザー
      チーム共有: 5: ユーザー
```

### 9. State Diagram

```mermaid
stateDiagram-v2
    [*] --> Draft : 新規作成
    Draft --> Review : レビュー依頼
    Review --> Draft : 修正要求
    Review --> Approved : 承認
    Approved --> Published : 公開
    Published --> Archived : アーカイブ
    Draft --> Deleted : 削除
    Archived --> [*] : 完全削除

    state Review {
        [*] --> Pending
        Pending --> InProgress : レビュー開始
        InProgress --> Completed : レビュー完了
        Completed --> [*]
    }
```

### 10. C4 Architecture Diagram

```mermaid
C4Context
    title システムコンテキスト図

    Person(user, "ユーザー", "ドキュメントの作成・閲覧を行う")
    Person(admin, "管理者", "システムの管理・設定を行う")

    System(docSystem, "ドキュメント管理システム", "Webベースのドキュメント管理プラットフォーム")

    System_Ext(github, "GitHub", "コード管理・CI/CD")
    System_Ext(figma, "Figma", "デザインツール")
    System_Ext(ai, "AI Service", "自動要約・翻訳サービス")

    Rel(user, docSystem, "使用")
    Rel(admin, docSystem, "管理")
    Rel(docSystem, github, "連携")
    Rel(docSystem, figma, "埋め込み")
    Rel(docSystem, ai, "API呼び出し")
```

### 11. 検証チェックリスト

各ツールで以下の項目を確認してください：

#### レンダリング確認

* [ ] フローチャートが正しく表示される
* [ ] シーケンス図が正しく表示される
* [ ] クラス図が正しく表示される
* [ ] ER 図が正しく表示される
* [ ] ガントチャートが正しく表示される
* [ ] パイチャートが正しく表示される
* [ ] Git Graph が正しく表示される
* [ ] Journey Map が正しく表示される
* [ ] State Diagram が正しく表示される
* [ ] C4 Diagram が正しく表示される

#### 編集機能確認

* [ ] Mermaid 記法でリアルタイム編集可能
* [ ] 図表のエクスポートが可能（PNG/SVG 等）
* [ ] 図表の埋め込みコードが取得可能
* [ ] 図表のサイズ調整が可能

#### AI 連携確認

* [ ] AI が図表の内容を理解・解釈可能
* [ ] AI による図表の自動生成・修正提案
* [ ] 図表に基づくドキュメント生成

#### パフォーマンス確認

* [ ] 大きな図表でも適切な表示速度
* [ ] 複数の図表を含むページの読み込み速度
* [ ] 図表編集時のレスポンス性能

***

**使用方法**: 各ツールでこのファイル全体をコピー＆ペーストし、上記の図表が正しく表示されるかを確認してください。
