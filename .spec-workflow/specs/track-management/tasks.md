# Tasks Document: 楽曲管理（Track Management）

## Phase 1: 基盤構築（型定義・インフラ）

- [ ] 1. Supabase プロジェクト設定
  - Supabase プロジェクトの接続設定
  - 環境変数の設定（.env.local）
  - _Requirements: 全体の前提条件_
  - _Prompt: Role: DevOps Engineer specializing in Supabase and Next.js configuration | Task: Configure Supabase project connection with environment variables in .env.local following tech.md guidelines, set up server-side only access pattern | Restrictions: Do not expose Supabase credentials to client, use SUPABASE_SERVICE_ROLE_KEY for server-side only | Success: Supabase client is properly configured in server/lib/supabase.ts, environment variables are set correctly, connection test passes_

- [ ] 2. データベーススキーマ作成
  - File: Supabase Dashboard または migration file
  - tracks テーブルの作成（id, title, artist, album, duration, file_path, file_size, cover_url, created_at, updated_at）
  - Storage バケット（tracks）の作成
  - _Requirements: REQ-001, REQ-002_
  - _Prompt: Role: Database Administrator specializing in PostgreSQL and Supabase | Task: Create tracks table schema and Storage bucket following design.md data models, enable RLS policies for future auth integration | Restrictions: Use UUID for primary key, follow snake_case naming for columns, set appropriate constraints | Success: Table is created with correct schema, Storage bucket is configured, RLS policies are in place_

- [ ] 3. 型定義の作成
  - File: src/types/models/track.ts, src/types/interfaces/track-repository.ts, src/types/interfaces/track-service.ts, src/types/api/track.ts
  - Track 型、CreateTrackInput 型、TrackQueryOptions 型の定義
  - ITrackRepository、ITrackService インターフェースの定義
  - API リクエスト/レスポンス型の定義
  - _Leverage: design.md Data Models, Components and Interfaces セクション_
  - _Requirements: REQ-001, REQ-002, REQ-003, REQ-004, REQ-005_
  - _Prompt: Role: TypeScript Developer specializing in type systems and interfaces | Task: Create comprehensive TypeScript types and interfaces following design.md specifications, establish type safety foundation for entire feature | Restrictions: Follow existing project type naming conventions, ensure interfaces support DI and testing, export all types properly | Success: All types compile without errors, interfaces are well-documented, types cover all feature requirements_

## Phase 2: サーバーサイド実装

- [ ] 4. Supabase クライアント設定
  - File: src/server/lib/supabase.ts
  - サーバーサイド専用 Supabase クライアントの作成
  - _Leverage: tech.md Supabase-Vercel連携, structure.md server/lib/_
  - _Requirements: 全体の前提条件_
  - _Prompt: Role: Backend Developer specializing in Supabase and server-side JavaScript | Task: Create server-side only Supabase client following structure.md patterns, ensure client is never exposed to browser | Restrictions: Use createClient from @supabase/supabase-js with service role key, singleton pattern for efficiency | Success: Supabase client is properly initialized, works only on server-side, type-safe_

- [ ] 5. TrackRepository 実装
  - File: src/server/repositories/track-repository.ts
  - ITrackRepository の実装（findAll, findById, create, delete）
  - Supabase Database との CRUD 操作
  - Supabase Storage との連携（ファイルアップロード、削除、署名付きURL生成）
  - _Leverage: src/server/lib/supabase.ts, src/types/interfaces/track-repository.ts_
  - _Requirements: REQ-001, REQ-002, REQ-003, REQ-004_
  - _Prompt: Role: Backend Developer with expertise in Repository Pattern and Supabase | Task: Implement TrackRepository with Supabase Database and Storage operations following ITrackRepository interface, handle file uploads and signed URL generation | Restrictions: Must implement all interface methods, handle errors gracefully, use transactions where appropriate | Success: Repository correctly implements interface, all CRUD operations work with Supabase, Storage operations are reliable_

- [ ] 6. ID3タグ抽出ユーティリティ
  - File: src/server/lib/id3-parser.ts
  - MP3ファイルからID3タグ（タイトル、アーティスト、アルバム、再生時間）を抽出
  - ID3タグが存在しない場合のフォールバック処理
  - _Requirements: REQ-001 (Acceptance Criteria 3, 4)_
  - _Prompt: Role: Backend Developer with expertise in audio file processing | Task: Create ID3 tag extraction utility using music-metadata or similar library, handle missing tags with fallback to filename | Restrictions: Support MP3 format only, handle corrupt or missing ID3 tags gracefully, extract cover art if available | Success: Utility correctly extracts ID3 tags from MP3 files, fallback works for missing tags, cover art extraction works_

