# 主要なMCPサーバーの紹介

## はじめに

この章では、実際に使える主要なMCPサーバーを詳しく紹介します。各MCPサーバーの特徴、インストール方法、そして具体的な活用例を学びます。

## Serena

### 概要

**Serena**は、Claude Codeを賢くしてくれると評判のMCPサーバーです。プロジェクトのコンテキストを理解し、より適切な提案を可能にします。

**公式リポジトリ:**
- https://github.com/oraios/serena

### 特徴

- ✅ IDE統合: プロジェクトのコンテキストを理解
- ✅ プロジェクト構造の把握: ディレクトリ構造を自動認識
- ✅ スマートな提案: コンテキストに基づいた適切な提案

### インストール方法

#### 前提条件

`mise`（またはuvパッケージマネージャー）を使用します。

```bash
# miseのインストール（まだの場合）
curl https://mise.run | sh

# uvのインストール
mise use uv
```

#### インストールコマンド

```bash
# プロジェクトディレクトリで実行
claude mcp add serena -- uvx --from git+https://github.com/oraios/serena serena start-mcp-server --enable-web-dashboard false --context ide-assistant --project $(pwd)
```

**コマンドの説明:**
- `claude mcp add serena` - SerenaをMCPサーバーとして追加
- `uvx` - uvパッケージランナー
- `--from git+https://github.com/oraios/serena` - GitHubから直接インストール
- `--context ide-assistant` - IDEアシスタントモードで起動
- `--project $(pwd)` - 現在のプロジェクトをコンテキストとして設定

### 活用例

#### Custom Slash Commandと組み合わせる

```markdown
# .claude/commands/review-with-serena.md

---
description: Serenaのコンテキストを活用した高度なコードレビュー
---

Serenaを使って、プロジェクトの全体像を理解した上でコードレビューを実行してください。

特に以下に注意:
- プロジェクトのアーキテクチャとの整合性
- 既存のコーディング規約への準拠
- プロジェクト固有のパターンとの一貫性
```

#### Sub Agentと組み合わせる

```markdown
# .claude/agents/serena-powered-reviewer.md

---
name: serena-powered-reviewer
description: Serenaのコンテキストを活用した高度なコードレビュー専門家
tools: Read, Grep, Glob
model: inherit
---

あなたはSerenaのコンテキストを活用するコードレビュー専門家です。

## レビュープロセス

1. Serenaからプロジェクト構造を取得
2. プロジェクトのアーキテクチャパターンを理解
3. そのコンテキストに基づいてコードをレビュー
```

## Figma MCP

### 概要

**Figma MCP**は、FigmaのデザインファイルとClaude Codeを連携させるMCPサーバーです。デザインから直接コードを生成できます。

### 特徴

- ✅ デザインファイルの読み取り
- ✅ コンポーネント情報の取得
- ✅ SVGアセットの自動ダウンロード
- ✅ デザインスペック（サイズ、色、フォントなど）の抽出

### インストール方法

#### ステップ1: Figmaアクセストークンの取得

1. Figmaにログイン
2. 設定（Settings） → アカウント（Account） → Personal Access Tokens
3. 「Generate new token」をクリック
4. トークンに名前を付ける（例: `Claude Code Integration`）
5. トークンをコピー（一度しか表示されない）

#### ステップ2: MCPサーバーの追加

```bash
# 環境変数を設定（.zshrcや.bashrcに追加を推奨）
export FIGMA_ACCESS_TOKEN="your-figma-token-here"

# Figma MCPサーバーを追加
claude mcp add figma npx @anthropic-ai/figma-mcp
```

#### ステップ3: 動作確認

```bash
# MCPサーバーの一覧を確認
claude mcp list

# Figmaが表示されればOK
```

### 使用方法

Claude Codeセッション内で、FigmaファイルのURLを共有するだけです。

```
> このFigmaファイルからButtonコンポーネントを実装してください
> https://www.figma.com/file/xxxxx/Design-System
```

Claude Codeが自動的に:
1. Figmaファイルにアクセス
2. デザインを解析
3. Reactコンポーネントとして実装
4. Tailwind CSSでスタイリング

### 活用例

#### デザインシステムの実装

```
> Figmaのデザインシステムから、以下のコンポーネントを実装してください：
> - Button（Primary, Secondary, Outline）
> - Input（Text, Password, Email）
> - Card
>
> Figma URL: https://www.figma.com/file/xxxxx/Design-System
```

Claude Codeの応答例:
```
Figmaファイルを取得しました。デザインシステムを分析します...

【分析結果】
- カラーパレット: Blue 600 (Primary), Gray scale
- タイポグラフィ: Inter フォントファミリー
- スペーシング: 8pxグリッド

【実装開始】
1. Button コンポーネント
2. Input コンポーネント
3. Card コンポーネント

各コンポーネントのプロップスとバリエーションも実装します。
```

## Context7 MCP

### 概要

**Context7**は、最新のライブラリドキュメントを参照できるMCPサーバーです。Claude Codeの知識カットオフを超えて、常に最新の情報にアクセスできます。

### 特徴

