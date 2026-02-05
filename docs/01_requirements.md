# 機能要件と設計の詳細

## 機能要件

### 1. ユーザー管理

#### 1.1 イベント作成者（主催者）の要件

- イベントを作成するには、必ずアカウントが必要
- アカウント作成方法は以下の2つ:
  - メールアドレス + パスワードで登録
  - SNSアカウント（Google/GitHub/LINE）でログイン
- ログイン/ログアウト機能が必要
- パスワードリセット機能が必要（メール送信）

#### 1.2 参加者の要件

- イベントタイプによって参加方法が異なる:
  - **ゲスト参加OK**: アカウント不要、名前とメールアドレスのみ
  - **SNSログイン必須**: Google/GitHub/LINEのいずれかでログイン
  - **アカウント登録必須**: このサービスのアカウント作成が必要

#### 1.3 SNSログインの仕様

- 対応するSNS: Google、GitHub、LINE
- 初回ログイン時: 自動でユーザーアカウントを作成
- 2回目以降: 既存のユーザーアカウントに紐付けてログイン
- 1つのメールアドレスに複数のSNSアカウントを紐付け可能
- SNSログインのみのユーザーは、パスワードフィールドがnull

### 2. イベント管理

#### 2.1 イベント作成

以下の情報を入力してイベントを作成:

- **タイトル**: 必須、255文字以内
- **説明**: 必須、テキスト
- **日時**: 必須、未来の日時
- **場所**: 必須、255文字以内
- **参加方法**: 必須、以下の3つから選択
  - ゲスト参加OK
  - SNSログイン必須（どのSNSを許可するか選択）
  - アカウント登録必須
- **定員**: 任意、正の整数または無制限

作成時の自動処理:

- 一意なslug（8文字のランダム英数字）を自動生成
- イベントURLは `/events/{slug}` の形式

#### 2.2 イベント編集

- 作成者のみが編集可能
- すべての項目を編集可能（slugは変更不可）
- 編集時にバリデーションを実行

#### 2.3 イベント削除

- 作成者のみが削除可能
- 論理削除（deleted_atに日時を記録）
- 削除後もデータは保持される

#### 2.4 イベント一覧

- ログインユーザーは、自分が作成したイベント一覧を閲覧可能
- 表示項目: タイトル、日時、参加者数、定員
- 編集・削除ボタンを表示

### 3. 参加管理

#### 3.1 イベント詳細ページ

- すべてのユーザーが、slugを知っていればアクセス可能
- 表示項目:
  - イベントのタイトル、日時、場所、説明
  - 参加者リスト（名前のみ）
  - 現在の参加者数と定員
  - 参加方法の説明
- イベントタイプに応じて、参加フォームまたはログインボタンを表示

#### 3.2 参加登録

##### ゲスト参加OKの場合

フロー:

1. 名前とメールアドレスを入力
2. 「参加する」ボタンをクリック
3. バリデーション:
   - 名前: 必須、255文字以内
   - メールアドレス: 必須、正しい形式、同じイベントで重複不可
   - 定員: 定員に達していないか確認
4. 参加確定
5. 参加確認メールを送信（キャンセル用トークン付き）

##### SNSログイン必須の場合

フロー:

1. 許可されたSNSのログインボタンを表示（例: Google、GitHub）
2. SNSでログイン
3. 初回: ユーザーアカウント自動作成、social_accountsに記録
4. 2回目以降: 既存アカウントでログイン
5. 「参加する」ボタンをクリック
6. バリデーション:
   - 同じユーザーが同じイベントに重複登録していないか
   - 定員に達していないか
7. 参加確定
8. 参加確認メールを送信

##### アカウント登録必須の場合

フロー:

1. 未ログインの場合、ログイン画面にリダイレクト
2. アカウント未登録の場合、登録画面にリダイレクト
3. ログイン後、「参加する」ボタンをクリック
4. バリデーション:
   - 同じユーザーが同じイベントに重複登録していないか
   - 定員に達していないか
