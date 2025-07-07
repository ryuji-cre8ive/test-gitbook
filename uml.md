# uml

## PlantUML 記法検証用サンプル

このドキュメントは、各ドキュメント管理ツールの PlantUML 記法対応状況を検証するためのサンプル図表集です。

### 1. クラス図（Class Diagram）

#### 1.1 基本的なクラス図

```plantuml
@startuml
class User {
    -id: String
    -name: String
    -email: String
    -createdAt: Date
    +login(): boolean
    +logout(): void
    +updateProfile(): void
}

class Document {
    -id: String
    -title: String
    -content: String
    -author: User
    -createdAt: Date
    +save(): void
    +delete(): void
    +share(): void
}

class Comment {
    -id: String
    -content: String
    -author: User
    -createdAt: Date
    +edit(): void
    +delete(): void
}

User ||--o{ Document : creates
Document ||--o{ Comment : has
User ||--o{ Comment : writes
@enduml
```

#### 1.2 継承関係を含むクラス図

```plantuml
@startuml
abstract class BaseEntity {
    #id: String
    #createdAt: Date
    #updatedAt: Date
    +save(): void
    +delete(): void
}

class User {
    -name: String
    -email: String
    -role: UserRole
    +authenticate(): boolean
}

class Document {
    -title: String
    -content: String
    -type: DocumentType
    +publish(): void
    +archive(): void
}

enum UserRole {
    ADMIN
    EDITOR
    VIEWER
}

enum DocumentType {
    SPECIFICATION
    MANUAL
    REPORT
}

BaseEntity <|-- User
BaseEntity <|-- Document
User "1" --> "0..*" Document : owns
User ..> UserRole : uses
Document ..> DocumentType : uses
@enduml
```

### 2. シーケンス図（Sequence Diagram）

#### 2.1 基本的なシーケンス図

```plantuml
@startuml
actor User as user
participant "Frontend" as frontend
participant "API Server" as api
database "Database" as db

user -> frontend: ログイン要求
frontend -> api: 認証リクエスト
api -> db: ユーザー情報照会
db --> api: ユーザー情報返却
api --> frontend: 認証トークン
frontend --> user: ログイン成功
@enduml
```

#### 2.2 複雑なシーケンス図

```plantuml
@startuml
participant "Client" as client
participant "Load Balancer" as lb
participant "Application" as app
participant "Redis Cache" as cache
database "PostgreSQL" as db

client -> lb: APIリクエスト
activate lb
lb -> app: リクエスト転送
activate app

app -> cache: キャッシュ確認
activate cache

alt キャッシュヒット
    cache --> app: キャッシュデータ返却
    deactivate cache
    app --> lb: レスポンス
else キャッシュミス
    cache --> app: キャッシュなし
    deactivate cache
    app -> db: データ取得
    activate db

    alt データ取得成功
        db --> app: データ返却
        deactivate db
        app -> cache: キャッシュ更新
        activate cache
        cache --> app: 更新完了
        deactivate cache
        app --> lb: レスポンス
    else データベースエラー
        db --> app: エラー
        deactivate db
        app --> lb: エラーレスポンス
    end
end

deactivate app
lb --> client: 最終レスポンス
deactivate lb
@enduml
```

### 3. アクティビティ図（Activity Diagram）

#### 3.1 基本的なアクティビティ図

```plantuml
@startuml
start
:ユーザーログイン;
if (認証成功?) then (yes)
    :メイン画面表示;
    :ドキュメント一覧取得;
    if (ドキュメント作成?) then (yes)
        :新規ドキュメント作成;
        :内容入力;
        :保存;
    else (no)
        :既存ドキュメント選択;
        :ドキュメント表示;
    endif
else (no)
    :エラーメッセージ表示;
    stop
endif
:ログアウト;
stop
@enduml
```

#### 3.2 複雑なワークフロー