- ✅ 最新のライブラリドキュメント参照
- ✅ バージョン固有の情報取得
- ✅ 正確なAPIリファレンス
- ✅ ベストプラクティスの提供

### インストール方法

```bash
# Context7 MCPサーバーを追加（SSE方式）
claude mcp add --transport sse context7 https://mcp.context7.com/sse
```

**注意:** Context7はリモートサーバーで動作するため、`--transport sse`オプションが必要です。

### 使用方法

特別な指示は不要です。Claude Codeが自動的にContext7を使って最新情報を取得します。

```
> Next.js 15のApp Routerで、Server Actionsを使ってフォームを実装してください
```

Claude Codeが自動的に:
1. Context7から最新のNext.js 15ドキュメントを取得
2. Server Actionsの正確な構文を確認
3. 最新のベストプラクティスに従って実装

### 活用例

#### 最新フレームワークの学習

```
> React 19の新機能「Use Hook」について教えてください。具体的な使用例も含めて。
```

#### バージョン固有の実装

```
> TypeScript 5.3のsatisfiesオペレーターを使って、型安全な設定オブジェクトを作成してください
```

#### ライブラリの比較

```
> Zustandとjotaiを比較して、状態管理ライブラリを選定したいです。
> 最新のドキュメントを参照して、それぞれの特徴と使い分けを教えてください。
```

## Chrome DevTools MCP

### 概要

**Chrome DevTools MCP**は、Chromeブラウザのデベロッパーツールと連携し、パフォーマンス分析やデバッグを可能にします。

### 特徴

- ✅ パフォーマンスプロファイリング
- ✅ Core Web Vitals計測
- ✅ ネットワーク分析
- ✅ コンソールログの取得
- ✅ ブラウザでのデバッグ作業
- ✅ Puppeteerを使った自動化

### インストール方法

```bash
# Chrome DevTools MCPサーバーを追加
claude mcp add chrome-devtools npx chrome-devtools-mcp
```

### 使用方法

```
> 本番環境のパフォーマンスを分析してください
> URL: https://example.com
```

Claude Codeが自動的に:
1. Chromeブラウザを起動
2. 対象URLにアクセス
3. DevToolsでパフォーマンスを計測
4. Core Web Vitalsを取得（LCP, FID, CLS）
5. ボトルネックを分析
6. 改善提案をレポート

### 活用例

#### Core Web Vitals計測

```
> 以下のページのCore Web Vitalsを計測してください：
> - トップページ: https://example.com
> - 商品ページ: https://example.com/products/1
> - カートページ: https://example.com/cart
>
> 各ページの問題点を特定し、改善提案をしてください。
```

#### ネットワーク分析

```
> このページのネットワークリクエストを分析してください。
> URL: https://example.com
>
> 特に以下を確認:
> - 不要なリクエスト
> - サイズの大きいリソース
> - キャッシュ設定
```

## Playwright MCP

### 概要

**Playwright MCP**は、ブラウザの自動操作を可能にします。E2Eテストの生成、スクリーンショット取得、ユーザーフローの検証などができます。

### 特徴

- ✅ ブラウザの自動操作
- ✅ E2Eテストの生成
- ✅ スクリーンショット取得
- ✅ 複数ブラウザ対応（Chrome, Firefox, Safari）

### インストール方法

```bash
# Playwright MCPサーバーを追加
claude mcp add playwright npx @playwright/mcp
```

### 使用方法

```
> ログインフローのE2Eテストを作成してください
```

Claude Codeが自動的に:
1. Playwrightを起動
2. ログインフローを実行
3. 各ステップをキャプチャ
4. テストコードを生成

### 活用例

#### E2Eテストの生成

```
> 以下のユーザーフローのE2Eテストを作成してください：
>
> 1. トップページにアクセス
> 2. 「ログイン」ボタンをクリック
> 3. メールアドレスとパスワードを入力
> 4. 「ログイン」ボタンをクリック
> 5. ダッシュボードページが表示されることを確認
>
> テストフレームワーク: Playwright + TypeScript
```

#### スクリーンショットの取得

```
> 以下のページのスクリーンショットを取得してください：
> - デスクトップビュー（1920x1080）
> - タブレットビュー（768x1024）
> - モバイルビュー（375x667）
>
> URL: https://example.com
```

#### ビジュアルリグレッションテスト

```
> 本番環境とステージング環境のビジュアル差分をチェックしてください
> 本番: https://example.com
> ステージング: https://staging.example.com
```

## GitHub MCP

### 概要

**GitHub MCP**（コミュニティ版）は、GitHubの操作を自動化できます。

**注意:** 現在、GitHub MCPはコミュニティ版が主流です。公式版の開発も進行中です。

### 特徴

- ✅ Issue、PRの操作
- ✅ レビューコメントの追加
- ✅ ブランチ管理
- ✅ ラベルの操作

### インストール方法

```bash
# GitHub MCPサーバーを追加（コミュニティ版）
# 注: インストール方法はMCPサーバーの実装により異なる
# 公式版が利用可能になるまで、gh CLIを使用する方が確実
```

### 代替手段: gh CLI

現状では、Claude Code内で `gh` コマンドを直接使用する方が確実です。

