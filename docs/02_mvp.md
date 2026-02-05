# MVP機能と実装計画

## MVPの定義

### MVPとは

Minimum Viable Product（実用最小限の製品）

このシステムの最小限の価値を提供できる機能に絞り、まずは動作する製品を作成する。

### MVPの範囲

#### 実装する機能

1. ユーザー管理（登録・ログイン・SNSログイン）
2. イベント管理（作成・編集・削除）
3. 参加管理（ゲスト参加・SNSログイン参加・アカウント参加）
4. 参加者リスト表示
5. キャンセル機能
6. メール通知（参加確認、キャンセル通知）

#### 実装しない機能（フェーズ2以降）

- 承認制イベント（申請→承認フロー）
- 共同管理者機能
- コメント機能
- イベント検索・公開イベント一覧
- カレンダー表示
- リマインドメール（イベント前日通知）
- イベントのサムネイル画像
- SNSシェアボタン
- 参加履歴

---

## MVP機能一覧（チェックリスト）

### 【必須】ユーザー管理

#### イベント作成者向け

- [ ] ユーザー登録
  - [ ] メールアドレス + パスワードで登録
  - [ ] SNSログイン（Google/GitHub/LINE）
- [ ] ログイン/ログアウト
- [ ] パスワードリセット
  - [ ] メールアドレス入力
  - [ ] リセットメール送信
  - [ ] 新しいパスワード設定

#### 参加者向け

- [ ] SNSログイン（Google/GitHub/LINE）
  - [ ] 初回: 自動でユーザー作成
  - [ ] 2回目以降: 既存アカウントでログイン
- [ ] ゲスト参加（ログイン不要、名前+メールのみ）

### 【必須】イベント管理（作成者側）

#### イベントCRUD

- [ ] イベント作成
  - [ ] タイトル入力
  - [ ] 説明入力
  - [ ] 日時選択
  - [ ] 場所入力
  - [ ] 参加方法選択
    - [ ] ゲスト参加OK
    - [ ] SNSログイン必須（どのSNSを許可するか選択）
    - [ ] アカウント登録必須
  - [ ] 定員設定（任意）
  - [ ] 一意なslugの自動生成
- [ ] イベント編集
  - [ ] すべての項目を編集可能（slugは変更不可）
  - [ ] バリデーション実行
- [ ] イベント削除
  - [ ] 論理削除（deleted_atに日時を記録）
  - [ ] 確認ダイアログ表示
- [ ] 自分が作成したイベント一覧
  - [ ] タイトル、日時、参加者数、定員を表示
  - [ ] 編集・削除ボタンを表示

#### 参加者管理

- [ ] 参加者リスト表示
  - [ ] 名前
  - [ ] 参加経路（ゲスト/Google/GitHub/LINE/アカウント）
  - [ ] 参加日時
- [ ] 参加者の強制削除（主催者権限）
  - [ ] 確認ダイアログ表示
  - [ ] 物理削除実行
- [ ] 定員に達したら自動で参加締切
  - [ ] 「定員に達しました」メッセージ表示
  - [ ] 参加ボタンを非表示

### 【必須】参加管理（参加者側）

#### イベント参加

- [ ] イベント詳細ページの閲覧（URLアクセス）
  - [ ] タイトル、日時、場所、説明を表示
  - [ ] 参加者リスト表示
  - [ ] 現在の参加者数と定員を表示
- [ ] 参加登録
  - [ ] **ゲスト参加OKの場合**
    - [ ] 名前入力フォーム
    - [ ] メールアドレス入力フォーム
    - [ ] 「参加する」ボタン
    - [ ] バリデーション実行
    - [ ] 即時参加確定
  - [ ] **SNSログイン必須の場合**
    - [ ] 許可されたSNSのログインボタン表示
    - [ ] SNS認証処理
    - [ ] 「参加する」ボタン
    - [ ] バリデーション実行
    - [ ] 即時参加確定
  - [ ] **アカウント登録必須の場合**
    - [ ] 未ログインの場合、ログイン画面にリダイレクト
    - [ ] 「参加する」ボタン
    - [ ] バリデーション実行
    - [ ] 即時参加確定
- [ ] 参加者リスト表示
  - [ ] 参加者の名前を表示
  - [ ] 参加日時を表示
