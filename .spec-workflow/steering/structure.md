# Project Structure

## Directory Organization

```
audiarium/                      # プロジェクトルート（モノレポ）
├── .spec-workflow/             # 仕様書・ワークフロー管理
│   ├── steering/               # ステアリングドキュメント
│   ├── specs/                  # 機能仕様書
│   └── templates/              # テンプレート
├── web/                        # Next.js（Web UI + API）
│   ├── src/
│   │   ├── app/                # App Router（ページのみ）
│   │   │   ├── (auth)/         # 認証関連ページ
│   │   │   ├── tracks/         # 楽曲ページ
│   │   │   ├── playlists/      # プレイリストページ
│   │   │   ├── layout.tsx      # ルートレイアウト
│   │   │   └── page.tsx        # ホームページ
│   │   ├── server/             # サーバーサイド処理（すべてここに集約）
│   │   │   ├── api/            # API Routes（エントリーポイント）
│   │   │   │   ├── tracks/     # 楽曲 API
│   │   │   │   │   └── route.ts
│   │   │   │   ├── playlists/  # プレイリスト API
│   │   │   │   │   └── route.ts
│   │   │   │   └── auth/       # 認証 API
│   │   │   │       └── route.ts
│   │   │   ├── services/       # ビジネスロジック
│   │   │   │   ├── track-service.ts
│   │   │   │   └── playlist-service.ts
│   │   │   ├── repositories/   # データアクセス層
│   │   │   │   ├── track-repository.ts
│   │   │   │   └── playlist-repository.ts
│   │   │   ├── lib/            # サーバー専用ライブラリ
│   │   │   │   └── supabase.ts # Supabase クライアント
│   │   │   └── factories/      # DI ファクトリー
│   │   │       └── index.ts
│   │   ├── components/         # 再利用可能コンポーネント
│   │   │   ├── ui/             # 基本 UI コンポーネント（Presentational）
│   │   │   └── features/       # 機能別コンポーネント（Container）
│   │   ├── stores/             # 状態管理（Zustand）
│   │   │   ├── player-store.ts # 再生状態
│   │   │   ├── track-store.ts  # 楽曲データ
│   │   │   └── auth-store.ts   # 認証状態
│   │   ├── services/           # フロントエンド用サービス層
│   │   │   ├── track-client.ts # Track API クライアント
│   │   │   └── playlist-client.ts
│   │   ├── lib/                # クライアント用ユーティリティ
│   │   │   ├── api/            # fetch wrapper
│   │   │   ├── audio/          # 音声再生ユーティリティ
│   │   │   └── utils/          # 汎用ユーティリティ
│   │   ├── hooks/              # カスタムフック
│   │   └── types/              # TypeScript 型定義
│   │       ├── models/         # ドメインモデル型
│   │       │   ├── track.ts    # Track 型
│   │       │   └── playlist.ts # Playlist 型
│   │       ├── interfaces/     # インターフェース定義（機能ごとに分割）
│   │       │   ├── track-repository.ts   # ITrackRepository
│   │       │   ├── track-service.ts      # ITrackService
│   │       │   ├── playlist-repository.ts
│   │       │   └── playlist-service.ts
│   │       ├── api/            # API リクエスト/レスポンス型
│   │       │   ├── track-api.ts
│   │       │   └── playlist-api.ts
│   │       └── index.ts        # 型の re-export
│   ├── __tests__/              # テストファイル
│   │   ├── unit/               # ユニットテスト
│   │   │   ├── services/       # Service 層テスト
│   │   │   ├── stores/         # Store テスト
│   │   │   └── utils/          # ユーティリティテスト
│   │   ├── integration/        # 統合テスト
│   │   │   └── api/            # API テスト
│   │   └── mocks/              # モックファイル
│   │       ├── supabase.ts     # Supabase モック
│   │       └── handlers.ts     # MSW ハンドラー
│   ├── public/                 # 静的ファイル
│   └── package.json
└── app/                        # Flutter（モバイル）※後日作成
    ├── lib/
    │   ├── screens/            # 画面
    │   ├── widgets/            # ウィジェット
    │   ├── services/           # API クライアント
    │   ├── models/             # データモデル
    │   └── providers/          # 状態管理
    └── pubspec.yaml
```

### サーバーサイド構成の説明

`server/` ディレクトリにサーバーサイド処理をすべて集約しています：