```bash
# gh CLIのインストール
# macOS
brew install gh

# Windows
winget install GitHub.cli

# Linux (Debian/Ubuntu)
sudo apt install gh
```

### 使用例

```
> Issue #123の内容を確認して、それに基づいて実装してください

Claude Code:
```bash
# Issueの内容を取得
gh issue view 123
```

内容を確認し、実装を開始します...
```

## Notion MCP

### 概要

**Notion MCP**（ベータ版）は、Notionのドキュメントを参照できます。

**注意:** 2025年7月時点でベータ版です。フルアクセス権限のあるページのみ閲覧可能です。

### 特徴

- ✅ Notionページの読み取り
- ✅ データベースの参照
- ⚠️ フルアクセス権限が必要

### インストール方法

```bash
# Notion MCPサーバーを追加（SSE方式）
claude mcp add --transport sse notion https://mcp.notion.com/sse
```

### 使用方法

```
> この Notion ページの内容に基づいて、機能仕様書を作成してください
> https://www.notion.so/xxxxx/Feature-Spec
```

### 活用例

#### ドキュメントベースの実装

```
> NotionのAPI仕様書に基づいて、APIクライアントを実装してください
> Notion URL: https://www.notion.so/xxxxx/API-Spec
```

## その他のMCPサーバー

### MCPサーバーの探し方

公式のMCPサーバーカタログ:
```
https://code.visualstudio.com/mcp
```

ここで、以下のようなMCPサーバーが見つかります:
- データベース連携（PostgreSQL, MongoDB）
- クラウドサービス連携（AWS, GCP, Azure）
- 分析ツール連携（Google Analytics）
- コミュニケーションツール連携（Slack, Discord）

## MCPサーバーの組み合わせ

複数のMCPサーバーを組み合わせることで、さらに強力なワークフローを構築できます。

### 例1: デザインから実装、テストまで自動化

```
1. Figma MCP: デザインを取得
   ↓
2. Claude Code: コンポーネントを実装
   ↓
3. Playwright MCP: E2Eテストを生成
   ↓
4. Chrome DevTools MCP: パフォーマンスをチェック
```

**実行例:**
```
> Figmaのこのデザインから、Buttonコンポーネントを実装して、
> E2Eテストも作成し、最後にパフォーマンスをチェックしてください
>
> Figma URL: https://www.figma.com/file/xxxxx/Button
```

### 例2: 最新ドキュメントを参照しながら実装、PRまで作成

```
1. Context7 MCP: 最新のNext.js 15ドキュメントを参照
   ↓
2. Claude Code: App Routerで実装
   ↓
3. gh CLI（GitHub操作）: ブランチ作成、PR作成
```

**実行例:**
```
> Next.js 15の最新ドキュメントを参照して、App Routerで
> ユーザー認証機能を実装し、PRを作成してください
```

## MCPサーバーの選択基準

### プロジェクトの種類別

| プロジェクトタイプ | 推奨MCPサーバー |
|------------------|----------------|
| **Webアプリケーション** | Context7, Chrome DevTools, Playwright |
| **デザインシステム** | Figma, Chrome DevTools |
| **ライブラリ開発** | Context7, GitHub |
| **パフォーマンス重視** | Chrome DevTools, Playwright |
| **ドキュメントベース** | Notion, Context7 |

### 開発フェーズ別

| フェーズ | 推奨MCPサーバー |
|---------|----------------|
| **設計** | Figma, Notion |
| **実装** | Context7, Serena |
| **テスト** | Playwright, Chrome DevTools |
| **レビュー** | GitHub |
| **デプロイ** | Chrome DevTools（パフォーマンス確認） |

## まとめ

### 主要MCPサーバーの特徴

| MCPサーバー | 用途 | トランスポート | 推奨度 |
|------------|------|---------------|--------|
| **Serena** | コンテキスト理解 | stdio | ⭐⭐⭐⭐⭐ |
| **Figma** | デザイン連携 | stdio | ⭐⭐⭐⭐⭐ |
| **Context7** | 最新ドキュメント | SSE | ⭐⭐⭐⭐⭐ |
| **Chrome DevTools** | パフォーマンス | stdio | ⭐⭐⭐⭐ |
| **Playwright** | ブラウザ自動化 | stdio | ⭐⭐⭐⭐ |
| **GitHub** | GitHub操作 | stdio | ⭐⭐⭐ |
| **Notion** | ドキュメント参照 | SSE | ⭐⭐⭐（ベータ） |

### 導入の順序（推奨）

```
フェーズ1（必須）:
├── Context7（最新ドキュメント参照）
└── Serena（コンテキスト理解）

フェーズ2（プロジェクトに応じて）:
├── Figma（デザイン重視の場合）
├── Chrome DevTools（パフォーマンス重視の場合）
└── Playwright（テスト重視の場合）

フェーズ3（オプション）:
├── GitHub MCP
└── Notion MCP
```

### 次章の内容

次の章では、プロジェクトでのMCP設定方法を学びます。`.mcp.json`の作成、チーム共有、設定管理のベストプラクティスを詳しく解説します。
