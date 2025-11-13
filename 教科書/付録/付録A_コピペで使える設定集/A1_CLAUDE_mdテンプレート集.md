# A1. CLAUDE.mdテンプレート集

## 基本テンプレート

### 最小構成テンプレート

```markdown
# Project Configuration

## Project Overview
プロジェクトの簡単な説明

## Development Rules
- コードは可読性を重視する
- エラーハンドリングを適切に実装する
- テストを書く

## Code Style
- インデント: スペース2つ
- 命名規則: camelCase
- コメントは日本語でも可
```

---

## フロントエンド開発用テンプレート

### React + TypeScript プロジェクト

```markdown
# React TypeScript Project Configuration

## Project Overview
- Framework: React 18
- Language: TypeScript
- Build Tool: Vite
- State Management: React Context / Redux Toolkit
- Styling: Tailwind CSS / CSS Modules

## Directory Structure
```
src/
├── components/     # 再利用可能なコンポーネント
├── pages/          # ページコンポーネント
├── hooks/          # カスタムフック
├── contexts/       # Context API
├── utils/          # ユーティリティ関数
├── types/          # TypeScript型定義
├── api/            # API通信
└── assets/         # 静的ファイル
```

## MUST - Critical Rules
- MUST すべてのコンポーネントはTypeScriptで型定義すること
- MUST PropsとStateには明示的な型を指定すること
- MUST useEffectの依存配列を正しく設定すること
- MUST イベントハンドラーでは適切な型を使用すること（React.MouseEvent など）

## Component Guidelines
- 関数コンポーネントを使用（クラスコンポーネント禁止）
- Propsは interface で定義
- デフォルト export ではなく named export を推奨
- コンポーネント名は PascalCase
- ファイル名はコンポーネント名と一致させる

### Component Template
```typescript
import { FC } from 'react';

interface ButtonProps {
  label: string;
  onClick: () => void;
  variant?: 'primary' | 'secondary';
  disabled?: boolean;
}

export const Button: FC<ButtonProps> = ({
  label,
  onClick,
  variant = 'primary',
  disabled = false
}) => {
  return (
    <button
      onClick={onClick}
      disabled={disabled}
      className={`btn btn-${variant}`}
    >
      {label}
    </button>
  );
};
```

## State Management Rules
- ローカルステートは useState を使用
- グローバルステートは Context API または Redux Toolkit
- フォームステートは React Hook Form を推奨
- サーバーステートは TanStack Query (React Query) を推奨

## API Communication
- axiosまたはfetchを使用
- 環境変数でAPIエンドポイントを管理
- エラーハンドリングを必ず実装
- ローディング状態とエラー状態を管理

### API Client Template
```typescript
import axios from 'axios';

const apiClient = axios.create({
  baseURL: import.meta.env.VITE_API_BASE_URL,
  timeout: 10000,
  headers: {
    'Content-Type': 'application/json',
  },
});

// リクエストインターセプター
apiClient.interceptors.request.use(
  (config) => {
    const token = localStorage.getItem('token');
    if (token) {
      config.headers.Authorization = `Bearer ${token}`;
    }
    return config;
  },
  (error) => Promise.reject(error)
);

// レスポンスインターセプター
apiClient.interceptors.response.use(
  (response) => response,
  (error) => {
    if (error.response?.status === 401) {
      // 認証エラー処理
      window.location.href = '/login';
    }
    return Promise.reject(error);
  }
);

export default apiClient;
```

## Testing Guidelines
- テストフレームワーク: Vitest
- React Testing Library を使用
- ユーザーの振る舞いをテスト（実装詳細ではない）
- アクセシビリティを考慮したセレクター使用

## Performance Optimization
- React.memoでコンポーネントをメモ化
- useCallbackでコールバック関数をメモ化
- useMemoで計算結果をメモ化
- Code Splitting（React.lazyとSuspense）を活用
- 画像の最適化（WebP形式、lazy loading）

## Security Rules
- XSS対策: dangerouslySetInnerHTMLの使用を最小限に
- CSRF対策: トークンベースの認証
- 環境変数で機密情報を管理（.envファイル）
- HTTPSを使用
- Content Security Policy（CSP）の設定

## Accessibility Guidelines
- セマンティックHTML要素を使用
- ARIAラベルを適切に設定
- キーボード操作をサポート
- 色のコントラスト比を確保（WCAG AA基準）
- フォーカスインジケーターを表示
```

---

### Vue.js プロジェクト