```
server/
├── api/           # エントリーポイント（HTTP リクエスト受付のみ）
├── services/      # ビジネスロジック（バリデーション、変換等）
├── repositories/  # データアクセス（Supabase 操作）
├── lib/           # Supabase クライアント等
└── factories/     # DI 用ファクトリー
```

**依存方向**: `api/ → services/ → repositories/ → lib/`

## Naming Conventions

### Files

- **コンポーネント**: `kebab-case.tsx`（例: `track-list.tsx`）
- **ページ**: `page.tsx`（Next.js App Router 規約）
- **API**: `route.ts`（Next.js App Router 規約）
- **ユーティリティ**: `kebab-case.ts`（例: `format-duration.ts`）
- **型定義**: `kebab-case.ts`（例: `track.ts`）
- **テスト**: `*.test.ts` / `*.test.tsx`

### Code

- **コンポーネント**: `PascalCase`（例: `TrackList`）
- **関数**: `camelCase`（例: `formatDuration`）
- **定数**: `UPPER_SNAKE_CASE`（例: `MAX_FILE_SIZE`）
- **変数**: `camelCase`（例: `trackList`）
- **型（Type）**: `PascalCase`（例: `Track`, `CreateTrackInput`）
- **インターフェース**: `I` プレフィックス + `PascalCase`（例: `ITrackRepository`, `ITrackService`）

### Directories

- すべて `kebab-case`（例: `track-list/`, `api/`）
- Next.js 規約のフォルダは例外（例: `(auth)/`, `[id]/`）

## Type & Interface Management（型・インターフェース管理）

### 型定義の構造

```
types/
├── models/              # ドメインモデル（エンティティ）
│   ├── track.ts         # Track, TrackWithArtist
│   ├── playlist.ts      # Playlist, PlaylistWithTracks
│   └── user.ts          # User
├── interfaces/          # 抽象化インターフェース（機能ごとに分割）
│   ├── track-repository.ts   # ITrackRepository
│   ├── track-service.ts      # ITrackService
│   ├── playlist-repository.ts
│   └── playlist-service.ts
├── api/                 # API 関連の型（機能ごとに分割）
│   ├── track-api.ts     # CreateTrackRequest, TrackResponse
│   └── playlist-api.ts
└── index.ts             # re-export
```

### モデル型の定義例

```typescript
// types/models/track.ts

/** 楽曲の基本情報 */
export type Track = {
  id: string;
  title: string;
  artist: string;
  album: string | null;
  duration: number;       // 秒数
  filePath: string;
  coverUrl: string | null;
  createdAt: Date;
  updatedAt: Date;
};

/** アーティスト情報を含む楽曲 */
export type TrackWithArtist = Track & {
  artistInfo: Artist;
};

/** 楽曲作成時の入力 */
export type CreateTrackInput = Pick<Track, 'title' | 'artist' | 'album' | 'duration' | 'filePath'>;

/** 楽曲更新時の入力 */
export type UpdateTrackInput = Partial<CreateTrackInput>;
```

### インターフェース定義（機能ごとにファイル分割）

```typescript
// types/interfaces/track-repository.ts

import { Track, CreateTrackInput, UpdateTrackInput } from '@/types/models/track';

/** 楽曲リポジトリのインターフェース */
export interface ITrackRepository {
  findAll(): Promise<Track[]>;
  findById(id: string): Promise<Track | null>;
  findByArtist(artist: string): Promise<Track[]>;
  create(input: CreateTrackInput): Promise<Track>;
  update(id: string, input: UpdateTrackInput): Promise<Track>;
  delete(id: string): Promise<void>;
}
```

```typescript
// types/interfaces/track-service.ts

import { Track, CreateTrackInput, UpdateTrackInput } from '@/types/models/track';

/** 楽曲サービスのインターフェース */
export interface ITrackService {
  getAllTracks(): Promise<Track[]>;
  getTrackById(id: string): Promise<Track | null>;
  searchTracks(query: string): Promise<Track[]>;
  createTrack(input: CreateTrackInput): Promise<Track>;
  updateTrack(id: string, input: UpdateTrackInput): Promise<Track>;
  deleteTrack(id: string): Promise<void>;
}
```

### サーバーサイド実装

#### Repository 実装