- [ ] 参加キャンセル
  - [ ] **ゲスト参加者の場合**
    - [ ] メールのキャンセル用URLからアクセス
    - [ ] participation_tokenで本人確認
    - [ ] 「キャンセルする」ボタン
    - [ ] キャンセル実行（status='cancelled'、cancelled_at記録）
  - [ ] **ログインユーザーの場合**
    - [ ] マイページから参加中のイベント一覧表示
    - [ ] 「キャンセル」ボタン
    - [ ] キャンセル実行（status='cancelled'、cancelled_at記録）

### 【必須】メール通知

- [ ] 参加確認メール（参加者へ）
  - [ ] イベント詳細（タイトル、日時、場所）
  - [ ] イベント詳細ページのURL
  - [ ] キャンセル用URL（ゲストのみ）
- [ ] キャンセル通知メール（主催者へ）
  - [ ] キャンセルした参加者の名前
  - [ ] キャンセル日時
  - [ ] 現在の参加者数

### 【必須】セキュリティ・バリデーション

- [ ] 同一メールアドレスでの重複参加防止（ゲスト）
  - [ ] DB制約: UNIQUE KEY (event_id, guest_email)
- [ ] 同一ユーザーでの重複参加防止（ログインユーザー）
  - [ ] DB制約: UNIQUE KEY (event_id, user_id)
- [ ] 定員オーバーの防止
  - [ ] トランザクション + ロックで制御
  - [ ] 参加登録時に定員チェック
- [ ] イベントslugの一意性保証
  - [ ] 重複チェック、重複時は再生成
- [ ] CSRF対策
  - [ ] すべてのフォームに@csrfディレクティブ
- [ ] XSS対策
  - [ ] Blade自動エスケープ（{{ $variable }}）
- [ ] 権限チェック
  - [ ] イベント編集: 作成者のみ
  - [ ] イベント削除: 作成者のみ
  - [ ] 参加者削除: 作成者のみ
  - [ ] Laravelのポリシー（Policy）で実装

---

## 技術スタック

### バックエンド

- **言語**: PHP 8.2以上
- **フレームワーク**: Laravel 11.x
- **データベース**: MySQL 8.0以上
- **認証**: Laravel Breeze または Jetstream
- **SNSログイン**: Laravel Socialite

### フロントエンド

- **テンプレートエンジン**: Blade
- **CSSフレームワーク**: Tailwind CSS
- **JavaScript**: Alpine.js（軽量なインタラクション）
  - または Livewire（リアルタイム更新が必要な場合）

### メール送信

- **開発環境**: Mailtrap または MailHog
- **本番環境**: SendGrid、Mailgun、AWS SES のいずれか

### 外部サービス（SNSログイン）

- **Google OAuth 2.0**: Google Cloud Console で設定
- **GitHub OAuth Apps**: GitHub Developer Settings で設定
- **LINE Login**: LINE Developers で設定

### 開発環境

- **ローカル開発**: Docker（Laravel Sail）または Valet
- **バージョン管理**: Git（GitHub または GitLab）

### デプロイ環境（本番）

- **サーバー**: VPS、AWS EC2、Heroku など
- **ドメイン**: 任意
- **SSL証明書**: Let's Encrypt（無料）

---

## 実装の優先順位

### ステップ1: 基盤構築（2-3日）

#### 実装内容

1. Laravelプロジェクトのセットアップ
   - `composer create-project laravel/laravel event-manager`
   - 環境設定（.env）
2. データベース設計
   - マイグレーションファイル作成
   - users、social_accounts、events、participants
3. 認証機能
   - Laravel Breeze インストール
   - 登録・ログイン・ログアウト機能
4. Laravel Socialite セットアップ
   - Google、GitHub、LINE の OAuth 設定
5. モデル・リレーション定義
   - User、SocialAccount、Event、Participant モデル
   - リレーションシップの定義

#### 成果物

- 認証機能が動作する
- ログイン/ログアウトができる
- SNSログインができる（Google、GitHub、LINE）
- データベーステーブルが作成されている

#### 実装タスク

- [ ] Laravelプロジェクト作成
- [ ] .env設定（データベース、メール）
- [ ] マイグレーションファイル作成・実行
- [ ] Laravel Breeze インストール
- [ ] Laravel Socialite インストール
- [ ] OAuth アプリケーション登録（Google、GitHub、LINE）
- [ ] SNSログインのルート・コントローラー作成
- [ ] User、SocialAccount モデル作成
- [ ] Event、Participant モデル作成