```plantuml
@startuml
start
:ドキュメント作成要求;
:入力値検証;

if (入力値有効?) then (no)
    :エラーメッセージ表示;
    stop
else (yes)
    :権限チェック;
    if (権限OK?) then (no)
        :アクセス拒否;
        stop
    else (yes)
        fork
            :メタデータ保存;
        fork again
            :全文検索インデックス更新;
        fork again
            :通知送信;
        end fork

        :ドキュメント保存;

        if (AI処理要求?) then (yes)
            :AI要約生成;
            :要約保存;
        endif

        :成功レスポンス;
    endif
endif
stop
@enduml
```

### 4. ユースケース図（Use Case Diagram）

```plantuml
@startuml
left to right direction

actor "一般ユーザー" as user
actor "管理者" as admin
actor "システム管理者" as sysadmin

rectangle "ドキュメント管理システム" {
    usecase "ログイン" as login
    usecase "ドキュメント作成" as create
    usecase "ドキュメント編集" as edit
    usecase "ドキュメント閲覧" as view
    usecase "コメント投稿" as comment
    usecase "ユーザー管理" as usermgmt
    usecase "権限設定" as permission
    usecase "システム設定" as sysconfig
    usecase "バックアップ" as backup
}

user --> login
user --> create
user --> edit
user --> view
user --> comment

admin --> usermgmt
admin --> permission

sysadmin --> sysconfig
sysadmin --> backup

edit .> login : <<include>>
create .> login : <<include>>
comment .> login : <<include>>
@enduml
```

### 5. コンポーネント図（Component Diagram）

```plantuml
@startuml
package "Frontend" {
    [React App] as react
    [Redux Store] as redux
    [Router] as router
}

package "Backend" {
    [API Gateway] as gateway
    [Auth Service] as auth
    [Document Service] as docservice
    [Search Service] as search
}

package "Database" {
    [PostgreSQL] as postgres
    [Redis Cache] as redis
    [Elasticsearch] as elastic
}

package "External Services" {
    [GitHub API] as github
    [Figma API] as figma
    [OpenAI API] as openai
}

react --> redux
react --> router
react --> gateway : HTTP

gateway --> auth
gateway --> docservice
gateway --> search

auth --> postgres
docservice --> postgres
docservice --> redis
search --> elastic

docservice --> github : API
docservice --> figma : API
docservice --> openai : API
@enduml
```

### 6. 配置図（Deployment Diagram）

```plantuml
@startuml
node "Load Balancer" as lb {
    [Nginx]
}

node "Application Server 1" as app1 {
    [Docker Container 1]
    [Node.js App 1]
}

node "Application Server 2" as app2 {
    [Docker Container 2]
    [Node.js App 2]
}

node "Database Server" as dbserver {
    [PostgreSQL Primary]
    [PostgreSQL Replica]
}

node "Cache Server" as cacheserver {
    [Redis Cluster]
}

cloud "External Services" as external {
    [GitHub]
    [Figma]
    [OpenAI]
}

lb --> app1 : HTTP
lb --> app2 : HTTP
app1 --> dbserver : TCP/5432
app2 --> dbserver : TCP/5432
app1 --> cacheserver : TCP/6379
app2 --> cacheserver : TCP/6379
app1 --> external : HTTPS
app2 --> external : HTTPS
@enduml
```

### 7. 状態図（State Diagram）

```plantuml
@startuml
[*] --> Draft : 新規作成

Draft --> Review : レビュー依頼
Draft --> Deleted : 削除

Review --> Draft : 修正要求
Review --> Approved : 承認
Review --> Rejected : 却下

Approved --> Published : 公開
Approved --> Draft : 再編集

Published --> Archived : アーカイブ
Published --> Draft : 編集

Rejected --> Draft : 再作成
Rejected --> Deleted : 削除

Archived --> Published : 復元
Archived --> [*] : 完全削除

Deleted --> [*]

state Review {
    [*] --> Pending
    Pending --> InProgress : 開始
    InProgress --> Completed : 完了
    Completed --> [*]
}
@enduml
```

### 8. タイミング図（Timing Diagram）

