Milestone 1: MVP

Issue #1: プロジェクトのセットアップ

## 概要

Laravelプロジェクトのセットアップと開発環境の構築

## タスク

- [ ] Laravelプロジェクト作成
- [ ] .env設定（データベース、メール）
- [ ] Gitリポジトリ初期化
- [ ] README.md作成

## 受け入れ基準

- ローカル環境でLaravelが起動する
- データベース接続が成功する

## 備考

- Laravel 11.x を使用
- PHP 8.2以上

## Labels

`priority: high`, `type: feature`, `area: backend`

## Milestone

MVP

Issue #2: データベース設計とマイグレーション作成

## 概要

データベーステーブルの設計とマイグレーションファイルの作成

## タスク

- [ ] usersテーブルのマイグレーション作成
- [ ] social_accountsテーブルのマイグレーション作成
- [ ] eventsテーブルのマイグレーション作成
- [ ] participantsテーブルのマイグレーション作成
- [ ] マイグレーション実行確認

## 受け入れ基準

- すべてのテーブルが正しく作成される
- リレーションシップが正しく設定されている
- UNIQUE制約、INDEX、外部キーが正しく設定されている

## 備考

設計書: `01_requirements.md` のデータベース設計を参照

## Labels

`priority: high`, `type: feature`, `area: database`

## Milestone

MVP

Issue #3: モデルとリレーションシップの定義

## 概要

Eloquentモデルの作成とリレーションシップの定義

## タスク

- [ ] Userモデル作成
- [ ] SocialAccountモデル作成
- [ ] Eventモデル作成
- [ ] Participantモデル作成
- [ ] リレーションシップ定義
  - User hasMany Events
  - User hasMany SocialAccounts
  - User hasMany Participants
  - Event hasMany Participants
  - Event belongsTo User

## 受け入れ基準

- すべてのモデルが作成されている
- リレーションシップが正しく動作する
- tinkerで関連データを取得できる

## Labels

`priority: high`, `type: feature`, `area: backend`

## Milestone

MVP

Issue #4: 認証機能（Laravel Breeze）のセットアップ

## 概要

Laravel Breezeを使用した基本的な認証機能の実装

## タスク

- [ ] Laravel Breeze インストール
- [ ] Blade + Tailwind CSS の選択
- [ ] 認証画面のカスタマイズ（必要に応じて）
- [ ] ログイン/ログアウト機能の確認
- [ ] ユーザー登録機能の確認

## 受け入れ基準

- ユーザー登録ができる
- ログイン/ログアウトができる
- 認証ミドルウェアが正しく動作する

## 備考

- パスワードリセット機能は後回し（フェーズ2）

## Labels

`priority: high`, `type: feature`, `area: backend`, `area: frontend`

## Milestone

MVP

Issue #5: SNSログイン（Laravel Socialite）のセットアップ

## 概要

Google、GitHub、LINEでのSNSログイン機能の実装

## タスク

- [ ] Laravel Socialite インストール
- [ ] config/services.php に各プロバイダーの設定追加
- [ ] Google OAuth アプリケーション登録
- [ ] GitHub OAuth アプリケーション登録
- [ ] LINE Login アプリケーション登録
- [ ] SocialAuthController 作成
- [ ] ルート設定（/auth/{provider}, /auth/{provider}/callback）
- [ ] SNSログインボタンの実装（Blade）

## 受け入れ基準

- Googleアカウントでログインできる
- GitHubアカウントでログインできる
- LINEアカウントでログインできる
- 初回ログイン時に自動でユーザー作成される
- 2回目以降は既存ユーザーとしてログインできる

## 備考

開発環境用の.envに各プロバイダーのクライアントIDとシークレットを設定

## Labels

`priority: high`, `type: feature`, `area: backend`

## Milestone

MVP

Issue #6: イベント作成機能

## 概要

イベント作成フォームと保存処理の実装

## タスク

