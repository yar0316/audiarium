# Technology Stack

## Project Type

クロスプラットフォーム音楽管理・再生アプリケーション。Web アプリケーション + モバイルアプリ + API サービスのハイブリッド構成。

## Core Technologies

### Primary Language(s)
- **TypeScript**: Web フロントエンド・API（Next.js）
- **Dart**: モバイルアプリ（Flutter）
- **Runtime**: Node.js 20+

### Key Dependencies/Libraries

**Web (Next.js)**
- **Next.js 16**: フルスタックフレームワーク（App Router）
- **React 19**: UI ライブラリ
- **Tailwind CSS 4**: スタイリング
- **Howler.js**: Web Audio 再生
- **@supabase/supabase-js**: Supabase クライアント

**Mobile (Flutter)** ※後日実装
- **Flutter 3.x**: クロスプラットフォームモバイルフレームワーク
- **just_audio**: 音楽再生
- **supabase_flutter**: Supabase クライアント

### Application Architecture

```
┌─────────────────────────────────────────────────┐
│                    Vercel                       │
│  ┌───────────────────────────────────────────┐  │
│  │              Next.js 16                   │  │
│  │  ┌─────────────┐  ┌─────────────────┐     │  │
│  │  │  Web UI     │  │   API Routes    │     │  │
│  │  │  (React)    │  │   (/api/*)      │     │  │
│  │  └─────────────┘  └────────┬────────┘     │  │
│  └────────────────────────────┼──────────────┘  │
└───────────────────────────────┼─────────────────┘
                                │ REST API
          ┌─────────────────────┼─────────────────────┐
          │                     │                     │
          ▼                     ▼                     ▼
  ┌───────────────┐    ┌───────────────┐    ┌───────────────┐
  │    Flutter    │    │   Supabase    │    │   Supabase    │
  │    (Mobile)   │    │  PostgreSQL   │    │    Storage    │
  └───────────────┘    └───────────────┘    └───────────────┘
```

### Data Storage
- **Primary storage**: Supabase (PostgreSQL) ※Vercel Integration で環境変数を自動連携
- **File storage**: Supabase Storage（MP3 ファイル）
- **Caching**: なし（個人利用のため不要）
- **Data formats**: JSON（API）、MP3（音楽ファイル）

### External Integrations
- **Supabase**: DB、Storage、Auth
- **Vercel**: ホスティング・デプロイ
- **Protocols**: HTTP/REST
- **Authentication**: Supabase Auth（Email/Password）

## Development Environment

### Build & Development Tools
- **Build System**: Next.js built-in（Turbopack）
- **Package Management**: npm / pnpm
- **Development workflow**: `next dev --turbopack`（Hot Reload）

### Code Quality Tools
- **Static Analysis**: ESLint
- **Formatting**: Prettier
- **Testing Framework**: Vitest（予定）
- **Type Checking**: TypeScript strict mode

### Version Control & Collaboration
- **VCS**: Git
- **Repository**: GitHub
- **Branching Strategy**: GitHub Flow（main + feature branches）

## Deployment & Distribution

### Web
- **Platform**: Vercel（Serverless）
- **Distribution**: URL アクセス
- **CI/CD**: Vercel 自動デプロイ（GitHub 連携）

### Mobile ※後日
- **Platform**: iOS / Android
- **Distribution**: 個人利用（App Store 非公開）

## Technical Requirements & Constraints

### Performance Requirements
- 音楽再生のレイテンシ: 1秒以内に再生開始
- API レスポンス: 500ms 以内
- ファイルアップロード: 50MB まで対応

### Compatibility Requirements
- **Web**: Chrome, Safari, Firefox（最新版）
- **Mobile**: iOS 15+, Android 10+

### Security & Compliance
- **Authentication**: Supabase Auth
- **API Protection**: 認証必須
- **Storage**: Supabase RLS（Row Level Security）

### Scalability & Reliability
- **Expected Load**: 1ユーザー（個人利用）
- **Availability**: 最低限（Vercel 無料枠で十分）

## Technical Decisions & Rationale

### Decision Log

1. **Next.js API Routes vs 専用バックエンド**
   - 選択: Next.js API Routes
   - 理由: 個人利用で規模が小さい、Vercel 無料枠で運用可能、インフラ管理不要

2. **Supabase vs Firebase**
   - 選択: Supabase
   - 理由: PostgreSQL ベースで SQL が使える、無料枠が十分、Storage 機能付き

3. **Flutter vs React Native**
   - 選択: Flutter
   - 理由: 単一コードベースで iOS/Android 対応、パフォーマンス良好

4. **Tailwind CSS vs CSS Modules**
   - 選択: Tailwind CSS
   - 理由: ユーザーが慣れている、高速な UI 開発

## Known Limitations

- **Vercel 無料枠制限**: Serverless Function 実行時間 10秒、帯域制限あり
- **Supabase 無料枠制限**: Storage 1GB、DB 500MB
- **オフライン非対応**: Web 版はオンライン必須（PWA 化は将来検討）