```markdown
# Vue.js Project Configuration

## Project Overview
- Framework: Vue 3
- Language: TypeScript / JavaScript
- Build Tool: Vite
- State Management: Pinia
- Router: Vue Router

## MUST - Critical Rules
- MUST Composition APIを使用すること（Options API非推奨）
- MUST <script setup>構文を使用すること
- MUST コンポーネントは単一ファイルコンポーネント(.vue)とすること
- MUST propsとemitsには型を定義すること

## Component Structure
```vue
<script setup lang="ts">
import { ref, computed } from 'vue';

interface Props {
  title: string;
  count?: number;
}

const props = withDefaults(defineProps<Props>(), {
  count: 0
});

const emit = defineEmits<{
  (e: 'update', value: number): void;
}>();

const localCount = ref(props.count);

const doubleCount = computed(() => localCount.value * 2);

const increment = () => {
  localCount.value++;
  emit('update', localCount.value);
};
</script>

<template>
  <div class="component">
    <h2>{{ title }}</h2>
    <p>Count: {{ localCount }}</p>
    <p>Double: {{ doubleCount }}</p>
    <button @click="increment">Increment</button>
  </div>
</template>

<style scoped>
.component {
  padding: 1rem;
}
</style>
```

## Naming Conventions
- コンポーネント名: PascalCase（例: UserProfile.vue）
- ファイル名: コンポーネント名と一致
- イベント名: kebab-case（例: @update-value）
- Props: camelCase

## State Management with Pinia
```typescript
import { defineStore } from 'pinia';

export const useUserStore = defineStore('user', {
  state: () => ({
    user: null as User | null,
    isAuthenticated: false,
  }),

  getters: {
    userName: (state) => state.user?.name ?? 'Guest',
  },

  actions: {
    async login(credentials: LoginCredentials) {
      try {
        const response = await api.login(credentials);
        this.user = response.data.user;
        this.isAuthenticated = true;
      } catch (error) {
        console.error('Login failed:', error);
        throw error;
      }
    },

    logout() {
      this.user = null;
      this.isAuthenticated = false;
    },
  },
});
```
```

---

## バックエンド開発用テンプレート

### Node.js + Express + TypeScript

```markdown
# Node.js Express TypeScript Project

## Project Overview
- Runtime: Node.js 20+
- Framework: Express.js
- Language: TypeScript
- Database: PostgreSQL / MongoDB
- ORM: Prisma / Mongoose
- Authentication: JWT

## Directory Structure
```
src/
├── controllers/    # リクエストハンドラー
├── services/       # ビジネスロジック
├── models/         # データモデル
├── routes/         # ルート定義
├── middlewares/    # カスタムミドルウェア
├── utils/          # ユーティリティ
├── config/         # 設定ファイル
├── types/          # TypeScript型定義
└── tests/          # テストファイル
```

## MUST - Critical Rules
- MUST すべてのエンドポイントでバリデーションを実装すること
- MUST エラーハンドリングミドルウェアを使用すること
- MUST 環境変数で機密情報を管理すること
- MUST データベース接続はプール管理すること
- MUST ログを適切に記録すること

## Controller Pattern
```typescript
import { Request, Response, NextFunction } from 'express';
import { UserService } from '../services/user.service';
import { CreateUserDto } from '../types/user.dto';

export class UserController {
  constructor(private userService: UserService) {}

  async createUser(req: Request, res: Response, next: NextFunction) {
    try {
      const userData: CreateUserDto = req.body;
      const user = await this.userService.createUser(userData);

      res.status(201).json({
        success: true,
        data: user,
      });
    } catch (error) {
      next(error);
    }
  }

  async getUser(req: Request, res: Response, next: NextFunction) {
    try {
      const { id } = req.params;
      const user = await this.userService.getUserById(id);

      if (!user) {
        return res.status(404).json({
          success: false,
          message: 'User not found',
        });
      }

      res.json({
        success: true,
        data: user,
      });
    } catch (error) {
      next(error);
    }
  }
}
```

## Error Handling
```typescript
import { Request, Response, NextFunction } from 'express';

export class AppError extends Error {
  constructor(
    public statusCode: number,
    public message: string,
    public isOperational = true
  ) {
    super(message);
    Error.captureStackTrace(this, this.constructor);
  }
}

export const errorHandler = (
  err: Error,
  req: Request,
  res: Response,
  next: NextFunction
) => {
  if (err instanceof AppError) {
    return res.status(err.statusCode).json({
      success: false,
      message: err.message,
    });
  }

  console.error('ERROR:', err);

  res.status(500).json({
    success: false,
    message: 'Internal server error',
  });
};
```

## Validation with Zod
```typescript
import { z } from 'zod';
import { Request, Response, NextFunction } from 'express';