- [ ] EventController::create() - 作成画面表示
- [ ] EventController::store() - イベント保存処理
- [ ] イベント作成フォーム（Blade）
  - [ ] タイトル入力
  - [ ] 説明入力（textarea）
  - [ ] 日時選択
  - [ ] 場所入力
  - [ ] 参加方法選択（ラジオボタン）
  - [ ] SNS選択（チェックボックス、SNSログイン必須の場合のみ表示）
  - [ ] 定員入力（任意）
- [ ] slug自動生成ロジック
- [ ] EventRequest バリデーション作成
- [ ] ルート設定

## 受け入れ基準

- イベント作成フォームが表示される
- すべての項目を入力して送信できる
- バリデーションエラーが正しく表示される
- 一意なslugが自動生成される
- eventsテーブルにデータが保存される

## 備考

slug生成: 8文字のランダム英数字、重複チェック付き

## Labels

`priority: high`, `type: feature`, `area: backend`, `area: frontend`

## Milestone

MVP

Issue #7: イベント編集機能

## 概要

イベント編集フォームと更新処理の実装

## タスク

- [ ] EventController::edit() - 編集画面表示
- [ ] EventController::update() - イベント更新処理
- [ ] イベント編集フォーム（Blade）
- [ ] EventPolicy 作成（作成者のみ編集可能）
- [ ] バリデーション実装
- [ ] ルート設定

## 受け入れ基準

- 作成者のみイベント編集画面にアクセスできる
- 他のユーザーがアクセスすると403エラー
- すべての項目を編集できる（slugは変更不可）
- バリデーションエラーが正しく表示される
- eventsテーブルが正しく更新される

## Labels

`priority: high`, `type: feature`, `area: backend`, `area: frontend`

## Milestone

MVP

Issue #8: イベント削除機能

## 概要

イベント削除（物理削除）機能の実装

## タスク

- [ ] EventController::destroy() - イベント削除処理
- [ ] 削除ボタンの実装（Blade）
- [ ] 確認ダイアログの実装（JavaScript）
- [ ] EventPolicy に delete メソッド追加（作成者のみ削除可能）
- [ ] ルート設定

## 受け入れ基準

- 作成者のみイベント削除ができる
- 削除前に確認ダイアログが表示される
- eventsテーブルからデータが物理削除される
- 削除後、イベント一覧画面にリダイレクトされる

## 備考

物理削除で実装（論理削除はフェーズ2）

## Labels

`priority: high`, `type: feature`, `area: backend`, `area: frontend`

## Milestone

MVP

Issue #9: イベント一覧（自分が作成したもの）

## 概要

自分が作成したイベントの一覧表示

## タスク

- [ ] EventController::index() - 一覧表示
- [ ] イベント一覧画面（Blade）
  - [ ] タイトル、日時、参加者数、定員を表示
  - [ ] 編集・削除ボタンを表示
  - [ ] 「新しいイベントを作成」ボタン
- [ ] ルート設定

## 受け入れ基準

- ログインユーザーが作成したイベントのみ表示される
- 各イベントの情報が正しく表示される
- 編集・削除ボタンが機能する

## Labels

`priority: medium`, `type: feature`, `area: backend`, `area: frontend`

## Milestone

MVP

Issue #10: イベントのプレビュー機能

## 概要

イベント作成・編集時に、参加者視点でのプレビューを表示

## タスク

- [ ] プレビューボタンの実装（編集画面）
- [ ] プレビュー表示（モーダルまたは別タブ）
- [ ] 参加者視点での表示確認

## 受け入れ基準

- イベント作成・編集画面から「プレビュー」ボタンをクリックできる
- 参加者が見る画面と同じ表示がプレビューされる

## Labels

`priority: medium`, `type: feature`, `area: frontend`

## Milestone

MVP

Issue #11: イベント詳細ページ（公開URL）

## 概要

イベント詳細ページの実装（/events/{slug}）