```typescript
// server/repositories/track-repository.ts

import { SupabaseClient } from '@supabase/supabase-js';
import { ITrackRepository } from '@/types/interfaces/track-repository';
import { Track, CreateTrackInput, UpdateTrackInput } from '@/types/models/track';

/** Supabase を使用した楽曲リポジトリ実装 */
export class SupabaseTrackRepository implements ITrackRepository {
  constructor(private supabase: SupabaseClient) {}

  async findAll(): Promise<Track[]> {
    const { data, error } = await this.supabase
      .from('tracks')
      .select('*')
      .order('created_at', { ascending: false });
    if (error) throw error;
    return data;
  }

  async findById(id: string): Promise<Track | null> {
    const { data, error } = await this.supabase
      .from('tracks')
      .select('*')
      .eq('id', id)
      .single();
    if (error) return null;
    return data;
  }

  async findByArtist(artist: string): Promise<Track[]> {
    const { data, error } = await this.supabase
      .from('tracks')
      .select('*')
      .ilike('artist', `%${artist}%`);
    if (error) throw error;
    return data;
  }

  async create(input: CreateTrackInput): Promise<Track> {
    const { data, error } = await this.supabase
      .from('tracks')
      .insert(input)
      .select()
      .single();
    if (error) throw error;
    return data;
  }

  async update(id: string, input: UpdateTrackInput): Promise<Track> {
    const { data, error } = await this.supabase
      .from('tracks')
      .update(input)
      .eq('id', id)
      .select()
      .single();
    if (error) throw error;
    return data;
  }

  async delete(id: string): Promise<void> {
    const { error } = await this.supabase
      .from('tracks')
      .delete()
      .eq('id', id);
    if (error) throw error;
  }
}
```

#### Service 実装

```typescript
// server/services/track-service.ts

import { ITrackService } from '@/types/interfaces/track-service';
import { ITrackRepository } from '@/types/interfaces/track-repository';
import { Track, CreateTrackInput, UpdateTrackInput } from '@/types/models/track';

/** 楽曲サービス実装 */
export class TrackService implements ITrackService {
  constructor(private repository: ITrackRepository) {}

  async getAllTracks(): Promise<Track[]> {
    return this.repository.findAll();
  }

  async getTrackById(id: string): Promise<Track | null> {
    return this.repository.findById(id);
  }

  async searchTracks(query: string): Promise<Track[]> {
    // ビジネスロジック: アーティスト検索
    return this.repository.findByArtist(query);
  }

  async createTrack(input: CreateTrackInput): Promise<Track> {
    // バリデーション
    if (!input.title || input.title.trim() === '') {
      throw new Error('タイトルは必須です');
    }
    if (input.duration <= 0) {
      throw new Error('再生時間は正の値である必要があります');
    }
    return this.repository.create(input);
  }

  async updateTrack(id: string, input: UpdateTrackInput): Promise<Track> {
    // 存在確認
    const existing = await this.repository.findById(id);
    if (!existing) {
      throw new Error('楽曲が見つかりません');
    }
    return this.repository.update(id, input);
  }

  async deleteTrack(id: string): Promise<void> {
    // 存在確認
    const existing = await this.repository.findById(id);
    if (!existing) {
      throw new Error('楽曲が見つかりません');
    }
    return this.repository.delete(id);
  }
}
```

### フロントエンド用サービス層

```typescript
// services/track-client.ts

import { Track, CreateTrackInput, UpdateTrackInput } from '@/types/models/track';
import { apiClient } from '@/lib/api/client';

/** フロントエンド用楽曲 API クライアント */
export const trackClient = {
  async getAll(): Promise<Track[]> {
    return apiClient.get<Track[]>('/api/tracks');
  },

  async getById(id: string): Promise<Track | null> {
    return apiClient.get<Track>(`/api/tracks/${id}`);
  },

  async create(input: CreateTrackInput): Promise<Track> {
    return apiClient.post<Track>('/api/tracks', input);
  },

  async update(id: string, input: UpdateTrackInput): Promise<Track> {
    return apiClient.patch<Track>(`/api/tracks/${id}`, input);
  },

  async delete(id: string): Promise<void> {
    return apiClient.delete(`/api/tracks/${id}`);
  },
};
```