---

### ステップ2: イベント機能（2-3日）

#### 実装内容

1. イベントCRUD
   - 作成フォーム
   - 編集フォーム
   - 削除機能（論理削除）
2. slug自動生成ロジック
   - 8文字のランダム英数字生成
   - 重複チェック
3. イベント一覧（自分が作成したもの）
   - マイページ画面
   - 開催予定・開催済みの分類

#### 成果物

- イベントを作成できる
- 作成したイベントを編集・削除できる
- 自分のイベント一覧が見られる
- 各イベントに一意なURLが生成される

#### 実装タスク

- [ ] イベント作成画面（Blade）
  - [ ] タイトル、説明、日時、場所の入力フォーム
  - [ ] 参加方法選択（ラジオボタン）
  - [ ] SNS選択（チェックボックス、SNSログイン必須の場合のみ）
  - [ ] 定員入力（任意）
- [ ] EventController 作成
  - [ ] create() - 作成画面表示
  - [ ] store() - イベント作成処理
  - [ ] edit() - 編集画面表示
  - [ ] update() - イベント更新処理
  - [ ] destroy() - イベント削除処理（論理削除）
- [ ] slug生成ロジック実装
  - [ ] ランダム文字列生成関数
  - [ ] 重複チェック処理
- [ ] イベント一覧画面（Blade）
  - [ ] 自分が作成したイベントを表示
  - [ ] 編集・削除ボタン
- [ ] バリデーション実装
  - [ ] EventRequest クラス作成
  - [ ] 必須項目、文字数制限、日時形式チェック
- [ ] EventPolicy 作成
  - [ ] update(): 作成者のみ編集可能
  - [ ] delete(): 作成者のみ削除可能

---

### ステップ3: 参加機能（3-4日）

#### 実装内容

1. イベント詳細ページ（公開URL）
   - タイトル、日時、場所、説明表示
   - 参加者リスト表示
   - 参加者数と定員表示
2. 参加登録
   - ゲスト参加フォーム
   - SNSログインボタン
   - アカウント登録必須の場合のリダイレクト
3. 重複チェック・定員チェック
   - トランザクション + ロック
4. 参加者リスト表示
   - すべてのユーザーが閲覧可能
   - 主催者は参加経路も表示

#### 成果物

- イベントURLで詳細が見られる
- 参加登録ができる（ゲスト/SNS/アカウント）
- 参加者リストが表示される
- 定員オーバーが防止される

#### 実装タスク

- [ ] イベント詳細画面（Blade）
  - [ ] イベント情報表示
  - [ ] 参加者リスト表示
  - [ ] 参加フォーム（ゲスト参加OKの場合）
  - [ ] SNSログインボタン（SNSログイン必須の場合）
  - [ ] ログイン誘導（アカウント登録必須の場合）
- [ ] ParticipantController 作成
  - [ ] store() - 参加登録処理
  - [ ] 参加方法による条件分岐
  - [ ] バリデーション
  - [ ] トランザクション + ロック処理
- [ ] 参加登録ロジック実装
  - [ ] ゲスト参加: guest_name、guest_email を保存
  - [ ] SNSログイン参加: user_id、joined_via を保存
  - [ ] アカウント参加: user_id、joined_via を保存
- [ ] participation_token生成
  - [ ] 128bit（32文字）のランダム文字列
- [ ] 定員チェック処理
  - [ ] 現在の参加者数をカウント
  - [ ] 定員に達している場合はエラー
- [ ] 重複チェック処理
  - [ ] DB制約で対応済み
  - [ ] エラーハンドリング実装

---

### ステップ4: キャンセル・管理機能（2-3日）

#### 実装内容

1. 参加キャンセル機能
   - ゲスト: メールのキャンセルURL
   - ログインユーザー: マイページから
2. 参加者の強制削除（主催者権限）
   - 参加者管理画面
   - 削除ボタン
3. メール通知
   - 参加確認メール
   - キャンセル通知メール

#### 成果物

- 参加をキャンセルできる
- 主催者が参加者を削除できる
- メール通知が届く

#### 実装タスク

- [ ] キャンセル機能実装
  - [ ] キャンセル画面（Blade）
  - [ ] ParticipantController::cancel() - キャンセル処理
  - [ ] participation_tokenで本人確認
  - [ ] status='cancelled'、cancelled_at記録