## タスク

- [ ] EventController::show() - 詳細表示
- [ ] イベント詳細画面（Blade）
  - [ ] タイトル、日時、場所、説明を表示
  - [ ] 参加者リスト表示
  - [ ] 現在の参加者数と定員を表示
  - [ ] 参加フォームまたはログインボタン（イベントタイプによる）
- [ ] ルート設定

## 受け入れ基準

- URLを知っていれば誰でもアクセスできる
- イベント情報が正しく表示される
- 参加者リストが表示される
- イベントタイプに応じた参加方法が表示される

## Labels

`priority: high`, `type: feature`, `area: backend`, `area: frontend`

## Milestone

MVP

Issue #12: 参加登録機能（ゲスト参加）

## 概要

ゲスト参加（名前+メールアドレス）での参加登録機能

## タスク

- [ ] ParticipantController::store() - 参加登録処理
- [ ] ゲスト参加フォーム（Blade）
  - [ ] 名前入力
  - [ ] メールアドレス入力
  - [ ] 「参加する」ボタン
- [ ] バリデーション実装
- [ ] 重複チェック（event_id + guest_email）
- [ ] 定員チェック（トランザクション + ロック）
- [ ] participation_token生成
- [ ] ルート設定

## 受け入れ基準

- 名前とメールアドレスを入力して参加登録できる
- 同じメールアドレスで重複登録できない
- 定員に達している場合はエラーメッセージが表示される
- participantsテーブルにデータが保存される
- 参加確認メールが送信される

## Labels

`priority: high`, `type: feature`, `area: backend`, `area: frontend`

## Milestone

MVP

Issue #13: 参加登録機能（SNSログイン必須）

## 概要

SNSログインが必須のイベントへの参加登録機能

## タスク

- [ ] ParticipantController::store() に条件分岐追加
- [ ] SNSログインボタンの表示（Blade）
- [ ] 許可されたSNSのみ表示
- [ ] ログイン後の参加登録処理
- [ ] バリデーション実装
- [ ] 重複チェック（event_id + user_id）
- [ ] 定員チェック
- [ ] ルート設定

## 受け入れ基準

- 許可されたSNSのログインボタンのみ表示される
- ログイン後、参加登録ができる
- 同じユーザーで重複登録できない
- 定員に達している場合はエラーメッセージが表示される
- participantsテーブルにデータが保存される（user_id）
- 参加確認メールが送信される

## Labels

`priority: high`, `type: feature`, `area: backend`, `area: frontend`

## Milestone

MVP

Issue #14: 参加登録機能（アカウント登録必須）

## 概要

アカウント登録が必須のイベントへの参加登録機能

## タスク

- [ ] ParticipantController::store() に条件分岐追加
- [ ] 未ログイン時のリダイレクト処理
- [ ] ログイン後の参加登録処理
- [ ] バリデーション実装
- [ ] 重複チェック（event_id + user_id）
- [ ] 定員チェック
- [ ] ルート設定

## 受け入れ基準

- 未ログインの場合、ログイン画面にリダイレクトされる
- ログイン後、イベント詳細ページに戻る
- 参加登録ができる
- 同じユーザーで重複登録できない
- 定員に達している場合はエラーメッセージが表示される
- participantsテーブルにデータが保存される
- 参加確認メールが送信される

## Labels

`priority: high`, `type: feature`, `area: backend`, `area: frontend`

## Milestone

MVP

Issue #15: 参加者リスト表示

## 概要

イベント詳細ページでの参加者リスト表示

## タスク

- [ ] 参加者リストの表示（Blade）
  - [ ] 参加者の名前
  - [ ] 参加日時
  - [ ] 参加経路（主催者のみ表示）
- [ ] 参加者数のカウント表示
- [ ] 定員との比較表示（例: 12/50人）

## 受け入れ基準