```typescript
// lib/api/client.ts

const BASE_URL = process.env.NEXT_PUBLIC_API_URL || '';

class ApiClient {
  async get<T>(path: string): Promise<T> {
    const res = await fetch(`${BASE_URL}${path}`);
    if (!res.ok) throw new Error(await res.text());
    return res.json();
  }

  async post<T>(path: string, body: unknown): Promise<T> {
    const res = await fetch(`${BASE_URL}${path}`, {
      method: 'POST',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(body),
    });
    if (!res.ok) throw new Error(await res.text());
    return res.json();
  }

  async patch<T>(path: string, body: unknown): Promise<T> {
    const res = await fetch(`${BASE_URL}${path}`, {
      method: 'PATCH',
      headers: { 'Content-Type': 'application/json' },
      body: JSON.stringify(body),
    });
    if (!res.ok) throw new Error(await res.text());
    return res.json();
  }

  async delete(path: string): Promise<void> {
    const res = await fetch(`${BASE_URL}${path}`, { method: 'DELETE' });
    if (!res.ok) throw new Error(await res.text());
  }
}

export const apiClient = new ApiClient();
```

### ファクトリーパターンでの DI

```typescript
// server/factories/index.ts

import { ITrackRepository } from '@/types/interfaces/track-repository';
import { ITrackService } from '@/types/interfaces/track-service';
import { SupabaseTrackRepository } from '@/server/repositories/track-repository';
import { TrackService } from '@/server/services/track-service';
import { createSupabaseClient } from '@/server/lib/supabase';

export function createTrackRepository(): ITrackRepository {
  const supabase = createSupabaseClient();
  return new SupabaseTrackRepository(supabase);
}

export function createTrackService(): ITrackService {
  const repository = createTrackRepository();
  return new TrackService(repository);
}
```

### テスト用モック実装

```typescript
// __tests__/mocks/track-repository.ts

import { ITrackRepository } from '@/types/interfaces/track-repository';
import { Track, CreateTrackInput, UpdateTrackInput } from '@/types/models/track';

export class MockTrackRepository implements ITrackRepository {
  private tracks: Track[] = [];

  async findAll(): Promise<Track[]> {
    return this.tracks;
  }

  async findById(id: string): Promise<Track | null> {
    return this.tracks.find(t => t.id === id) ?? null;
  }

  async findByArtist(artist: string): Promise<Track[]> {
    return this.tracks.filter(t => t.artist.includes(artist));
  }

  async create(input: CreateTrackInput): Promise<Track> {
    const track: Track = {
      id: `mock-${Date.now()}`,
      ...input,
      coverUrl: null,
      createdAt: new Date(),
      updatedAt: new Date(),
    };
    this.tracks.push(track);
    return track;
  }

  async update(id: string, input: UpdateTrackInput): Promise<Track> {
    const index = this.tracks.findIndex(t => t.id === id);
    if (index === -1) throw new Error('Not found');
    this.tracks[index] = { ...this.tracks[index], ...input, updatedAt: new Date() };
    return this.tracks[index];
  }

  async delete(id: string): Promise<void> {
    this.tracks = this.tracks.filter(t => t.id !== id);
  }

  // テスト用ヘルパー
  setTracks(tracks: Track[]): void {
    this.tracks = tracks;
  }
}
```

## Import Patterns

### Import Order

```typescript
// 1. React / Next.js
import { useState } from 'react';
import { useRouter } from 'next/navigation';

// 2. 外部ライブラリ
import { createClient } from '@supabase/supabase-js';

// 3. 内部モジュール（エイリアス使用）
import { TrackService } from '@/server/services/track-service';
import { Track } from '@/types/track';

// 4. 相対インポート（同一機能内のみ）
import { TrackItem } from './track-item';

// 5. スタイル
import './styles.css';
```

### Path Aliases

```typescript
// tsconfig.json で設定済み
"@/*" → "src/*"

// 使用例
import { cn } from '@/lib/utils';
import { Button } from '@/components/ui/button';
```

## Code Structure Patterns

### コンポーネントファイル構成

```typescript
// 1. インポート
import { useState } from 'react';

// 2. 型定義
interface TrackListProps {
  tracks: Track[];
  onSelect: (track: Track) => void;
}

// 3. コンポーネント本体
export function TrackList({ tracks, onSelect }: TrackListProps) {
  // hooks
  const [selected, setSelected] = useState<string | null>(null);

  // handlers
  const handleClick = (track: Track) => {
    setSelected(track.id);
    onSelect(track);
  };

  // render
  return (
    <ul>
      {tracks.map((track) => (
        <li key={track.id} onClick={() => handleClick(track)}>
          {track.title}
        </li>
      ))}
    </ul>
  );
}
```

### API Route 構成