- [ ] マイページ（参加者用）実装
  - [ ] 参加中のイベント一覧表示
  - [ ] キャンセルボタン
- [ ] 参加者管理画面（主催者用）実装
  - [ ] 参加者リスト表示（詳細版）
  - [ ] 参加経路、参加日時表示
  - [ ] 削除ボタン
  - [ ] 確認ダイアログ
- [ ] 参加者削除機能実装
  - [ ] ParticipantController::destroy()
  - [ ] 主催者のみ実行可能（Policy）
  - [ ] 物理削除
- [ ] メール通知実装
  - [ ] ParticipationConfirmedMail（参加確認）
  - [ ] ParticipantCancelledMail（キャンセル通知）
  - [ ] Mailableクラス作成
  - [ ] Bladeテンプレート作成
- [ ] メール送信処理
  - [ ] 参加登録完了時に送信
  - [ ] キャンセル時に送信（主催者へ）

---

### ステップ5: 仕上げ（2-3日）

#### 実装内容

1. バリデーションの強化
   - エラーメッセージの日本語化
   - フロントエンドバリデーション（Alpine.js）
2. エラーハンドリング
   - 404、403、500エラーページ
   - ユーザーフレンドリーなエラーメッセージ
3. UI/UXの調整
   - レスポンシブデザイン確認
   - ローディング状態の表示
   - フラッシュメッセージ
4. テスト
   - 手動テスト（各機能の動作確認）
   - または自動テスト（PHPUnit）

#### 成果物

- エラーが適切に表示される
- UIが洗練されている
- 基本的な動作が安定している
- レスポンシブデザイン対応

#### 実装タスク

- [ ] バリデーションメッセージの日本語化
  - [ ] resources/lang/ja/validation.php 作成
- [ ] エラーページ作成
  - [ ] 404.blade.php
  - [ ] 403.blade.php
  - [ ] 500.blade.php
- [ ] フラッシュメッセージ実装
  - [ ] 成功メッセージ（緑色）
  - [ ] エラーメッセージ（赤色）
  - [ ] 警告メッセージ（黄色）
- [ ] レスポンシブデザイン確認
  - [ ] スマホ表示の確認
  - [ ] タブレット表示の確認
- [ ] ローディング状態の実装
  - [ ] フォーム送信時のローディングスピナー
  - [ ] ボタンの二重クリック防止
- [ ] 手動テスト
  - [ ] ユーザー登録・ログイン
  - [ ] SNSログイン（Google、GitHub、LINE）
  - [ ] イベント作成・編集・削除
  - [ ] ゲスト参加
  - [ ] SNSログイン参加
  - [ ] アカウント参加
  - [ ] キャンセル
  - [ ] 参加者削除
  - [ ] メール送信
  - [ ] 定員管理
  - [ ] 重複防止
  - [ ] 権限チェック
- [ ] （オプション）自動テスト作成
  - [ ] Feature Test（機能テスト）
  - [ ] Unit Test（単体テスト）

---

## 開発期間の見積もり

| フェーズ  | 内容             | 想定工数    |
| --------- | ---------------- | ----------- |
| ステップ1 | 基盤構築         | 2-3日       |
| ステップ2 | イベント機能     | 2-3日       |
| ステップ3 | 参加機能         | 3-4日       |
| ステップ4 | キャンセル・管理 | 2-3日       |
| ステップ5 | 仕上げ           | 2-3日       |
| **合計**  |                  | **11-16日** |

※ 1日 = 4-6時間の作業想定
※ Laravel/SNSログインの経験があれば短縮可能

---

## 環境設定

### 開発環境の構築

#### 必要なソフトウェア

- PHP 8.2以上
- Composer
- MySQL 8.0以上
- Node.js（Tailwind CSSのビルド用）
- Git

#### Laravelプロジェクトのセットアップ

```bash
# Laravelプロジェクト作成
composer create-project laravel/laravel event-manager

cd event-manager

# Laravel Breeze インストール
composer require laravel/breeze --dev
php artisan breeze:install blade

# Laravel Socialite インストール
composer require laravel/socialite

# データベースマイグレーション
php artisan migrate

# 開発サーバー起動
php artisan serve
```

### .env設定例