- すべてのユーザーが参加者リストを閲覧できる
- 主催者のみ参加経路が表示される
- 現在の参加者数が正しく表示される

## Labels

`priority: high`, `type: feature`, `area: frontend`

## Milestone

MVP

Issue #16: 参加キャンセル機能（ゲスト）

## 概要

ゲスト参加者のキャンセル機能

## タスク

- [ ] ParticipantController::cancel() - キャンセル処理
- [ ] キャンセル画面（Blade）
- [ ] participation_tokenで本人確認
- [ ] status='cancelled'、cancelled_at記録
- [ ] キャンセル通知メール（主催者へ）
- [ ] ルート設定（/events/{slug}/cancel/{token}）

## 受け入れ基準

- メールのキャンセルURLからアクセスできる
- participation_tokenで本人確認される
- キャンセルボタンをクリックするとキャンセルされる
- participantsテーブルのstatusが'cancelled'に更新される
- 主催者にキャンセル通知メールが送信される

## Labels

`priority: high`, `type: feature`, `area: backend`, `area: frontend`

## Milestone

MVP

Issue #17: 参加キャンセル機能（ログインユーザー）

## 概要

ログインユーザーのキャンセル機能

## タスク

- [ ] マイページの作成
- [ ] 参加中のイベント一覧表示
- [ ] キャンセルボタンの実装
- [ ] ParticipantController::cancel() でログインユーザー対応
- [ ] キャンセル通知メール（主催者へ）
- [ ] ルート設定

## 受け入れ基準

- マイページで参加中のイベント一覧が表示される
- キャンセルボタンをクリックするとキャンセルされる
- participantsテーブルのstatusが'cancelled'に更新される
- 主催者にキャンセル通知メールが送信される

## Labels

`priority: high`, `type: feature`, `area: backend`, `area: frontend`

## Milestone

MVP

Issue #18: 参加者管理画面（主催者専用）

## 概要

主催者が参加者を管理できる画面

## タスク

- [ ] EventController::participants() - 参加者管理画面表示
- [ ] 参加者管理画面（Blade）
  - [ ] 参加者リスト（詳細版）
  - [ ] 参加者の名前、参加経路、参加日時
  - [ ] 削除ボタン
- [ ] EventPolicy に viewParticipants メソッド追加
- [ ] ルート設定

## 受け入れ基準

- 主催者のみアクセスできる
- 他のユーザーがアクセスすると403エラー
- 参加者の詳細情報が表示される
- 削除ボタンが表示される

## Labels

`priority: medium`, `type: feature`, `area: backend`, `area: frontend`

## Milestone

MVP

Issue #19: 参加者の強制削除（主催者権限）

## 概要

主催者が参加者を強制削除できる機能

## タスク

- [ ] ParticipantController::destroy() - 参加者削除処理
- [ ] 削除ボタンの実装（Blade）
- [ ] 確認ダイアログの実装（JavaScript）
- [ ] ParticipantPolicy 作成（主催者のみ削除可能）
- [ ] ルート設定

## 受け入れ基準

- 主催者のみ参加者削除ができる
- 削除前に確認ダイアログが表示される
- participantsテーブルからデータが物理削除される
- 削除後、参加者管理画面にリダイレクトされる

## Labels

`priority: medium`, `type: feature`, `area: backend`, `area: frontend`

## Milestone

MVP

Issue #20: メール通知（参加確認）

## 概要

参加登録完了時の参加確認メール

## タスク

- [ ] ParticipationConfirmedMail Mailable作成
- [ ] メールテンプレート（Blade）
  - [ ] イベント詳細（タイトル、日時、場所）
  - [ ] イベント詳細ページのURL
  - [ ] キャンセル用URL（ゲストのみ）
- [ ] メール送信処理（ParticipantController::store()）

## 受け入れ基準

- 参加登録完了時にメールが送信される
- メール内容が正しい
- キャンセル用URLが機能する（ゲストのみ）

## Labels

