# MCPとは

## はじめに

MCP（Model Context Protocol）は、Claude Codeの能力を外部サービスと連携させ、飛躍的に拡張する仕組みです。この章では、MCPの概念、メリット、仕組みを詳しく解説します。

## MCPの定義

### Model Context Protocol（MCP）とは

**MCP（Model Context Protocol）**は、AIモデルが外部のサービス、ツール、データソースと安全かつ標準化された方法で通信するためのプロトコルです。

Anthropic社が提唱し、オープンスタンダードとして公開されています。

### 公式の説明

> Model Context Protocolは、AIアシスタントと外部データソース、ツール、サービス間の標準化された通信を可能にします。これにより、開発者はAIアシスタントの能力を簡単に拡張できます。

### なぜMCPが必要なのか

**従来の課題:**

1. **各サービスで独自の連携方法**
   - GitHubはGitHub API
   - FigmaはFigma API
   - Notionはnotion API
   → 各サービスごとに実装が必要

2. **セキュリティの懸念**
   - 認証情報の管理が複雑
   - アクセス権限の制御が難しい

3. **保守性の問題**
   - APIの変更に追従するのが大変
   - サービスごとにコードが異なる

**MCPによる解決:**

1. **標準化されたインターフェース**
   - すべてのサービスが同じプロトコルで通信
   - 一度理解すれば、どのサービスにも応用可能

2. **セキュリティの向上**
   - 安全な認証メカニズム
   - 細かいアクセス権限制御

3. **保守性の向上**
   - プロトコルが安定しているため、変更に強い
   - 再利用可能なコンポーネント

## MCPの基本概念

### MCPサーバー

外部サービスとの橋渡しをするプログラムです。

```
Claude Code
    ↓
MCPクライアント（Claude Code内蔵）
    ↓
MCPサーバー（例: Figma MCPサーバー）
    ↓
外部サービス（Figma）
```

**主要なMCPサーバー:**
- Figma MCPサーバー
- GitHub MCPサーバー
- Notion MCPサーバー
- Context7 MCPサーバー（ドキュメント参照）
- Chrome DevTools MCPサーバー
- Playwright MCPサーバー

### MCPクライアント

Claude Code自体が、MCPクライアントとして機能します。MCPサーバーと通信し、外部サービスの機能を利用します。

### MCPツール

MCPサーバーが提供する具体的な機能です。

**例: Figma MCPサーバーが提供するツール**
- `figma-get-file`: Figmaファイルの取得
- `figma-get-components`: コンポーネント一覧の取得
- `figma-export-images`: 画像のエクスポート

## MCPのメリット

### 1. 能力の拡張

Claude Code単体では実現できない機能を追加できます。

**例:**
- **Figma連携**: デザインファイルを読み取り、コンポーネントを実装
- **GitHub連携**: Issue、PRの操作、レビュー、ブランチ管理
- **Notion連携**: ドキュメントの参照、タスク管理
- **Context7**: 最新のライブラリドキュメントを参照

### 2. リアルタイム情報の取得

Claude Codeの知識カットオフを超えて、最新の情報にアクセスできます。

**例:**
```
従来:
> Next.js 15の新機能を教えてください
Claude: 私の知識は2025年1月までです。最新のNext.js 15の情報は持っていません。

MCP（Context7）連携後:
> Next.js 15の新機能を教えてください
Claude: Context7から最新のNext.js 15ドキュメントを取得します...
       → 最新の情報を提供
```

### 3. 作業の自動化

外部サービスの操作を自動化できます。

**例:**
```
# Figmaから取得 → コンポーネント実装 → GitHubにPR作成
> Figmaのデザインファイルから、Buttonコンポーネントを実装して、PRを作成してください
```

### 4. 開発効率の向上

手動で行っていた作業をClaude Codeに任せられます。

**例:**
- デザインからコードへの変換
- ドキュメントの自動生成
- テストの自動実行とレポート
- パフォーマンステストの実行

## MCPの仕組み

### アーキテクチャ