5. 参加確定
6. 参加確認メールを送信

#### 3.3 参加者リスト表示

- すべてのユーザーが参加者リストを閲覧可能
- 表示項目:
  - 参加者の名前
  - 参加日時
  - 参加経路（ゲスト/Google/GitHub/LINE/アカウント）は主催者のみ閲覧可能

#### 3.4 参加キャンセル

##### ゲスト参加者の場合

- 参加確認メールに記載されたキャンセル用URLにアクセス
- participation_tokenで本人確認
- 「キャンセルする」ボタンをクリック
- statusを'cancelled'に変更、cancelled_atに日時を記録
- キャンセル通知メールを主催者に送信

##### ログインユーザーの場合

- マイページから参加中のイベント一覧を表示
- 「キャンセル」ボタンをクリック
- statusを'cancelled'に変更、cancelled_atに日時を記録
- キャンセル通知メールを主催者に送信

#### 3.5 参加者の強制削除（主催者権限）

- イベント作成者のみが実行可能
- 参加者リストから「削除」ボタンをクリック
- 確認ダイアログを表示
- 削除実行: participantsテーブルから物理削除

### 4. メール通知

#### 4.1 参加確認メール（参加者へ）

送信タイミング: 参加登録完了時

内容:

```
件名: [イベント名] への参加登録が完了しました

本文:
[参加者名] 様

以下のイベントへの参加登録が完了しました。

イベント名: [タイトル]
日時: [日時]
場所: [場所]

イベント詳細: [イベントURL]

※ キャンセルする場合は以下のURLにアクセスしてください
[キャンセルURL]（ゲスト参加者のみ）
```

#### 4.2 キャンセル通知メール（主催者へ）

送信タイミング: 参加キャンセル時

内容:

```
件名: [イベント名] の参加者がキャンセルしました

本文:
[主催者名] 様

以下の参加者がキャンセルしました。

イベント名: [タイトル]
キャンセルした参加者: [名前]
キャンセル日時: [日時]

現在の参加者数: [人数]/[定員]

イベント管理: [イベント管理URL]
```

### 5. セキュリティとバリデーション

#### 5.1 重複参加防止

- **ゲスト参加者**: event_id + guest_emailのUNIQUE制約
- **ログインユーザー**: event_id + user_idのUNIQUE制約
- データベースレベルで重複を防止

#### 5.2 定員管理

- 定員がnullの場合: 無制限
- 定員が設定されている場合: 参加登録時にチェック
- 同時登録の競合: トランザクション + ロックで制御

```sql
DB::transaction(function () {
    $event = Event::lockForUpdate()->find($eventId);
    $currentCount = $event->participants()->where('status', 'approved')->count();
    if ($event->max_participants && $currentCount >= $event->max_participants) {
        throw new Exception('定員に達しています');
    }
    // 参加登録処理
});
```

#### 5.3 slug生成

- 8文字のランダム英数字（a-zA-Z0-9）
- 重複チェックを実行、重複する場合は再生成
- 例: `abc12345`, `XyZ98765`

#### 5.4 participation_token生成

- 128bit（32文字）のランダム文字列
- キャンセル用URLに使用: `/events/{slug}/cancel/{token}`

#### 5.5 入力バリデーション

- タイトル: 必須、文字列、255文字以内
- 説明: 必須、文字列
- 日時: 必須、日時形式
- 場所: 必須、文字列、255文字以内
- 定員: 任意、正の整数
- 名前（ゲスト）: 必須、文字列、255文字以内
- メールアドレス（ゲスト）: 必須、メール形式

#### 5.6 権限チェック

- イベント編集: 作成者のみ
- イベント削除: 作成者のみ
- 参加者削除: 作成者のみ
- Laravelのポリシー（Policy）で実装

#### 5.7 CSRF対策

- Laravelの標準機能で対応済み
- すべてのフォームに `@csrf` ディレクティブを含める