`priority: high`, `type: feature`, `area: email`

## Milestone

MVP

Issue #21: メール通知（キャンセル通知）

## 概要

参加キャンセル時の主催者への通知メール

## タスク

- [ ] ParticipantCancelledMail Mailable作成
- [ ] メールテンプレート（Blade）
  - [ ] イベント名
  - [ ] キャンセルした参加者の名前
  - [ ] キャンセル日時
  - [ ] 現在の参加者数
- [ ] メール送信処理（ParticipantController::cancel()）

## 受け入れ基準

- 参加キャンセル時に主催者にメールが送信される
- メール内容が正しい

## Labels

`priority: high`, `type: feature`, `area: email`

## Milestone

MVP

Issue #22: バリデーションメッセージの日本語化

## 概要

バリデーションエラーメッセージの日本語化

## タスク

- [ ] resources/lang/ja/validation.php 作成
- [ ] すべてのバリデーションメッセージを日本語に翻訳
- [ ] カスタムバリデーションメッセージの追加

## 受け入れ基準

- すべてのバリデーションエラーが日本語で表示される
- エラーメッセージが自然な日本語である

## Labels

`priority: medium`, `type: enhancement`, `area: frontend`

## Milestone

MVP

Issue #23: エラーページの作成

## 概要

カスタムエラーページの作成

## タスク

- [ ] 404エラーページ（resources/views/errors/404.blade.php）
- [ ] 403エラーページ（resources/views/errors/403.blade.php）
- [ ] 500エラーページ（resources/views/errors/500.blade.php）
- [ ] Tailwind CSSでスタイリング

## 受け入れ基準

- 各エラーページが正しく表示される
- ユーザーフレンドリーなメッセージが表示される
- デザインが統一されている

## Labels

`priority: medium`, `type: feature`, `area: frontend`

## Milestone

MVP

Issue #24: フラッシュメッセージの実装

## 概要

成功・エラー・警告メッセージの表示

## タスク

- [ ] フラッシュメッセージコンポーネント作成（Blade）
- [ ] 成功メッセージ（緑色）
- [ ] エラーメッセージ（赤色）
- [ ] 警告メッセージ（黄色）
- [ ] Alpine.js で自動非表示（5秒後）

## 受け入れ基準

- すべてのアクション後にフラッシュメッセージが表示される
- メッセージが5秒後に自動で消える
- デザインが統一されている

## Labels

`priority: medium`, `type: feature`, `area: frontend`

## Milestone

MVP

Issue #25: レスポンシブデザインの確認

## 概要

スマホ・タブレット表示の確認と調整

## タスク

- [ ] スマホ表示の確認（iPhone、Android）
- [ ] タブレット表示の確認（iPad）
- [ ] PC表示の確認
- [ ] レイアウト崩れの修正
- [ ] ボタンのタップ領域の調整

## 受け入れ基準

- すべてのデバイスで正しく表示される
- スマホでも操作しやすい

## Labels

`priority: medium`, `type: enhancement`, `area: frontend`

## Milestone

MVP

Issue #26: ローディング状態の実装

## 概要

フォーム送信時のローディングスピナー表示

## タスク

- [ ] ローディングスピナーコンポーネント作成
- [ ] フォーム送信時にスピナー表示（Alpine.js）
- [ ] ボタンの二重クリック防止
- [ ] 送信中はボタンを無効化

## 受け入れ基準

- フォーム送信時にローディングスピナーが表示される
- 送信中はボタンをクリックできない
- 送信完了後、スピナーが消える

## Labels

`priority: low`, `type: enhancement`, `area: frontend`

## Milestone

MVP

Issue #27: 手動テスト（全機能）

## 概要

すべての機能の手動テスト

## テスト項目

### ユーザー管理

- [ ] メールアドレス + パスワードでユーザー登録
- [ ] ログイン/ログアウト
- [ ] Googleアカウントでログイン
- [ ] GitHubアカウントでログイン
- [ ] LINEアカウントでログイン