- [ ] 7. TrackService 実装
  - File: src/server/services/track-service.ts
  - ITrackService の実装（getAllTracks, getTrackById, createTrack, deleteTrack）
  - ビジネスロジック（バリデーション、ID3タグ抽出、ファイルサイズ制限）
  - _Leverage: src/server/repositories/track-repository.ts, src/server/lib/id3-parser.ts, src/types/interfaces/track-service.ts_
  - _Requirements: REQ-001, REQ-002, REQ-003, REQ-004, REQ-005_
  - _Prompt: Role: Backend Developer with expertise in service layer architecture | Task: Implement TrackService with business logic following ITrackService interface, integrate ID3 parsing and validation | Restrictions: Must implement interface exactly, enforce 50MB file limit, validate MP3 MIME type, handle all error cases | Success: Service implements all interface methods, business rules are enforced, proper error handling_

- [ ] 8. API Routes 実装
  - File: src/app/api/tracks/route.ts, src/app/api/tracks/[id]/route.ts
  - GET /api/tracks - 楽曲一覧取得（検索、ソート対応）
  - POST /api/tracks - 楽曲アップロード（FormData）
  - GET /api/tracks/[id] - 楽曲詳細取得
  - DELETE /api/tracks/[id] - 楽曲削除
  - _Leverage: src/server/services/track-service.ts, src/types/api/track.ts_
  - _Requirements: REQ-001, REQ-002, REQ-003, REQ-004, REQ-005_
  - _Prompt: Role: Full-stack Developer with expertise in Next.js App Router API Routes | Task: Implement RESTful API endpoints using Next.js route handlers, integrate TrackService for business logic | Restrictions: Use proper HTTP status codes, validate request data, return consistent JSON response format, handle errors gracefully | Success: All endpoints work correctly, proper error responses, follows REST conventions_

## Phase 3: クライアントサイド実装

- [ ] 9. API クライアント作成
  - File: src/services/track-client.ts
  - REST API との通信（getAll, create, getById, delete）
  - エラーハンドリング、型安全な レスポンス
  - _Leverage: src/types/api/track.ts_
  - _Requirements: REQ-001, REQ-002, REQ-003, REQ-004, REQ-005_
  - _Prompt: Role: Frontend Developer with expertise in API client design | Task: Create type-safe API client for track operations using fetch, handle errors and provide consistent response format | Restrictions: Use proper HTTP methods, handle network errors gracefully, return typed responses | Success: API client is type-safe, error handling works correctly, all methods functional_

- [ ] 10. Zustand Store 作成
  - File: src/stores/track-store.ts
  - TrackState インターフェース実装
  - Actions: fetchTracks, uploadTrack, deleteTrack, setSearchQuery, setSortBy
  - _Leverage: src/services/track-client.ts, design.md trackStore 仕様_
  - _Requirements: REQ-001, REQ-002, REQ-003, REQ-004_
  - _Prompt: Role: Frontend Developer with expertise in Zustand state management | Task: Create Zustand store for track management following design.md specifications, integrate with track-client for API calls | Restrictions: Follow Zustand best practices, handle loading and error states, keep state normalized | Success: Store manages track state correctly, actions work as expected, UI can subscribe to state changes_

## Phase 4: UI コンポーネント実装

- [ ] 11. 共通 UI コンポーネント作成
  - File: src/components/ui/button.tsx, src/components/ui/input.tsx, src/components/ui/modal.tsx, src/components/ui/loading-spinner.tsx
  - 再利用可能な Presentational コンポーネント
  - Tailwind CSS でスタイリング
  - _Requirements: 全体の UI 要件_
  - _Prompt: Role: Frontend Developer specializing in React and Tailwind CSS | Task: Create reusable UI components (Button, Input, Modal, LoadingSpinner) with Tailwind CSS styling | Restrictions: Follow accessibility guidelines, use semantic HTML, keep components simple and composable | Success: Components are reusable, styled correctly, accessible_

- [ ] 12. TrackItem コンポーネント
  - File: src/components/ui/track-item.tsx
  - Presentational コンポーネント（タイトル、アーティスト、アルバム、再生時間、カバー画像表示）
  - 削除ボタン、クリックハンドラー
  - _Leverage: src/components/ui/button.tsx_
  - _Requirements: REQ-002, REQ-004_
  - _Prompt: Role: Frontend Developer with expertise in React component design | Task: Create TrackItem presentational component displaying track info with delete button and click handler | Restrictions: Component should be stateless, accept all data via props, follow accessibility guidelines | Success: Component displays track info correctly, events are properly handled, responsive design_