const createUserSchema = z.object({
  body: z.object({
    email: z.string().email(),
    password: z.string().min(8),
    name: z.string().min(2).max(50),
  }),
});

export const validate = (schema: z.ZodSchema) => {
  return (req: Request, res: Response, next: NextFunction) => {
    try {
      schema.parse({
        body: req.body,
        query: req.query,
        params: req.params,
      });
      next();
    } catch (error) {
      if (error instanceof z.ZodError) {
        return res.status(400).json({
          success: false,
          errors: error.errors,
        });
      }
      next(error);
    }
  };
};
```

## Database Guidelines
- トランザクションを適切に使用
- インデックスを最適化
- N+1問題を避ける
- コネクションプールを使用
- マイグレーションファイルを管理

## Security Best Practices
- Helmet.jsでHTTPヘッダーを保護
- Rate limitingを実装
- CORS設定を適切に
- SQLインジェクション対策（プリペアドステートメント）
- パスワードはbcryptでハッシュ化
- JWTの有効期限を設定
```

---

## フルスタックプロジェクト用テンプレート

### Next.js フルスタック

```markdown
# Next.js Full-Stack Project

## Project Overview
- Framework: Next.js 14+ (App Router)
- Language: TypeScript
- Styling: Tailwind CSS
- Database: PostgreSQL + Prisma
- Authentication: NextAuth.js

## MUST - Critical Rules
- MUST Server ComponentsとClient Componentsを適切に使い分けること
- MUST データフェッチはServer Componentsで行うこと
- MUST 環境変数は.env.localで管理すること
- MUST APIルートでバリデーションを実装すること

## Directory Structure (App Router)
```
app/
├── (auth)/              # 認証関連ページ
├── (dashboard)/         # ダッシュボード
├── api/                 # APIルート
├── components/          # 共有コンポーネント
├── lib/                 # ユーティリティ
└── types/               # 型定義

prisma/
├── schema.prisma        # データベーススキーマ
└── migrations/          # マイグレーション
```

## Server Components vs Client Components
- デフォルトはServer Components
- インタラクション必要な場合のみ'use client'
- Server Componentsでデータフェッチ
- Client Componentsは最小限に

## API Route Pattern
```typescript
import { NextRequest, NextResponse } from 'next/server';
import { z } from 'zod';
import { prisma } from '@/lib/prisma';

const postSchema = z.object({
  title: z.string().min(1).max(200),
  content: z.string().min(1),
});

export async function POST(req: NextRequest) {
  try {
    const body = await req.json();
    const validatedData = postSchema.parse(body);

    const post = await prisma.post.create({
      data: validatedData,
    });

    return NextResponse.json({ success: true, data: post }, { status: 201 });
  } catch (error) {
    if (error instanceof z.ZodError) {
      return NextResponse.json(
        { success: false, errors: error.errors },
        { status: 400 }
      );
    }

    return NextResponse.json(
      { success: false, message: 'Internal server error' },
      { status: 500 }
    );
  }
}
```
```

---

## モノレポ用テンプレート

```markdown
# Monorepo Project Configuration

## Project Overview
- Monorepo Tool: Turborepo / Nx
- Package Manager: pnpm
- Language: TypeScript

## Workspace Structure
```
packages/
├── web/            # Next.js アプリ
├── api/            # Express API
├── mobile/         # React Native アプリ
├── ui/             # 共有UIコンポーネント
├── utils/          # 共有ユーティリティ
├── types/          # 共有型定義
└── config/         # 共有設定

apps/
└── docs/           # ドキュメントサイト
```

## MUST - Critical Rules
- MUST 共有コードは packages に配置すること
- MUST 循環依存を避けること
- MUST package.json の dependencies を適切に管理すること
- MUST 共有パッケージはバージョニングすること

## Package Dependencies
- 内部パッケージは workspace:* で参照
- 外部依存は各パッケージで個別管理
- 共通の依存はルートでインストール可能

## Build Configuration
```json
{
  "pipeline": {
    "build": {
      "dependsOn": ["^build"],
      "outputs": ["dist/**", ".next/**"]
    },
    "test": {
      "dependsOn": ["build"],
      "outputs": []
    },
    "lint": {
      "outputs": []
    },
    "dev": {
      "cache": false
    }
  }
}
```
```

---

## まとめ

このテンプレート集は、プロジェクトの種類に応じて適切なCLAUDE.mdを素早く作成するためのものです。

**使い方:**
1. プロジェクトに合ったテンプレートを選択
2. CLAUDE.mdにコピー&ペースト
3. プロジェクト固有の情報を追記
4. Claude Codeを再起動

各テンプレートは実践的な開発パターンとベストプラクティスを含んでいるため、すぐに活用できます。