### イベント管理

- [ ] イベント作成（ゲスト参加OK）
- [ ] イベント作成（SNSログイン必須）
- [ ] イベント作成（アカウント登録必須）
- [ ] イベント編集
- [ ] イベント削除
- [ ] イベント一覧表示
- [ ] イベントプレビュー

### 参加管理

- [ ] ゲスト参加登録
- [ ] Googleログイン参加
- [ ] GitHubログイン参加
- [ ] LINEログイン参加
- [ ] アカウントログイン参加
- [ ] 参加者リスト表示
- [ ] ゲストの参加キャンセル
- [ ] ログインユーザーの参加キャンセル
- [ ] 主催者による参加者削除

### バリデーション

- [ ] 必須項目の入力チェック
- [ ] メールアドレス形式チェック
- [ ] 重複参加のチェック（ゲスト）
- [ ] 重複参加のチェック（ログインユーザー）
- [ ] 定員オーバーのチェック

### セキュリティ

- [ ] 他人のイベントを編集できないか
- [ ] 他人のイベントを削除できないか
- [ ] 他人の参加者を削除できないか

### メール

- [ ] 参加確認メールの送信
- [ ] キャンセル通知メールの送信
- [ ] メールの内容確認

## 受け入れ基準

- すべてのテスト項目がパスする
- バグや不具合が見つかった場合は、個別にIssueを作成

## Labels

`priority: high`, `type: documentation`

## Milestone

MVP

Milestone 2: フェーズ1.5（承認制追加）

Issue #28: イベント作成時に承認制オプション追加

## 概要

イベント作成時に「承認制」を選択できるようにする

## タスク

- [ ] イベント作成フォームに「承認制」チェックボックス追加
- [ ] eventsテーブルに requires_approval カラム追加（マイグレーション）
- [ ] EventRequest バリデーション更新
- [ ] Eventモデル更新

## 受け入れ基準

- イベント作成時に承認制を選択できる
- eventsテーブルに requires_approval が保存される

## Labels

`priority: high`, `type: feature`, `area: backend`, `area: frontend`

## Milestone

フェーズ1.5

Issue #29: 参加申請機能

## 概要

承認制イベントへの参加申請機能

## タスク

- [ ] 参加申請フォーム作成（Blade）
  - [ ] 名前入力（ゲストの場合）
  - [ ] メールアドレス入力（ゲストの場合）
  - [ ] メッセージ入力（任意）
- [ ] ParticipantController::store() に承認制の条件分岐追加
- [ ] status='pending' で保存
- [ ] 主催者に申請通知メール送信

## 受け入れ基準

- 承認制イベントで参加申請ができる
- participantsテーブルに status='pending' で保存される
- 主催者に申請通知メールが送信される

## Labels

`priority: high`, `type: feature`, `area: backend`, `area: frontend`

## Milestone

フェーズ1.5

Issue #30: 承認管理画面（主催者専用）

## 概要

主催者が参加申請を承認/拒否できる画面

## タスク

- [ ] EventController::approvals() - 承認管理画面表示
- [ ] 承認管理画面（Blade）
  - [ ] 申請中の参加者リスト
  - [ ] 参加者の名前、メッセージ、申請日時
  - [ ] 承認ボタン、拒否ボタン
- [ ] EventPolicy に viewApprovals メソッド追加
- [ ] ルート設定

## 受け入れ基準

- 主催者のみアクセスできる
- 申請中の参加者リストが表示される
- 承認/拒否ボタンが表示される

## Labels

`priority: high`, `type: feature`, `area: backend`, `area: frontend`

## Milestone

フェーズ1.5

Issue #31: 承認/拒否処理

## 概要

主催者が参加申請を承認または拒否する処理

## タスク

