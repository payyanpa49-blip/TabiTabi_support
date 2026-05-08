# TabiTabi ユーザープライバシー整理

最終更新日: 2026-05-08

このファイルは、TabiTabi のユーザープライバシーに関する実装内容を整理し、App Store Connect の「App Privacy」回答や審査対応時の確認資料として使うためのものです。公開用の正式文面は `PRIVACY_POLICY.md` を参照してください。

## 基本方針

- TabiTabi は、広告配信やユーザー追跡を目的としたデータ収集を行いません。
- 収集・保存する情報は、アカウント管理、旅行データ管理、スケジュール管理、GPSログ記録、共有機能、サポート対応のために使用します。
- 旅行写真、スケジュール写真、QR画像は、現時点のMVPでは端末内に保存します。Firebase Storage への写真アップロードは無効です。
- GPSログは、ユーザーがGPSログ機能を開始した場合のみ記録します。位置情報の許可は iOS の権限設定に従います。
- ユーザーが作成した旅行・予定・QR・GPS共有データは、Firebase Authentication のユーザーIDと関連付けて管理します。

## 取り扱うデータ

| データ種別 | 具体例 | 保存場所 | 利用目的 | ユーザーとの関連付け | トラッキング |
| --- | --- | --- | --- | --- | --- |
| 連絡先情報 | メールアドレス | Firebase Authentication、Firestore `users` | ログイン、本人確認、パスワード再設定、サポート対応 | あり | なし |
| 識別子 | Firebase UID | Firebase Authentication、Firestore | ユーザーごとのデータ分離、セキュリティルール判定 | あり | なし |
| プロフィール情報 | ニックネーム、年齢、性別、メール確認状態 | Firestore `users` | アカウント表示、初期設定、アプリ内表示 | あり | なし |
| 旅行データ | 旅行名、開始日、終了日、参加者、作成・更新日時 | Firestore `trips` | 旅行一覧、旅行詳細、編集、共有の管理 | あり | なし |
| スケジュールデータ | 予定名、日時、カテゴリ、予算、場所名、住所、緯度経度 | Firestore `schedules` | 旅程管理、地図表示、場所検索結果の保存 | あり | なし |
| 位置情報 | GPSログの緯度、経度、精度、高度、速度、記録日時 | 端末内SwiftData、Firestore `gps_sessions`、`gps_logs`、`gps_shares`、`points` | 移動ログ記録、地図表示、共有リンク用のルート表示 | あり | なし |
| 写真データ | 旅行写真、スケジュール写真、QR画像 | 端末内 Application Support | アプリ内表示、旅行・予定・QRの管理 | 端末内で関連付け | なし |
| QRデータ | QRタイトル、説明、URL、旅行・予定との関連情報 | Firestore、端末内SwiftData | QR情報の表示、編集、ローカルキャッシュ | あり | なし |
| 設定情報 | 表示言語 | UserDefaults | 日本語・英語表示の切り替え | 端末内設定 | なし |

## App Store Connect App Privacy 回答案

### トラッキング

- 他社のアプリやWebサイトをまたいだトラッキング: なし
- 広告目的のデータ利用: なし
- 第三者広告SDK: なし

### ユーザーに関連付けられる可能性があるデータ

- Contact Info
  - Email Address
- Identifiers
  - User ID
- User Content
  - 旅行データ
  - スケジュールデータ
  - QRデータ
  - 端末内に保存される写真・画像
- Location
  - Precise Location
  - GPSログ利用時の位置情報

### 現時点で収集しないデータ

- Financial Info
- Health and Fitness
- Contacts
- Browsing History
- Search History
- Purchases
- Advertising Data
- Sensitive Info
- Other Data for advertising or tracking

## 第三者サービス・Apple機能

| サービス・機能 | 用途 |
| --- | --- |
| Firebase Authentication | メールアドレス・パスワード認証、メール確認、パスワード再設定 |
| Cloud Firestore | ユーザー、旅行、予定、GPS共有、QRメタデータの保存 |
| Firebase Storage SDK | 依存関係として含まれていますが、現在のMVPでは写真保存には使用していません |
| Apple CoreLocation | GPSログ、現在地取得、バックグラウンド位置情報記録 |
| Apple MapKit | 地図表示、場所検索、予定地点の表示 |
| Apple PhotosUI | ユーザーが選択した写真の読み込み |

## 端末権限

TabiTabi は、以下の用途で iOS の権限を使用します。

- 位置情報
  - 旅行中の移動ルートを記録するために使用します。
  - バックグラウンドでも移動ルートを記録するために使用します。
- 写真
  - ユーザーが選択した旅行写真、スケジュール写真、QR画像をアプリ内に保存・表示するために使用します。

## 保存期間と削除

- ユーザーが作成した旅行、スケジュール、QR、GPSログ、共有データは、ユーザーが削除する、またはアカウント削除・データ削除を依頼するまで保存されます。
- 端末内に保存された写真・QR画像・ローカルGPSログは、アプリ内の削除操作またはアプリのアンインストールにより削除されます。
- Firebase上のデータ削除が必要な場合は、サポート窓口 `tabitabiapp@gmail.com` で対応します。
- FirebaseやAppleの基盤側で一時的なログやバックアップが保持される場合があります。

## リリース前チェック

- App Store Connect の App Privacy 回答が、このファイルと `PRIVACY_POLICY.md` の内容と一致していること。
- 公開用プライバシーポリシーURLが App Store Connect に設定されていること。
- Firebase Storage、Analytics、広告SDK、外部メール配信サービスなどを追加した場合は、このファイルと `PRIVACY_POLICY.md` を更新すること。
- アカウント作成機能を提供するため、ユーザーがデータ削除またはアカウント削除を依頼できる導線を明確にしておくこと。