```typescript
// src/app/api/tracks/route.ts
import { NextRequest, NextResponse } from 'next/server';
import { TrackService } from '@/server/services/track-service';

// GET /api/tracks
export async function GET(request: NextRequest) {
  const tracks = await TrackService.findAll();
  return NextResponse.json(tracks);
}

// POST /api/tracks
export async function POST(request: NextRequest) {
  const body = await request.json();
  const track = await TrackService.create(body);
  return NextResponse.json(track, { status: 201 });
}
```

### Service 層構成

```typescript
// src/server/services/track-service.ts
import { supabase } from '@/lib/supabase/client';
import { Track, CreateTrackInput } from '@/types/track';

export const TrackService = {
  async findAll(): Promise<Track[]> {
    const { data, error } = await supabase
      .from('tracks')
      .select('*')
      .order('created_at', { ascending: false });

    if (error) throw error;
    return data;
  },

  async create(input: CreateTrackInput): Promise<Track> {
    const { data, error } = await supabase
      .from('tracks')
      .insert(input)
      .select()
      .single();

    if (error) throw error;
    return data;
  },
};
```

## Code Organization Principles

1. **Single Responsibility**: 1ファイル1責務
2. **Colocation**: 関連ファイルは近くに配置（テスト、型定義）
3. **Feature-first**: 機能単位でグルーピング
4. **Barrel exports 非推奨**: 循環参照を避けるため直接インポート

## Testable Architecture（テスト容易性のための設計）

### レイヤー分離

```
┌─────────────────────────────────────────────────────────────────┐
│  Presentation Layer（UI）                                       │
│  ┌─────────────────────┐  ┌─────────────────────────────────┐  │
│  │  Presentational     │  │  Container Components           │  │
│  │  Components         │  │  (features/)                    │  │
│  │  (ui/)              │  │                                 │  │
│  │  - props のみ       │  │  - Store に接続                 │  │
│  │  - 状態なし         │  │  - Hooks 使用                   │  │
│  │  - テスト容易       │  │  - ロジック委譲                 │  │
│  └─────────────────────┘  └───────────────┬─────────────────┘  │
└───────────────────────────────────────────┼─────────────────────┘
                                            │
┌───────────────────────────────────────────┼─────────────────────┐
│  State Layer（状態管理）                   │                     │
│  ┌────────────────────────────────────────▼────────────────┐    │
│  │  Zustand Stores                                         │    │
│  │  - UI 状態と切り離し                                    │    │
│  │  - 純粋な状態管理のみ                                   │    │
│  │  - モック可能                                           │    │
│  └─────────────────────────────┬───────────────────────────┘    │
└────────────────────────────────┼────────────────────────────────┘
                                 │
┌────────────────────────────────┼────────────────────────────────┐
│  Data Layer（データアクセス）   │                                │
│  ┌─────────────────────────────▼───────────────────────────┐    │
│  │  API Client (lib/api/)                                  │    │
│  │  - fetch wrapper                                        │    │
│  │  - エラーハンドリング                                   │    │
│  │  - MSW でモック可能                                     │    │
│  └─────────────────────────────┬───────────────────────────┘    │
│                                │                                │
│  ┌─────────────────────────────▼───────────────────────────┐    │
│  │  Services (server/services/)                            │    │
│  │  - ビジネスロジック                                     │    │
│  │  - Repository を DI                                     │    │
│  └─────────────────────────────┬───────────────────────────┘    │
│                                │                                │
│  ┌─────────────────────────────▼───────────────────────────┐    │
│  │  Repositories (server/repositories/)                    │    │
│  │  - データアクセスのみ                                   │    │
│  │  - Supabase 操作を抽象化                                │    │
│  │  - テスト時はモック注入                                 │    │
│  └─────────────────────────────────────────────────────────┘    │
└─────────────────────────────────────────────────────────────────┘
```

### Container / Presentational パターン

```typescript
// Presentational Component（ui/）- テスト容易
// props のみ、状態なし、純粋な描画
interface TrackItemProps {
  track: Track;
  isPlaying: boolean;
  onPlay: () => void;
}

export function TrackItem({ track, isPlaying, onPlay }: TrackItemProps) {
  return (
    <div onClick={onPlay}>
      <span>{track.title}</span>
      {isPlaying && <PlayingIcon />}
    </div>
  );
}

// Container Component（features/）- Store 接続
export function TrackListContainer() {
  const { tracks, currentTrack, play } = usePlayerStore();
  
  return (
    <ul>
      {tracks.map((track) => (
        <TrackItem
          key={track.id}
          track={track}
          isPlaying={currentTrack?.id === track.id}
          onPlay={() => play(track)}
        />
      ))}
    </ul>
  );
}
```