```env
APP_NAME="イベント管理ツール"
APP_ENV=local
APP_DEBUG=true
APP_URL=http://localhost:8000

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=event_manager
DB_USERNAME=root
DB_PASSWORD=

MAIL_MAILER=smtp
MAIL_HOST=smtp.mailtrap.io
MAIL_PORT=2525
MAIL_USERNAME=your_mailtrap_username
MAIL_PASSWORD=your_mailtrap_password
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS=noreply@example.com
MAIL_FROM_NAME="${APP_NAME}"

# Google OAuth
GOOGLE_CLIENT_ID=your_google_client_id
GOOGLE_CLIENT_SECRET=your_google_client_secret
GOOGLE_REDIRECT_URI=http://localhost:8000/auth/google/callback

# GitHub OAuth
GITHUB_CLIENT_ID=your_github_client_id
GITHUB_CLIENT_SECRET=your_github_client_secret
GITHUB_REDIRECT_URI=http://localhost:8000/auth/github/callback

# LINE Login
LINE_CLIENT_ID=your_line_client_id
LINE_CLIENT_SECRET=your_line_client_secret
LINE_REDIRECT_URI=http://localhost:8000/auth/line/callback
```

### OAuth アプリケーションの登録

#### Google Cloud Console

1. https://console.cloud.google.com/ にアクセス
2. プロジェクトを作成
3. 「APIとサービス」→「認証情報」
4. 「認証情報を作成」→「OAuthクライアントID」
5. アプリケーションの種類: ウェブアプリケーション
6. 承認済みのリダイレクトURI: `http://localhost:8000/auth/google/callback`
7. クライアントIDとクライアントシークレットをコピー

#### GitHub Developer Settings

1. https://github.com/settings/developers にアクセス
2. 「New OAuth App」をクリック
3. Application name: イベント管理ツール（開発）
4. Homepage URL: `http://localhost:8000`
5. Authorization callback URL: `http://localhost:8000/auth/github/callback`
6. クライアントIDとクライアントシークレットをコピー

#### LINE Developers

1. https://developers.line.biz/ にアクセス
2. プロバイダーを作成
3. チャネルを作成（LINE Login）
4. Callback URL: `http://localhost:8000/auth/line/callback`
5. Channel IDとChannel Secretをコピー

---

## テスト項目

### 機能テスト（手動）

#### ユーザー管理

- [ ] メールアドレス + パスワードでユーザー登録
- [ ] ログイン/ログアウト
- [ ] パスワードリセット
- [ ] Googleアカウントでログイン
- [ ] GitHubアカウントでログイン
- [ ] LINEアカウントでログイン

#### イベント管理

- [ ] イベント作成（ゲスト参加OK）
- [ ] イベント作成（SNSログイン必須）
- [ ] イベント作成（アカウント登録必須）
- [ ] イベント編集
- [ ] イベント削除
- [ ] イベント一覧表示

#### 参加管理

- [ ] ゲスト参加登録
- [ ] Googleログイン参加
- [ ] GitHubログイン参加
- [ ] LINEログイン参加
- [ ] アカウントログイン参加
- [ ] 参加者リスト表示
- [ ] ゲストの参加キャンセル
- [ ] ログインユーザーの参加キャンセル
- [ ] 主催者による参加者削除

#### バリデーション

- [ ] 必須項目の入力チェック
- [ ] メールアドレス形式チェック
- [ ] 重複参加のチェック（ゲスト）
- [ ] 重複参加のチェック（ログインユーザー）
- [ ] 定員オーバーのチェック

#### セキュリティ

- [ ] 他人のイベントを編集できないか
- [ ] 他人のイベントを削除できないか
- [ ] 他人の参加者を削除できないか
- [ ] CSRF攻撃の防止
- [ ] XSS攻撃の防止

#### メール

- [ ] 参加確認メールの送信
- [ ] キャンセル通知メールの送信
- [ ] メールの内容確認

---

## デプロイ・運用

### 本番環境の準備

#### 必要なもの

- [ ] ドメイン（例: example.com）
- [ ] SSL証明書（Let's Encrypt推奨）
- [ ] データベース（MySQL）
- [ ] メールサーバー（SendGrid、Mailgun、AWS SES）
- [ ] OAuth アプリケーション（本番用に再登録）

### デプロイ手順（例: VPS）