```
┌─────────────────────────────────────────────┐
│          Claude Code（メイン）              │
│                                             │
│  ┌─────────────────────────────────────┐  │
│  │      MCPクライアント（内蔵）        │  │
│  └─────────────────────────────────────┘  │
│                    ↓                        │
└────────────────────┼────────────────────────┘
                     │ MCP Protocol
        ┌────────────┼────────────┐
        ↓            ↓            ↓
   ┌─────────┐  ┌─────────┐  ┌─────────┐
   │ Figma   │  │ GitHub  │  │Context7 │
   │  MCP    │  │  MCP    │  │  MCP    │
   │ Server  │  │ Server  │  │ Server  │
   └─────────┘  └─────────┘  └─────────┘
        ↓            ↓            ↓
   ┌─────────┐  ┌─────────┐  ┌─────────┐
   │  Figma  │  │ GitHub  │  │ Docs    │
   │   API   │  │   API   │  │  API    │
   └─────────┘  └─────────┘  └─────────┘
```

### 通信の流れ

```
1. ユーザー
   ↓
   「Figmaのデザインを取得してください」
   ↓
2. Claude Code（MCPクライアント）
   ↓
   Figma MCPサーバーに要求
   ↓
3. Figma MCPサーバー
   ↓
   Figma APIを呼び出し
   ↓
4. Figma API
   ↓
   デザインデータを返却
   ↓
5. Figma MCPサーバー
   ↓
   データをMCPプロトコル形式で返却
   ↓
6. Claude Code
   ↓
   デザインデータを処理、ユーザーに提示
```

### MCPサーバーの種類

#### 1. トランスポート方式: stdio

ローカルで実行されるプロセスと、標準入出力（stdin/stdout）で通信します。

**特徴:**
- ✅ シンプルな実装
- ✅ ローカル実行のため高速
- ⚠️ リモートアクセス不可

**例:**
```bash
# Figma MCPサーバー（stdio方式）
claude mcp add figma npx @anthropic-ai/figma-mcp
```

#### 2. トランスポート方式: SSE（Server-Sent Events）

HTTPベースで通信します。リモートサーバーとの通信に使用されます。

**特徴:**
- ✅ リモートサーバーにアクセス可能
- ✅ ファイアウォールを超えやすい
- ⚠️ レイテンシーがやや高い

**例:**
```bash
# Context7 MCPサーバー（SSE方式）
claude mcp add --transport sse context7 https://mcp.context7.com/sse
```

## MCPの設定ファイル

### プロジェクト設定: `.mcp.json`

プロジェクトルートに `.mcp.json` ファイルを作成することで、プロジェクト固有のMCP設定を管理できます。

```json
{
  "mcpServers": {
    "figma": {
      "command": "npx",
      "args": ["@anthropic-ai/figma-mcp"],
      "env": {
        "FIGMA_ACCESS_TOKEN": "your-token-here"
      }
    },
    "context7": {
      "transport": "sse",
      "url": "https://mcp.context7.com/sse"
    }
  }
}
```

### グローバル設定: `~/.claude/settings.json`

すべてのプロジェクトで共通して使うMCPサーバーは、グローバル設定に追加します。

```json
{
  "mcpServers": {
    "playwright": {
      "command": "npx",
      "args": ["@playwright/mcp"]
    }
  }
}
```

### 設定の優先順位

```
プロジェクト設定（.mcp.json）
    ↓ 優先
グローバル設定（~/.claude/settings.json）
```

同じ名前のMCPサーバーが定義されている場合、プロジェクト設定が優先されます。

## MCPの利用シーン

### シーン1: デザインからコード生成

**Figma MCP連携**

```
1. Figmaでデザインを作成
2. Claude CodeにFigmaファイルのURLを共有
3. Claude Codeがデザインを解析
4. Reactコンポーネントとして実装
5. Tailwind CSSでスタイリング
6. ストーリーブック用のストーリーも生成
```

### シーン2: 最新ドキュメントの参照

**Context7 MCP連携**

```
> Next.js 15のApp Routerで、Server Actionsを使ってフォームを実装してください

Claude Code:
1. Context7から最新のNext.js 15ドキュメントを取得
2. Server Actionsの正確な構文を確認
3. 最新のベストプラクティスに従って実装
4. TypeScript型定義も最新版を使用
```