- [ ] ParticipantController::approve() - 承認処理
- [ ] ParticipantController::reject() - 拒否処理
- [ ] participant_approvals テーブル作成（任意、履歴管理）
- [ ] status='approved' または 'rejected' に更新
- [ ] 承認通知メール（参加者へ）
- [ ] 拒否通知メール（参加者へ）
- [ ] ルート設定

## 受け入れ基準

- 承認ボタンをクリックすると status='approved' に更新される
- 拒否ボタンをクリックすると status='rejected' に更新される
- 参加者に承認/拒否通知メールが送信される

## Labels

`priority: high`, `type: feature`, `area: backend`

## Milestone

フェーズ1.5

Issue #32: メール通知（承認通知）

## 概要

参加申請が承認されたときの通知メール

## タスク

- [ ] ParticipantApprovedMail Mailable作成
- [ ] メールテンプレート（Blade）
  - [ ] イベント詳細
  - [ ] 承認されたメッセージ
  - [ ] イベント詳細ページのURL
- [ ] メール送信処理（ParticipantController::approve()）

## 受け入れ基準

- 承認時にメールが送信される
- メール内容が正しい

## Labels

`priority: high`, `type: feature`, `area: email`

## Milestone

フェーズ1.5

Issue #33: メール通知（拒否通知）

## 概要

参加申請が拒否されたときの通知メール

## タスク

- [ ] ParticipantRejectedMail Mailable作成
- [ ] メールテンプレート（Blade）
  - [ ] イベント名
  - [ ] 拒否されたメッセージ
  - [ ] 主催者からのメッセージ（任意）
- [ ] メール送信処理（ParticipantController::reject()）

## 受け入れ基準

- 拒否時にメールが送信される
- メール内容が正しい

## Labels

`priority: high`, `type: feature`, `area: email`

## Milestone

フェーズ1.5

Issue #34: 承認制機能のテスト

## 概要

承認制機能の手動テスト

## テスト項目

- [ ] 承認制イベントの作成
- [ ] 参加申請（ゲスト）
- [ ] 参加申請（SNSログイン）
- [ ] 参加申請（アカウント）
- [ ] 主催者による承認
- [ ] 主催者による拒否
- [ ] 承認通知メールの確認
- [ ] 拒否通知メールの確認
- [ ] 承認後の参加者リスト表示

## 受け入れ基準

- すべてのテスト項目がパスする

## Labels

`priority: high`, `type: documentation`

## Milestone

フェーズ1.5

Milestone 3: フェーズ2（機能拡張）

Issue #35: パスワードリセット機能

## 概要

パスワードを忘れた場合のリセット機能

## タスク

- [ ] パスワードリセット画面
- [ ] リセットメール送信処理
- [ ] パスワード再設定画面
- [ ] パスワード更新処理

## 受け入れ基準

- パスワードリセットメールが送信される
- メールのリンクから新しいパスワードを設定できる

## Labels

`priority: medium`, `type: feature`, `area: backend`, `area: frontend`

## Milestone

フェーズ2

Issue #36: コメント機能

## 概要

イベントページでのコメント機能

## タスク

- [ ] commentsテーブル作成
- [ ] コメント投稿機能
- [ ] コメント表示
- [ ] コメント削除（投稿者または主催者）

## 受け入れ基準

- イベントページでコメントを投稿できる
- コメントが時系列で表示される
- 投稿者または主催者がコメントを削除できる

## Labels

`priority: low`, `type: feature`, `area: backend`, `area: frontend`

## Milestone

フェーズ2

Issue #37: イベント検索機能

## 概要

公開イベントの検索機能

## タスク

- [ ] 検索フォーム作成
- [ ] 検索処理（タイトル、場所、カテゴリ）
- [ ] 検索結果表示
- [ ] ページネーション

## 受け入れ基準

- キーワードでイベントを検索できる
- 検索結果が正しく表示される

## Labels

`priority: low`, `type: feature`, `area: backend`, `area: frontend`

## Milestone

フェーズ2