```plantuml
@startuml
robust "User Request" as req
robust "Load Balancer" as lb
robust "Application" as app
robust "Database" as db

@0
req is Idle
lb is Waiting
app is Ready
db is Available

@100
req is Sending
lb is Processing

@200
lb is Forwarding
app is Processing

@300
app is Querying
db is Processing

@400
db is Responding
app is Processing

@500
app is Responding
lb is Forwarding

@600
lb is Sending
req is Receiving

@700
req is Idle
lb is Waiting
app is Ready
db is Available
@enduml
```

### 9. オブジェクト図（Object Diagram）

```plantuml
@startuml
object "user1:User" as user1 {
    id = "u001"
    name = "田中太郎"
    email = "tanaka@example.com"
    role = "EDITOR"
}

object "doc1:Document" as doc1 {
    id = "d001"
    title = "技術仕様書"
    content = "システムの技術仕様..."
    isPublic = true
}

object "comment1:Comment" as comment1 {
    id = "c001"
    content = "内容を確認しました"
}

object "comment2:Comment" as comment2 {
    id = "c002"
    content = "修正が必要です"
}

user1 --> doc1 : creates
user1 --> comment1 : writes
user1 --> comment2 : writes
doc1 --> comment1 : has
doc1 --> comment2 : has
@enduml
```

### 10. ネットワーク図

```plantuml
@startuml
!define AWSPUML https://raw.githubusercontent.com/awslabs/aws-icons-for-plantuml/v14.0/dist
!includeurl AWSPUML/AWSCommon.puml
!includeurl AWSPUML/ApplicationIntegration/ApplicationLoadBalancer.puml
!includeurl AWSPUML/Compute/EC2.puml
!includeurl AWSPUML/Database/RDS.puml
!includeurl AWSPUML/Database/ElastiCache.puml

ApplicationLoadBalancer(alb, "Application Load Balancer", "")
EC2(ec2_1, "Web Server 1", "")
EC2(ec2_2, "Web Server 2", "")
RDS(rds, "PostgreSQL Database", "")
ElastiCache(cache, "Redis Cache", "")

alb --> ec2_1
alb --> ec2_2
ec2_1 --> rds
ec2_2 --> rds
ec2_1 --> cache
ec2_2 --> cache
@enduml
```

### 11. 検証チェックリスト

各ツールで以下の項目を確認してください：

#### レンダリング確認

* [ ] クラス図が正しく表示される
* [ ] シーケンス図が正しく表示される
* [ ] アクティビティ図が正しく表示される
* [ ] ユースケース図が正しく表示される
* [ ] コンポーネント図が正しく表示される
* [ ] 配置図が正しく表示される
* [ ] 状態図が正しく表示される
* [ ] タイミング図が正しく表示される
* [ ] オブジェクト図が正しく表示される
* [ ] ネットワーク図が正しく表示される

#### 編集機能確認

* [ ] PlantUML 記法でリアルタイム編集可能
* [ ] 図表のエクスポートが可能（PNG/SVG 等）
* [ ] 図表のズーム・パン操作が可能
* [ ] スタイリング・テーマ変更が可能

#### 高度な機能確認

* [ ] includeurl による外部ライブラリ読み込み
* [ ] define による定数定義
* [ ] 条件分岐（alt/else）の表示
* [ ] ループ（loop）の表示
* [ ] ノート・コメントの表示

#### パフォーマンス確認

* [ ] 複雑な図表でも適切な表示速度
* [ ] 大量の要素を含む図表の描画性能
* [ ] 図表生成・更新時のレスポンス

#### AI 連携確認

* [ ] AI が PlantUML 記法を理解・解釈可能
* [ ] AI による PlantUML 図表の自動生成
* [ ] 図表の説明文自動生成

***

**使用方法**: 各ツールでこのファイル全体をコピー＆ペーストし、上記の図表が正しく表示されるかを確認してください。PlantUML に対応していないツールの場合は、代替手段（プラグイン、外部サービス連携等）があるかも合わせて調査してください。