### 状態管理（Zustand）

```typescript
// stores/player-store.ts
import { create } from 'zustand';
import { Track } from '@/types/track';

interface PlayerState {
  currentTrack: Track | null;
  isPlaying: boolean;
  play: (track: Track) => void;
  pause: () => void;
}

export const usePlayerStore = create<PlayerState>((set) => ({
  currentTrack: null,
  isPlaying: false,
  play: (track) => set({ currentTrack: track, isPlaying: true }),
  pause: () => set({ isPlaying: false }),
}));

// テスト時は初期状態を注入可能
// usePlayerStore.setState({ currentTrack: mockTrack, isPlaying: true });
```

### Repository Pattern（DI でモック可能）

```typescript
// server/repositories/track-repository.ts
import { SupabaseClient } from '@supabase/supabase-js';
import { Track, CreateTrackInput } from '@/types/track';

export interface ITrackRepository {
  findAll(): Promise<Track[]>;
  create(input: CreateTrackInput): Promise<Track>;
}

export function createTrackRepository(supabase: SupabaseClient): ITrackRepository {
  return {
    async findAll() {
      const { data, error } = await supabase
        .from('tracks')
        .select('*');
      if (error) throw error;
      return data;
    },
    async create(input) {
      const { data, error } = await supabase
        .from('tracks')
        .insert(input)
        .select()
        .single();
      if (error) throw error;
      return data;
    },
  };
}

// server/services/track-service.ts
export function createTrackService(repo: ITrackRepository) {
  return {
    async findAll() {
      return repo.findAll();
    },
    async create(input: CreateTrackInput) {
      // ビジネスロジック（バリデーション等）
      return repo.create(input);
    },
  };
}

// テスト時
const mockRepo: ITrackRepository = {
  findAll: vi.fn().mockResolvedValue([mockTrack]),
  create: vi.fn().mockResolvedValue(mockTrack),
};
const service = createTrackService(mockRepo);
```

### API モック（MSW）

```typescript
// __tests__/mocks/handlers.ts
import { http, HttpResponse } from 'msw';

export const handlers = [
  http.get('/api/tracks', () => {
    return HttpResponse.json([
      { id: '1', title: 'Test Track', artist: 'Test Artist' },
    ]);
  }),
  http.post('/api/tracks', async ({ request }) => {
    const body = await request.json();
    return HttpResponse.json({ id: '2', ...body }, { status: 201 });
  }),
];

// __tests__/mocks/supabase.ts
export const mockSupabase = {
  from: vi.fn(() => ({
    select: vi.fn().mockReturnThis(),
    insert: vi.fn().mockReturnThis(),
    single: vi.fn().mockResolvedValue({ data: mockTrack, error: null }),
  })),
};
```

### テスト戦略

| レイヤー | テスト種別 | モック対象 |
|---------|-----------|-----------|
| UI Components | Unit | なし（props のみ） |
| Container | Integration | Store をモック |
| Stores | Unit | API Client をモック |
| Services | Unit | Repository をモック |
| Repositories | Integration | Supabase をモック |
| API Routes | Integration | Service をモック or MSW |

## Module Boundaries

```
┌─────────────────────────────────────────────────────────┐
│                      app/ (Pages)                       │
│                          ↓                              │
│                   components/                           │
│                      ↓    ↓                             │
│               hooks/       lib/                         │
│                  ↓          ↓                           │
│            ┌─────┴──────────┴─────┐                     │
│            │     server/          │ ← API Routes only   │
│            │  services/repos      │                     │
│            └──────────┬───────────┘                     │
│                       ↓                                 │
│                   Supabase                              │
└─────────────────────────────────────────────────────────┘

依存方向: 上 → 下（逆方向の依存は禁止）
```

## Code Size Guidelines

- **ファイル**: 300行以下を目安
- **関数/メソッド**: 50行以下を目安
- **コンポーネント**: props 10個以下、分割を検討
- **ネスト深度**: 最大3レベル

## Documentation Standards

- **公開関数**: JSDoc コメント必須
- **複雑なロジック**: インラインコメント追加
- **型定義**: 必要に応じてコメント追加
- **README**: 各主要ディレクトリに配置（必要に応じて）
