# Requirements Document: 楽曲管理（Track Management）

## Introduction

音楽ファイル（MP3）のアップロード、一覧表示、メタデータ表示・編集、削除機能を提供する。音楽管理アプリのコア機能であり、すべての楽曲操作の基盤となる。

## Alignment with Product Vision

- **音楽ファイル管理**: MP3ファイルのアップロード、メタデータ編集、削除（product.md Key Features #1）
- **シンプルさ優先**: 必要最小限の機能から始め、複雑化を避ける（product.md Product Principles #1）
- **個人最適化**: 汎用性より自分の使いやすさを重視（product.md Product Principles #2）

## Requirements

### REQ-001: 楽曲ファイルのアップロード

**User Story:** ユーザーとして、MP3ファイルをアップロードしたい。ライブラリに楽曲を追加するため。

#### Acceptance Criteria

1. WHEN ユーザーがMP3ファイルを選択 THEN システム SHALL ファイルをSupabase Storageにアップロードする
2. WHEN アップロードが成功 THEN システム SHALL 楽曲メタデータをデータベースに保存する
3. WHEN アップロードが成功 THEN システム SHALL MP3ファイルからID3タグ（タイトル、アーティスト、アルバム、再生時間）を自動抽出する
4. IF ID3タグが存在しない THEN システム SHALL ファイル名をタイトルとして使用する
5. WHEN 複数ファイルを選択 THEN システム SHALL 一括アップロードをサポートする
6. IF ファイルサイズが50MBを超える THEN システム SHALL エラーメッセージを表示する
7. IF ファイル形式がMP3以外 THEN システム SHALL エラーメッセージを表示する

### REQ-002: 楽曲一覧の表示

**User Story:** ユーザーとして、アップロードした楽曲の一覧を見たい。ライブラリの楽曲を把握するため。

#### Acceptance Criteria

1. WHEN ユーザーが楽曲一覧ページにアクセス THEN システム SHALL すべての楽曲をリスト形式で表示する
2. WHEN 楽曲一覧を表示 THEN システム SHALL タイトル、アーティスト、アルバム、再生時間を表示する
3. WHEN 楽曲一覧を表示 THEN システム SHALL デフォルトで追加日時の降順（新しい順）でソートする
4. WHEN 楽曲にカバー画像がある THEN システム SHALL カバー画像のサムネイルを表示する
5. IF 楽曲が0件 THEN システム SHALL 「楽曲がありません」のメッセージとアップロードへの導線を表示する

### REQ-003: 楽曲の検索・フィルター

**User Story:** ユーザーとして、楽曲を検索・フィルターしたい。目的の曲を素早く見つけるため。

#### Acceptance Criteria

1. WHEN ユーザーが検索ボックスにテキストを入力 THEN システム SHALL タイトル、アーティスト、アルバムを対象に部分一致検索する
2. WHEN 検索結果が0件 THEN システム SHALL 「該当する楽曲がありません」のメッセージを表示する
3. WHEN ユーザーがソート順を変更 THEN システム SHALL タイトル順、アーティスト順、追加日時順でソートする

### REQ-004: 楽曲の削除

**User Story:** ユーザーとして、不要な楽曲を削除したい。ライブラリを整理するため。

#### Acceptance Criteria

1. WHEN ユーザーが楽曲の削除ボタンをクリック THEN システム SHALL 確認ダイアログを表示する
2. WHEN ユーザーが削除を確認 THEN システム SHALL 楽曲データをデータベースから削除する
3. WHEN ユーザーが削除を確認 THEN システム SHALL 音楽ファイルをSupabase Storageから削除する
4. WHEN 削除が成功 THEN システム SHALL 成功メッセージを表示し、一覧を更新する
5. WHEN ユーザーがキャンセル THEN システム SHALL ダイアログを閉じ、削除を中止する

### REQ-005: 楽曲詳細の表示

**User Story:** ユーザーとして、楽曲の詳細情報を見たい。楽曲の詳細を確認するため。

#### Acceptance Criteria

1. WHEN ユーザーが楽曲をクリック THEN システム SHALL 楽曲詳細ページ/モーダルを表示する
2. WHEN 詳細を表示 THEN システム SHALL タイトル、アーティスト、アルバム、再生時間、追加日時を表示する
3. WHEN 詳細を表示 THEN システム SHALL カバー画像を表示する（存在する場合）
4. WHEN 詳細を表示 THEN システム SHALL 削除ボタンを表示する

## Non-Functional Requirements

### Code Architecture and Modularity

- **Single Responsibility Principle**: 各コンポーネントは単一の責務を持つ
- **Modular Design**: UI、ビジネスロジック、データアクセス層を分離
- **Interface-based**: ITrackRepository、ITrackService インターフェースを使用
- **Testable**: モック可能な設計

### Performance

- 楽曲一覧の初期表示: 1秒以内
- ファイルアップロード: 50MBまで対応
- 検索結果表示: 500ms以内

### Security

- ファイルアップロード時のMIMEタイプ検証
- ファイルサイズ制限（50MB）
- 将来的な認証との統合を考慮

### Reliability

- アップロード失敗時のリトライ機能
- エラー時の適切なメッセージ表示
- データベース操作の整合性確保

### Usability

- ドラッグ&ドロップでのファイルアップロード対応
- アップロード進捗の可視化
- レスポンシブデザイン（PC/タブレット対応）