1. サーバーにSSH接続
2. 必要なソフトウェアをインストール（PHP、MySQL、Nginx/Apache）
3. Gitでコードをクローン
4. `composer install --optimize-autoloader --no-dev`
5. `.env` ファイルを本番用に設定
6. `php artisan key:generate`
7. `php artisan migrate --force`
8. `php artisan config:cache`
9. `php artisan route:cache`
10. `php artisan view:cache`
11. Webサーバーの設定（Nginx/Apache）
12. SSL証明書の設定（Let's Encrypt）
13. 動作確認

### 本番環境の .env 設定例

```env
APP_NAME="イベント管理ツール"
APP_ENV=production
APP_DEBUG=false
APP_URL=https://your-domain.com

DB_CONNECTION=mysql
DB_HOST=127.0.0.1
DB_PORT=3306
DB_DATABASE=event_manager_prod
DB_USERNAME=prod_user
DB_PASSWORD=secure_password

MAIL_MAILER=smtp
MAIL_HOST=smtp.sendgrid.net
MAIL_PORT=587
MAIL_USERNAME=apikey
MAIL_PASSWORD=your_sendgrid_api_key
MAIL_ENCRYPTION=tls
MAIL_FROM_ADDRESS=noreply@your-domain.com
MAIL_FROM_NAME="${APP_NAME}"

# Google OAuth（本番用）
GOOGLE_CLIENT_ID=your_production_google_client_id
GOOGLE_CLIENT_SECRET=your_production_google_client_secret
GOOGLE_REDIRECT_URI=https://your-domain.com/auth/google/callback

# GitHub OAuth（本番用）
GITHUB_CLIENT_ID=your_production_github_client_id
GITHUB_CLIENT_SECRET=your_production_github_client_secret
GITHUB_REDIRECT_URI=https://your-domain.com/auth/github/callback

# LINE Login（本番用）
LINE_CLIENT_ID=your_production_line_client_id
LINE_CLIENT_SECRET=your_production_line_client_secret
LINE_REDIRECT_URI=https://your-domain.com/auth/line/callback
```

---

## MVP完成後の次のステップ

### フェーズ2: 機能拡張

- [ ] 承認制イベント
  - [ ] 参加申請機能
  - [ ] 主催者の承認/拒否画面
  - [ ] 承認通知メール
- [ ] 共同管理者機能
  - [ ] 管理者の追加・削除
  - [ ] 管理者権限の設定
- [ ] イベント検索・一覧
  - [ ] 公開イベントの検索
  - [ ] カテゴリ別表示
- [ ] コメント機能
  - [ ] イベントページでのコメント
  - [ ] 返信機能
- [ ] リマインドメール
  - [ ] イベント前日通知
  - [ ] イベント当日通知

### フェーズ3: UX改善

- [ ] カレンダー表示
  - [ ] 月間カレンダー
  - [ ] イベントのカレンダー表示
- [ ] 参加履歴
  - [ ] 過去に参加したイベント一覧
  - [ ] 参加回数のバッジ
- [ ] SNSシェアボタン
  - [ ] Twitter、Facebookでシェア
  - [ ] OGP設定
- [ ] イベントのサムネイル画像
  - [ ] 画像アップロード機能
  - [ ] 画像リサイズ
- [ ] タイムゾーン対応
  - [ ] ユーザーのタイムゾーン設定
  - [ ] 日時の自動変換

---

## まとめ

### このMVPで実現できること

✅ イベントを作成して共有できる
✅ 参加方法を柔軟に選べる（ゲスト/SNS/アカウント）
✅ 参加者を管理できる
✅ いたずらをある程度防げる
✅ カジュアルにもしっかりとも使える

### このMVPでできないこと（後回し）

❌ 承認制（申請 → 承認フロー）
❌ コメント機能
❌ イベント検索
❌ カレンダー表示
❌ リマインドメール

### 開発のポイント

1. **段階的に実装する**: ステップ1から順番に進める
2. **動作確認を都度行う**: 各ステップ完了時にテストする
3. **セキュリティを優先する**: バリデーションと権限チェックを忘れずに
4. **ユーザー体験を意識する**: エラーメッセージやローディング状態を丁寧に

### 成功の定義（MVP完成時）

- [ ] すべての機能が動作する
- [ ] セキュリティ上の問題がない
- [ ] 実際に使ってもらえるレベルの完成度
- [ ] フィードバックを収集できる状態

---

**作成日**: 2026年2月1日
**バージョン**: 1.0