### シーン3: ブラウザ操作の自動化

**Chrome DevTools MCP + Playwright MCP連携**

```
> 本番環境のパフォーマンスをテストしてください

Claude Code:
1. Playwrightでブラウザを起動
2. 対象サイトにアクセス
3. Chrome DevToolsでパフォーマンスを計測
4. Core Web Vitalsを取得
5. ボトルネックを分析
6. 改善提案をレポート
```

### シーン4: GitHub操作の自動化

**GitHub MCP連携**

```
> Issue #123の内容を実装して、PRを作成してください

Claude Code:
1. GitHub MCPでIssue #123を取得
2. 内容を理解
3. 新しいブランチを作成
4. コードを実装
5. テストを実行
6. コミット
7. PRを作成
8. Issue #123をPRに関連付け
```

## MCPのセキュリティ

### 認証情報の管理

MCPサーバーは、外部サービスの認証情報を必要とします。

**安全な管理方法:**

1. **環境変数の使用**
   ```json
   {
     "mcpServers": {
       "figma": {
         "command": "npx",
         "args": ["@anthropic-ai/figma-mcp"],
         "env": {
           "FIGMA_ACCESS_TOKEN": "${FIGMA_ACCESS_TOKEN}"
         }
       }
     }
   }
   ```

2. **`.mcp.json` を `.gitignore` に追加**
   ```
   # .gitignore
   .mcp.json
   ```

3. **テンプレートファイルの提供**
   ```
   # .mcp.json.example
   {
     "mcpServers": {
       "figma": {
         "command": "npx",
         "args": ["@anthropic-ai/figma-mcp"],
         "env": {
           "FIGMA_ACCESS_TOKEN": "YOUR_TOKEN_HERE"
         }
       }
     }
   }
   ```

### アクセス権限の制御

MCPサーバーには、必要最小限の権限のみを付与します。

**例: Figmaトークン**
- ✅ 読み取り専用トークンを使用（デザインの参照のみ）
- ❌ 書き込み権限は付与しない（誤ってデザインを変更しないため）

## MCPのメンテナンス

### MCPサーバーの更新

```bash
# インストール済みのMCPサーバー一覧を表示
claude mcp list

# 特定のMCPサーバーを更新（最新版に）
npm update -g @anthropic-ai/figma-mcp

# すべてのMCPサーバーを最新版に更新
claude mcp update
```

### トラブルシューティング

**問題: MCPサーバーが起動しない**

```bash
# MCPサーバーのログを確認
claude mcp logs figma

# MCPサーバーを再起動
claude mcp restart figma
```

## MCPのエコシステム

### 公式MCPサーバー

Anthropic社が提供する公式MCPサーバー:
- `@anthropic-ai/figma-mcp` - Figma連携
- その他、順次追加予定

### サードパーティMCPサーバー

コミュニティが開発したMCPサーバー:
- Context7 - ドキュメント参照
- Chrome DevTools MCP
- Playwright MCP
- GitHub MCP（コミュニティ版）
- Notion MCP（ベータ版）

### MCPサーバーの一覧

公式のMCPサーバーカタログ:
- [MCP Servers for Visual Studio Code](https://code.visualstudio.com/mcp)

## まとめ

### MCPの重要なポイント

1. **標準化**: すべての外部サービスが統一されたプロトコルで通信
2. **拡張性**: 新しいサービスを簡単に追加できる
3. **セキュリティ**: 安全な認証と権限管理
4. **保守性**: プロトコルが安定しているため、長期的に使いやすい

### MCPで実現できること

- ✅ デザインツールとの連携（Figma）
- ✅ 最新ドキュメントの参照（Context7）
- ✅ ブラウザ操作の自動化（Chrome DevTools, Playwright）
- ✅ GitHub操作の自動化（GitHub MCP）
- ✅ ドキュメント管理（Notion MCP）

### 次章の内容

次の章では、主要なMCPサーバーを詳しく紹介します。各MCPサーバーの特徴、インストール方法、活用例を具体的に学びます。