#### 5.8 XSS対策

- Bladeテンプレートの自動エスケープで対応済み
- `{{ $variable }}` は自動的にエスケープされる
- HTMLを出力する場合は `{!! $variable !!}` を使用（要注意）

---

## データベース設計

### ER図

```
users (1) --< (N) events
users (1) --< (N) social_accounts
users (1) --< (N) participants
events (1) --< (N) participants
```

### テーブル定義

#### users（ユーザーテーブル）

```sql
CREATE TABLE users (
    id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(255) NOT NULL,
    email VARCHAR(255) UNIQUE NOT NULL,
    password VARCHAR(255) NULLABLE COMMENT 'SNSログインのみの場合null',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    INDEX idx_email (email)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

#### social_accounts（SNSアカウント連携テーブル）

```sql
CREATE TABLE social_accounts (
    id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id BIGINT UNSIGNED NOT NULL,
    provider ENUM('google', 'github', 'line') NOT NULL,
    provider_id VARCHAR(255) NOT NULL COMMENT 'SNS側のユーザーID',
    provider_token TEXT NULLABLE COMMENT 'アクセストークン（任意）',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    UNIQUE KEY unique_social_account (provider, provider_id),
    INDEX idx_user_id (user_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

#### events（イベントテーブル）

```sql
CREATE TABLE events (
    id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    user_id BIGINT UNSIGNED NOT NULL COMMENT '作成者',
    slug VARCHAR(255) UNIQUE NOT NULL COMMENT 'URL用の一意な文字列',
    title VARCHAR(255) NOT NULL,
    description TEXT NOT NULL,
    event_date DATETIME NOT NULL,
    location VARCHAR(255) NOT NULL,
    max_participants INT UNSIGNED NULLABLE COMMENT 'null = 無制限',
    participation_type ENUM('guest', 'social_login', 'account_required') NOT NULL,
    allowed_social_providers JSON NULLABLE COMMENT '["google", "github", "line"]',
    deleted_at TIMESTAMP NULLABLE COMMENT '論理削除',
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    INDEX idx_slug (slug),
    INDEX idx_user_id (user_id),
    INDEX idx_event_date (event_date),
    INDEX idx_deleted_at (deleted_at)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

#### participants（参加者テーブル）

```sql
CREATE TABLE participants (
    id BIGINT UNSIGNED AUTO_INCREMENT PRIMARY KEY,
    event_id BIGINT UNSIGNED NOT NULL,
    user_id BIGINT UNSIGNED NULLABLE COMMENT 'ログインユーザーの場合',
    guest_name VARCHAR(255) NULLABLE COMMENT 'ゲスト参加の場合',
    guest_email VARCHAR(255) NULLABLE COMMENT 'ゲスト参加の場合',
    participation_token VARCHAR(255) UNIQUE NOT NULL COMMENT 'キャンセル用トークン',
    joined_via ENUM('guest', 'google', 'github', 'line', 'account') NOT NULL COMMENT '参加経路',
    status ENUM('approved', 'cancelled') NOT NULL DEFAULT 'approved',
    cancelled_at TIMESTAMP NULLABLE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    updated_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
    FOREIGN KEY (event_id) REFERENCES events(id) ON DELETE CASCADE,
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE,
    UNIQUE KEY unique_user_participation (event_id, user_id) COMMENT 'ログインユーザーの重複防止',
    UNIQUE KEY unique_guest_participation (event_id, guest_email) COMMENT 'ゲストの重複防止',
    INDEX idx_event_id (event_id),
    INDEX idx_user_id (user_id),
    INDEX idx_status (status),
    INDEX idx_participation_token (participation_token)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COLLATE=utf8mb4_unicode_ci;
```

### データ型と制約の説明

#### ENUM型の値

- `participation_type`: `guest`, `social_login`, `account_required`
- `provider`: `google`, `github`, `line`
- `joined_via`: `guest`, `google`, `github`, `line`, `account`
- `status`: `approved`, `cancelled`

#### JSON型の使用

- `allowed_social_providers`: 許可するSNSプロバイダーのリスト
  - 例: `["google", "github"]`
  - 例: `["google", "github", "line"]`

#### NULLABLE な列

- `users.password`: SNSログインのみの場合null
- `events.max_participants`: 定員無制限の場合null
- `events.allowed_social_providers`: participation_type が `social_login` の場合のみ使用
- `participants.user_id`: ゲスト参加の場合null
- `participants.guest_name`: ログインユーザーの場合null
- `participants.guest_email`: ログインユーザーの場合null

---

## イベントタイプの詳細仕様

### 1. ゲスト参加OK

#### 設定画面

```
○ ゲスト参加OK
  └ 名前とメールアドレスのみで参加可能
  └ ⚠️ なりすましのリスクがあります
```

#### データベース設定

```php
participation_type = 'guest'
allowed_social_providers = null
```

#### 参加登録画面

```
┌────────────────────────────────┐
│ 名前：    [___________________]│
│ メール：  [___________________]│
│         [参加する]             │
└────────────────────────────────┘
```

#### 参加フロー

1. 名前とメールアドレスを入力
2. 「参加する」をクリック
3. バリデーション実行
4. participantsテーブルに登録:
   - `user_id` = null
   - `guest_name` = 入力された名前
   - `guest_email` = 入力されたメールアドレス
   - `joined_via` = 'guest'
   - `status` = 'approved'
5. 参加確認メール送信

### 2. SNSログイン必須

#### 設定画面

```
○ SNSログイン必須 【おすすめ】
  └ Google/GitHub/LINEでログイン
  └ 会員登録不要で本人確認できます
  └ 参加できるSNS:
     □ Google  □ GitHub  □ LINE
```

#### データベース設定

```php
participation_type = 'social_login'
allowed_social_providers = ["google", "github"] // 選択されたもの
```

#### 参加登録画面（未ログインの場合）

```
┌────────────────────────────────┐
│  [Googleでログイン]            │
│  [GitHubでログイン]            │
└────────────────────────────────┘
```

#### 参加登録画面（ログイン済みの場合）

```
┌────────────────────────────────┐
│ ログイン中: 山田太郎            │
│         [参加する]             │
└────────────────────────────────┘
```

#### 参加フロー

1. 許可されたSNSのログインボタンをクリック
2. SNS認証画面にリダイレクト
3. 認証成功、コールバックURLに戻る
4. 初回: usersテーブルにユーザー作成、social_accountsに記録
5. 2回目以降: 既存ユーザーとしてログイン
6. イベント詳細ページに戻る
7. 「参加する」をクリック
8. バリデーション実行
   - ログイン済みか確認
   - 許可されたSNSでログインしているか確認
9. participantsテーブルに登録:
   - `user_id` = ログインユーザーのID
   - `guest_name` = null
   - `guest_email` = null
   - `joined_via` = 'google' or 'github' or 'line'
   - `status` = 'approved'
10. 参加確認メール送信

### 3. アカウント登録必須

#### 設定画面

```
○ アカウント登録必須
  └ このサービスのアカウントが必要
  └ 最も信頼性が高い方法です
```

#### データベース設定

```php
participation_type = 'account_required'
allowed_social_providers = null
```

#### 参加登録画面（未ログインの場合）

```
┌────────────────────────────────┐
│ このイベントはアカウント登録が  │
│ 必要です。                      │
│                                 │
│  [ログイン]  [新規登録]         │
└────────────────────────────────┘
```

#### 参加登録画面（ログイン済みの場合）

```
┌────────────────────────────────┐
│ ログイン中: 山田太郎            │
│         [参加する]             │
└────────────────────────────────┘
```

#### 参加フロー

1. 未ログインの場合、ログイン画面にリダイレクト
2. アカウント未登録の場合、登録画面にリダイレクト
3. ログイン後、イベント詳細ページに戻る
4. 「参加する」をクリック
5. バリデーション実行
   - ログイン済みか確認
6. participantsテーブルに登録:
   - `user_id` = ログインユーザーのID
   - `guest_name` = null
   - `guest_email` = null
   - `joined_via` = 'account'
   - `status` = 'approved'
7. 参加確認メール送信

---

## UI/UX設計

### 1. イベント作成画面

```
┌──────────────────────────────────────────┐
│ 新しいイベントを作成                       │
├──────────────────────────────────────────┤
│                                            │
│ イベント情報                               │
│                                            │
│ タイトル *                                 │
│ [_______________________________________] │
│                                            │
│ 日時 *                                     │
│ [2026-02-15] [19:00]                      │
│                                            │
│ 場所 *                                     │
│ [_______________________________________] │
│                                            │
│ 説明 *                                     │
│ [_______________________________________] │
│ [_______________________________________] │
│ [_______________________________________] │
│                                            │
│ 参加方法 *                                 │
│                                            │
│ ○ ゲスト参加OK                            │
│   名前とメールアドレスのみで参加可能       │
│   ⚠️ なりすましのリスクがあります          │
│                                            │
│ ○ SNSログイン必須 【おすすめ】            │
│   Google/GitHub/LINEでログイン            │
│   会員登録不要で本人確認できます           │
│                                            │
│   参加できるSNS:                           │
│   □ Google  □ GitHub  □ LINE             │
│                                            │
│ ○ アカウント登録必須                      │
│   このサービスのアカウントが必要           │
│   最も信頼性が高い方法です                 │
│                                            │
│ 定員                                       │
│ [50] 人 （空欄で無制限）                   │
│                                            │
│              [作成する]                    │
│                                            │
└──────────────────────────────────────────┘
```

### 2. イベント詳細ページ（ゲスト参加OK）

```
┌──────────────────────────────────────────┐
│ 🎉 Laravel勉強会 #42                      │
├──────────────────────────────────────────┤
│ 📅 2026年2月15日 19:00 - 21:00           │
│ 📍 渋谷コワーキングスペース               │
│                                            │
│ 説明                                       │
│ Laravelの最新機能について学びましょう！   │
│ 初心者大歓迎です。                         │
│                                            │
│ ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  │
│                                            │
│ 参加者 (12/50人)                           │
│ ・山田太郎                                 │
│ ・佐藤花子                                 │
│ ・鈴木一郎                                 │
│ ・田中美咲                                 │
│ （他8名）                                  │
│                                            │
│ ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  │
│                                            │
│ このイベントに参加する                     │
│                                            │
│ 名前 *                                     │
│ [_______________________________________] │
│                                            │
│ メールアドレス *                           │
│ [_______________________________________] │
│                                            │
│              [参加する]                    │
│                                            │
└──────────────────────────────────────────┘
```

### 3. イベント詳細ページ（SNSログイン必須）

```
┌──────────────────────────────────────────┐
│ 🎉 エンジニア交流会                        │
├──────────────────────────────────────────┤
│ 📅 2026年2月20日 18:00 - 20:00           │
│ 📍 某オフィス                             │
│                                            │
│ 説明                                       │
│ エンジニア同士の交流会です。               │
│                                            │
│ ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  │
│                                            │
│ 参加者 (8/30人)                            │
│ ・山田太郎                                 │
│ ・佐藤花子                                 │
│ （他6名）                                  │
│                                            │
│ ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  │
│                                            │
│ ⚠️ このイベントはSNSログインが必要です     │
│                                            │
│  [Googleでログイン]                       │
│  [GitHubでログイン]                       │
│                                            │
└──────────────────────────────────────────┘
```

### 4. マイページ（作成者）

```
┌──────────────────────────────────────────┐
│ マイイベント                               │
├──────────────────────────────────────────┤
│                                            │
│ [+ 新しいイベントを作成]                   │
│                                            │
│ ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  │
│                                            │
│ 開催予定のイベント                         │
│                                            │
│ ┌────────────────────────────────┐       │
│ │ 🎉 Laravel勉強会 #42            │       │
│ │ 📅 2026/02/15 19:00            │       │
│ │ 👥 12/50人                      │       │
│ │ [詳細] [編集] [参加者管理]      │       │
│ └────────────────────────────────┘       │
│                                            │
│ ┌────────────────────────────────┐       │
│ │ 🎉 エンジニア交流会              │       │
│ │ 📅 2026/02/20 18:00            │       │
│ │ 👥 8/30人                       │       │
│ │ [詳細] [編集] [参加者管理]      │       │
│ └────────────────────────────────┘       │
│                                            │
│ ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  │
│                                            │
│ 開催済みのイベント                         │
│                                            │
│ ┌────────────────────────────────┐       │
│ │ 🎉 忘年会                       │       │
│ │ 📅 2025/12/25 18:00            │       │
│ │ 👥 20/20人                      │       │
│ │ [詳細]                          │       │
│ └────────────────────────────────┘       │
│                                            │
└──────────────────────────────────────────┘
```

### 5. 参加者管理画面（主催者専用）

```
┌──────────────────────────────────────────┐
│ 参加者管理: Laravel勉強会 #42             │
├──────────────────────────────────────────┤
│                                            │
│ イベント情報                               │
│ 📅 2026/02/15 19:00                       │
│ 📍 渋谷コワーキングスペース               │
│ 👥 現在の参加者: 12/50人                   │
│                                            │
│ ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━  │
│                                            │
│ 参加者リスト                               │
│                                            │
│ ┌──┬──────┬─────────┬─────┬─────┐ │
│ │No│名前    │参加経路   │参加日時│操作  │ │
│ ├──┼──────┼─────────┼─────┼─────┤ │
│ │1 │山田太郎│Google     │02/01  │[削除]│ │
│ │2 │佐藤花子│GitHub     │02/01  │[削除]│ │
│ │3 │鈴木一郎│ゲスト     │02/02  │[削除]│ │
│ │  │        │(suzuki@..│       │      │ │
│ │4 │田中美咲│LINE       │02/02  │[削除]│ │
│ └──┴──────┴─────────┴─────┴─────┘ │
│                                            │
│ [CSVダウンロード]                         │
│                                            │
└──────────────────────────────────────────┘
```

---

## API設計（内部用）

### エンドポイント一覧

#### 認証関連

- `GET /register` - 登録画面表示
- `POST /register` - ユーザー登録
- `GET /login` - ログイン画面表示
- `POST /login` - ログイン処理
- `POST /logout` - ログアウト
- `GET /password/reset` - パスワードリセット画面表示
- `POST /password/email` - パスワードリセットメール送信
- `GET /password/reset/{token}` - パスワード再設定画面表示
- `POST /password/reset` - パスワード再設定

#### SNSログイン

- `GET /auth/{provider}` - SNS認証画面へリダイレクト
- `GET /auth/{provider}/callback` - SNS認証後のコールバック

#### イベント管理

- `GET /events` - イベント一覧（自分が作成したもの）
- `GET /events/create` - イベント作成画面表示
- `POST /events` - イベント作成
- `GET /events/{slug}` - イベント詳細（公開）
- `GET /events/{slug}/edit` - イベント編集画面表示
- `PUT /events/{slug}` - イベント更新
- `DELETE /events/{slug}` - イベント削除

#### 参加管理

- `POST /events/{slug}/participate` - 参加登録
- `GET /events/{slug}/cancel/{token}` - キャンセル画面表示
- `POST /events/{slug}/cancel/{token}` - キャンセル処理
- `GET /events/{slug}/participants` - 参加者リスト（主催者専用）
- `DELETE /events/{slug}/participants/{id}` - 参加者削除（主催者専用）

---

**作成日**: 2026年2月1日
**バージョン**: 1.0