- [ ] 13. TrackList コンポーネント
  - File: src/components/features/track-list.tsx
  - Container コンポーネント（検索、ソート、一覧表示）
  - trackStore との連携
  - 空状態の表示（0件時）
  - _Leverage: src/stores/track-store.ts, src/components/ui/track-item.tsx_
  - _Requirements: REQ-002, REQ-003_
  - _Prompt: Role: Frontend Developer with expertise in React and state management | Task: Create TrackList container component integrating with trackStore, implement search/sort UI and empty state | Restrictions: Use trackStore for state, delegate display to TrackItem, handle loading and error states | Success: List displays tracks correctly, search/sort work, empty state shows when no tracks_

- [ ] 14. TrackUpload コンポーネント
  - File: src/components/features/track-upload.tsx
  - ドラッグ&ドロップ対応のアップロード UI
  - 複数ファイル対応、進捗表示
  - ファイルバリデーション（サイズ、形式）
  - _Leverage: src/stores/track-store.ts_
  - _Requirements: REQ-001_
  - _Prompt: Role: Frontend Developer with expertise in file upload UX | Task: Create TrackUpload component with drag-and-drop, multiple file support, progress display, and client-side validation | Restrictions: Validate file size (50MB) and type (MP3) before upload, show clear progress feedback, handle errors gracefully | Success: Drag-and-drop works, multiple files supported, validation prevents invalid uploads, progress shown_

- [ ] 15. TrackDetail コンポーネント
  - File: src/components/features/track-detail.tsx
  - 楽曲詳細モーダル（タイトル、アーティスト、アルバム、再生時間、カバー画像、追加日時）
  - 削除ボタン
  - _Leverage: src/components/ui/modal.tsx, src/stores/track-store.ts_
  - _Requirements: REQ-005, REQ-004_
  - _Prompt: Role: Frontend Developer with expertise in modal design | Task: Create TrackDetail modal component displaying full track information with delete option | Restrictions: Use Modal component, handle modal open/close state, confirm before delete | Success: Modal displays all track info, delete with confirmation works, proper close behavior_

## Phase 5: ページ統合

- [ ] 16. 楽曲管理ページ作成
  - File: src/app/tracks/page.tsx
  - TrackList, TrackUpload, TrackDetail の統合
  - ページレイアウト
  - _Leverage: src/components/features/track-list.tsx, src/components/features/track-upload.tsx, src/components/features/track-detail.tsx_
  - _Requirements: REQ-001, REQ-002, REQ-003, REQ-004, REQ-005_
  - _Prompt: Role: Full-stack Developer with expertise in Next.js App Router | Task: Create tracks page integrating all track components, implement page layout with proper component composition | Restrictions: Follow Next.js App Router conventions, use proper loading and error handling, ensure SSR compatibility | Success: Page displays all components correctly, navigation works, full feature functionality available_

## Phase 6: テスト

- [ ] 17. Repository 単体テスト
  - File: src/server/repositories/__tests__/track-repository.test.ts
  - TrackRepository の各メソッドのテスト
  - Supabase クライアントのモック
  - _Leverage: Vitest, MSW_
  - _Requirements: Non-Functional Requirements - Code Architecture_
  - _Prompt: Role: QA Engineer with expertise in unit testing | Task: Create unit tests for TrackRepository methods with mocked Supabase client | Restrictions: Test all CRUD operations, mock external dependencies, test error scenarios | Success: All repository methods tested, good coverage, tests run reliably_

- [ ] 18. Service 単体テスト
  - File: src/server/services/__tests__/track-service.test.ts
  - TrackService の各メソッドのテスト
  - Repository のモック
  - _Leverage: Vitest_
  - _Requirements: Non-Functional Requirements - Code Architecture_
  - _Prompt: Role: QA Engineer with expertise in service testing | Task: Create unit tests for TrackService methods with mocked repository | Restrictions: Test business logic in isolation, verify validation rules, test error handling | Success: All service methods tested, business rules verified, error handling tested_

- [ ] 19. API 統合テスト
  - File: src/app/api/tracks/__tests__/route.test.ts
  - API エンドポイントのテスト
  - _Leverage: Vitest, MSW_
  - _Requirements: Non-Functional Requirements_
  - _Prompt: Role: QA Engineer with expertise in API testing | Task: Create integration tests for track API endpoints using MSW for service mocking | Restrictions: Test all HTTP methods, verify response formats, test error responses | Success: All endpoints tested, proper status codes verified, error handling tested_

- [ ] 20. コンポーネントテスト
  - File: src/components/features/__tests__/track-list.test.tsx
  - TrackList, TrackUpload コンポーネントのテスト
  - _Leverage: Vitest, React Testing Library_
  - _Requirements: Non-Functional Requirements_
  - _Prompt: Role: Frontend QA Engineer with expertise in React testing | Task: Create component tests for TrackList and TrackUpload using React Testing Library | Restrictions: Test user interactions, verify UI states, mock store and API client | Success: Components tested for user interactions, loading/error states verified, accessibility checked_
