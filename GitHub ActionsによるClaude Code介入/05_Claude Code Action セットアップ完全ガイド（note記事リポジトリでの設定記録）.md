---
created: 2025-11-04
tags:
aliases: 
related: 
---
# Claude Code Action セットアップ完全ガイド

**作成日**: 2025年11月3日  
**リポジトリ**: `rm007080/note_Obsidian`  
**環境**: Windows 11 + WSL (Ubuntu) + PowerShell + Cursor IDE

---

## 📋 目次

1. [概要](#概要)
2. [前提条件](#前提条件)
3. [セットアップ手順（完全版）](#セットアップ手順完全版)
4. [発生したエラーと解決策](#発生したエラーと解決策)
5. [重要な学習ポイント](#重要な学習ポイント)
6. [今後のリポジトリでの作業手順](#今後のリポジトリでの作業手順)
7. [トラブルシューティング](#トラブルシューティング)
8. [参考情報](#参考情報)

---

## 概要

### Claude Code Actionとは

GitHub上のIssueやPull Requestのコメントで `@claude` を使うことで、Claude AIがコードレビュー、質問への回答、コード実装などを自動で行ってくれるGitHub Actionsの機能。

### 実現できること

- ✅ Issueでの質問に自動回答
- ✅ Pull Requestの自動レビュー
- ✅ コードの実装・修正
- ✅ ブランチの自動作成
- ✅ コミットとPRの自動作成

### 料金

- **Claude Pro/Max**: OAuth Token使用で追加料金なし（サブスクに含まれる）
- **その他**: API Key使用で従量課金

---

## 前提条件

### 必須要件

- [x] GitHubアカウント
- [x] リポジトリの管理者権限
- [x] Claude Pro/Maxプラン（OAuth Token使用の場合）
- [x] Node.js + npm（OAuth Token生成用）

### 環境

- **OS**: Windows 11
- **シェル**: PowerShell 7 / WSL (Ubuntu)
- **IDE**: Cursor
- **Git**: インストール済み

---

## セットアップ手順（完全版）

### ステップ1: Claude GitHub Appのインストール

#### 1-1. アプリのインストールページにアクセス

```
https://github.com/apps/claude
```

#### 1-2. インストール

1. **「Install」**または**「Configure」**ボタンをクリック
2. インストール先を選択：個人アカウント（`rm007080`）
3. リポジトリの選択：
   - **推奨**: 「Only select repositories」を選択
   - リポジトリを個別に選択（`note_Obsidian`）
   - セキュリティ理由：最小権限の原則
4. **「Install」**をクリック

#### 1-3. 確認

インストール済みアプリの確認：
```
https://github.com/settings/installations
```

**権限内容**:
- Contents: Read & Write
- Issues: Read & Write
- Pull requests: Read & Write

---

### ステップ2: OAuth Tokenの生成

#### 2-1. 環境の選択

**PowerShell推奨** (Windows環境で実行が簡単)

理由：
- ✅ 日本語パスの問題が起きない
- ✅ パス変換が不要
- ✅ Cursorのワークスペースと同じ環境

**WSLでも可能** (Linux環境)

注意点：
- ⚠️ パスを `/mnt/g/...` に変換する必要がある
- ⚠️ 日本語パスで問題が起きる可能性

#### 2-2. OAuth Token生成コマンド

**PowerShellで実行**:
```powershell
npx claude setup-token
```

**WSLで実行**:
```bash
npx claude setup-token
```

**注意**: パッケージ名は `claude` (~~`@anthropic-ai/claude-cli`~~ ではない)

#### 2-3. 認証フロー

1. コマンド実行後、**ブラウザが自動的に開く**
2. Claude.aiにログイン（既にログイン済みの場合はスキップ）
3. **「Authorize」**ボタンをクリック
4. ターミナルに戻ると**トークンが表示される**

#### 2-4. トークンの保存

表示例：
```
✓ Long-lived authentication token created successfully!

Your OAuth token (valid for 1 year):

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

Store this token securely. You won't be able to see it again.
```

**重要な特徴**:
- ✅ **1年間有効**な長期トークン
- ✅ 複数リポジトリで使い回せる
- ⚠️ **一度しか表示されない**（必ず安全な場所に保存）

**保存場所の推奨**:
- パスワードマネージャー（1Password, Bitwarden等）
- 暗号化されたテキストファイル
- このリポジトリの `CLAUDE_TEMPLATE.txt` (Gitに含めない場合)

---

### ステップ3: GitHub Secretsへの追加

#### 3-1. Secretsページにアクセス

**方法1: 直接URL**
```
https://github.com/rm007080/note_Obsidian/settings/secrets/actions
```

**方法2: 手動ナビゲーション**
1. GitHubでリポジトリにアクセス
2. **Settings** タブをクリック
3. 左サイドバー → **Secrets and variables** → **Actions**

#### 3-2. Secretsとは

**Secret（シークレット）**:
- 機密情報を安全に保管する仕組み
- 暗号化されて保存される
- 一度保存すると中身は見られない
- GitHub Actionsのワークフローからのみアクセス可能
- ログに出力されても自動的にマスク（`***`）

**Variablesとの違い**:

| 項目 | Secrets 🔒 | Variables 📝 |
|------|-----------|-------------|
| 用途 | 機密情報 | 非機密の設定値 |
| 暗号化 | ✅ される | ❌ されない |
| 表示 | ❌ 見られない | ✅ いつでも見られる |
| ログ | 自動マスク | そのまま表示 |
| 例 | APIキー、パスワード | 環境名、バージョン |

#### 3-3. Secretの追加

1. **「New repository secret」**ボタンをクリック
2. 入力内容：
   - **Name（名前）**: `CLAUDE_CODE_OAUTH_TOKEN`
   - **Secret（値）**: 生成したOAuth Token全体をコピペ
3. **「Add secret」**をクリック

**命名規則**:

✅ 正しい例:
```
CLAUDE_CODE_OAUTH_TOKEN
API_KEY
DATABASE_PASSWORD
```

❌ 間違った例:
```
claude_token          (小文字)
Claude-Token          (ハイフン使用)
GITHUB_MY_TOKEN       (GITHUB_で始まる予約語)
```

**技術的な制限**:
- 使える文字: `A-Z`, `0-9`, `_`（アンダースコア）
- 予約語: `GITHUB_`で始まる名前は使用不可

#### 3-4. 確認

保存後の表示：
```
Repository secrets:
  CLAUDE_CODE_OAUTH_TOKEN    Updated now    [Update] [Remove]
```

---

### ステップ4: ワークフローファイルの作成

#### 4-1. ディレクトリ構造

```
note_Obsidian/
├── .github/
│   └── workflows/
│       └── claude.yml  ← このファイルを作成
├── README.md
└── ...
```

#### 4-2. ディレクトリの作成

**PowerShellで実行**:
```powershell
cd "G:\マイドライブ\01_Obsidian\11_note\note_Obsidian"
New-Item -Path ".github\workflows" -ItemType Directory -Force
```

**または**:
```powershell
mkdir -Force .github/workflows
```

**WSLで実行**:
```bash
cd /mnt/g/マイドライブ/01_Obsidian/11_note/note_Obsidian
mkdir -p .github/workflows
```

#### 4-3. ワークフローファイルの作成

ファイル名: `.github/workflows/claude.yml`

```yaml
name: Claude Code Action

on:
  issue_comment:
    types: [created]
  pull_request_review_comment:
    types: [created]

permissions:
  contents: write
  issues: write
  pull-requests: write

jobs:
  claude-response:
    # Only run on comments that start with @claude
    if: |
      (github.event.comment.body != null && startsWith(github.event.comment.body, '@claude')) ||
      (github.event.comment.body != null && contains(github.event.comment.body, '@claude'))
    
    runs-on: ubuntu-latest
    
    steps:
      # Checkout the repository code
      - name: Checkout repository
        uses: actions/checkout@v4
      
      - name: Run Claude Code Action
        uses: anthropics/claude-code-action@v1
        with:
          # Use OAuth token (for Pro/Max users)
          claude_code_oauth_token: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}
          
          # GitHub token (automatically provided by GitHub Actions)
          github_token: ${{ secrets.GITHUB_TOKEN }}
          
          # Optional: Specify Claude model (defaults to claude-sonnet-4-20250514)
          # model: claude-sonnet-4-20250514
          
          # Optional: Maximum tokens for response
          # max_tokens: 4096
```

**重要なポイント**:

1. **`actions/checkout@v4` は必須**
   - これがないと `fatal: not a git repository` エラーが発生
   - Claude Code Actionがリポジトリにアクセスするために必要

2. **トリガー条件**
   - `issue_comment`: Issueのコメント
   - `pull_request_review_comment`: PRのレビューコメント
   - 条件: コメントに `@claude` が含まれる

3. **権限設定**
   - `contents: write`: コードの読み書き
   - `issues: write`: Issue操作
   - `pull-requests: write`: PR操作

4. **Secretsの参照**
   - `${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}`: 設定したSecret
   - `${{ secrets.GITHUB_TOKEN }}`: GitHub Actionsが自動提供

---

### ステップ5: GitHubにプッシュ

#### 5-1. Git操作（PowerShell推奨）

```powershell
# 現在の状態を確認
git status

# 新しいファイルを追加
git add .github/workflows/claude.yml

# コミット
git commit -m "feat: Add Claude Code Action workflow"

# GitHubにプッシュ
git push origin main
```

#### 5-2. コミットメッセージの規則

**Conventional Commits形式**:

```
<type>: <description>

例:
feat: Add Claude Code Action workflow
fix: Add checkout step to workflow
docs: Add setup guide
chore: Update dependencies
```

**Type一覧**:
- `feat`: 新機能追加
- `fix`: バグ修正
- `docs`: ドキュメント
- `test`: テスト
- `refactor`: リファクタリング
- `chore`: その他の変更

---

### ステップ6: 動作確認

#### 6-1. Issueの作成

1. GitHubでリポジトリにアクセス
2. **Issues** タブをクリック
3. **New issue** をクリック
4. 入力：
   - **Title**: `Test Claude Code Action`
   - **Comment**: `Hello!`
5. **Submit new issue** をクリック

#### 6-2. Claudeの呼び出し

Issue内のコメント欄に以下を入力：
```
@claude こんにちは！あなたは誰ですか？
```

#### 6-3. 確認

数秒～数十秒後：
1. **github-actions bot** が自動でコメント
2. **「Claude finished @username's task」** と表示
3. Claudeの応答が表示される

**期待される応答例**:
```
こんにちは！私はClaudeです。Anthropic社によって開発されたAIアシスタントで、
GitHub上でコードレビューや実装のサポートを行うことができます。

Claude Code Actionを通じて、以下のようなお手伝いができます：
• コードの質問への回答
• コードレビューとフィードバック
• コードの変更や実装
• プルリクエストの作成
```

#### 6-4. GitHub Actionsの確認

実行ログの確認：
```
https://github.com/rm007080/note_Obsidian/actions
```

表示内容：
```
Workflows
  Claude Code Action  ✅

Recent workflow runs
  Test Claude Code Action #1  ✅ Success
```

---

## 発生したエラーと解決策

### エラー1: `/install-github-app` コマンドの失敗

#### 症状

```
Install GitHub App

Error: Failed to access repository

Reason: GitHub Actions setup failed
```

#### 原因

Cursorの `/install-github-app` コマンドは自動化ツールだが、以下の理由で失敗：
1. 認証情報（OAuth Token）が未設定
2. GitHubアカウントとの連携が必要
3. ネットワークやアクセス権限の問題

#### 解決策

**手動セットアップを実施**（本ガイドの手順）

自動コマンドに依存せず、確実に各ステップを実行する。

---

### エラー2: `@anthropic-ai/claude-cli` パッケージが見つからない

#### 症状

```bash
npm error 404 Not Found - GET https://registry.npmjs.org/@anthropic-ai%2fclaude-cli
npm error 404  '@anthropic-ai/claude-cli@*' is not in this registry.
```

#### 原因

パッケージ名が間違っている。正しいパッケージ名は `claude`。

#### 解決策

**正しいコマンド**:
```bash
npx claude setup-token
```

**間違い**:
```bash
npx @anthropic-ai/claude-cli setup-token  # ❌
```

---

### エラー3: `fatal: not a git repository`

#### 症状

GitHub Actionsのログ：
```
fatal: not a git repository (or any of the parent directories): .git

Error in branch setup:
ShellError: Failed with exit code 128
  exitCode: 128,
  stdout: "",
  stderr: "fatal: not a git repository (or any of the parent directories): .git\n"
```

#### 原因

ワークフローファイルに **`actions/checkout@v4`** ステップが欠けている。

GitHub Actionsはデフォルトでリポジトリをチェックアウトしないため、Claude Code Actionがコードにアクセスできない。

#### 解決策

**ワークフローファイルに以下を追加**:

```yaml
steps:
  # この行を追加！
  - name: Checkout repository
    uses: actions/checkout@v4
  
  - name: Run Claude Code Action
    uses: anthropics/claude-code-action@v1
    # ...
```

**修正後の動作**:
1. ✅ `actions/checkout@v4` がリポジトリをクローン
2. ✅ `.git` ディレクトリが作成される
3. ✅ Claude Code Actionが正常に動作

---

## 重要な学習ポイント

### 1. GitHub Actionsの基本

#### ワークフローの構造

```yaml
name: ワークフロー名

on:                    # トリガー（いつ実行するか）
  issue_comment:
    types: [created]

permissions:           # 権限設定
  contents: write

jobs:                  # ジョブ定義
  job-name:
    runs-on: ubuntu-latest
    steps:             # ステップ（実行内容）
      - name: Step 1
        uses: action@v1
```

#### トリガーの種類

```yaml
on:
  push:                          # プッシュ時
  pull_request:                  # PR作成時
  issue_comment:                 # Issueコメント時
  schedule:                      # 定期実行
    - cron: '0 0 * * *'
  workflow_dispatch:             # 手動実行
```

---

### 2. GitHub Secretsの管理

#### Secretsの特徴

```yaml
# ワークフローでの使用
env:
  TOKEN: ${{ secrets.MY_SECRET }}  # Secretを環境変数に設定
```

**セキュリティ機能**:
1. 暗号化保存
2. ログの自動マスキング
3. 読み取り専用（保存後は見られない）

#### Organization vs Repository Secrets

| レベル | スコープ | 用途 |
|--------|---------|------|
| **Organization** | 組織内全リポジトリ | 共通の認証情報 |
| **Repository** | 単一リポジトリ | リポジトリ固有の設定 |

**個人アカウントの制限**:
- Organization Secretsは使用不可
- 各リポジトリで個別に設定が必要

---

### 3. OAuth Token vs API Key

#### 比較表

| 項目 | OAuth Token | API Key |
|------|------------|---------|
| **対象** | Pro/Maxユーザー | 全ユーザー |
| **料金** | 無料（サブスク含む） | 従量課金 |
| **有効期限** | 1年間 | 無期限（手動で無効化まで） |
| **セキュリティ** | 短期トークン（相対的） | 長期キー |
| **生成方法** | `npx claude setup-token` | Anthropic Console |
| **Secret名** | `CLAUDE_CODE_OAUTH_TOKEN` | `ANTHROPIC_API_KEY` |

#### 推奨

**Pro/Maxユーザー**: OAuth Token  
**その他**: API Key

---

### 4. PowerShell vs WSL

#### 環境の使い分け

| 作業 | PowerShell | WSL | 推奨 |
|------|-----------|-----|------|
| **ディレクトリ作成** | ✅ | ✅ | PowerShell（パス変換不要） |
| **OAuth Token生成** | ✅ | ✅ | どちらでも可 |
| **Git操作** | ✅ | ✅ | PowerShell（日本語パス対応） |
| **Linux特有コマンド** | ❌ | ✅ | WSL |

#### パスの違い

```powershell
# PowerShell
G:\マイドライブ\01_Obsidian\11_note\note_Obsidian

# WSL
/mnt/g/マイドライブ/01_Obsidian/11_note/note_Obsidian
```

---

### 5. Git操作の基本

#### Conventional Commits

```bash
# 形式
<type>(<scope>): <subject>

# 例
feat(auth): Add OAuth token support
fix(workflow): Add checkout step
docs(readme): Update setup instructions
```

#### Git操作フロー

```bash
# 1. 状態確認
git status

# 2. ステージング
git add <file>
git add .              # 全ファイル

# 3. コミット
git commit -m "message"

# 4. プッシュ
git push origin main

# 5. 履歴確認
git log --oneline
```

---

## 今後のリポジトリでの作業手順

### 所要時間：約2-3分

### チェックリスト

#### 事前準備（初回のみ）

- [x] OAuth Tokenを安全な場所に保存済み
- [x] `claude.yml` をテンプレートとして保存済み
- [x] Claude GitHub Appをインストール済み

#### 新しいリポジトリでの作業

##### Step 1: Claude GitHub Appにリポジトリを追加（1分）

1. https://github.com/settings/installations にアクセス
2. **Claude** をクリック
3. **Configure** をクリック
4. **Repository access** でリポジトリを追加
5. **Save** をクリック

##### Step 2: Secretsの追加（1分）

1. 新しいリポジトリの Settings → Secrets and variables → Actions
2. **New repository secret** をクリック
3. 入力：
   - Name: `CLAUDE_CODE_OAUTH_TOKEN`
   - Secret: 保存済みのOAuth Token
4. **Add secret** をクリック

##### Step 3: ワークフローファイルをコピー（1分）

**方法1: 手動コピー**

1. このリポジトリから `.github/workflows/claude.yml` をコピー
2. 新しいリポジトリに同じ構造で貼り付け

**方法2: コマンド**

```bash
# 元のリポジトリから
cp .github/workflows/claude.yml <新しいリポジトリのパス>/.github/workflows/

# 新しいリポジトリで
git add .github/workflows/claude.yml
git commit -m "feat: Add Claude Code Action workflow"
git push origin main
```

##### Step 4: 動作確認（1分）

1. Issueを作成
2. コメント: `@claude こんにちは`
3. Claudeの応答を確認

---

### テンプレートリポジトリの活用（推奨）

#### このリポジトリをテンプレート化

1. GitHubでこのリポジトリにアクセス
2. **Settings** → **Template repository** にチェック
3. Save

#### 新しいリポジトリの作成

1. GitHubのトップページ
2. **New repository**
3. **Repository template** で `note_Obsidian` を選択
4. リポジトリ名を入力
5. **Create repository**

**メリット**:
- `.github/workflows/claude.yml` が自動的に含まれる
- セットアップガイドも含まれる
- Secretsの追加のみで完了（約1分）

---

## トラブルシューティング

### 問題1: Claudeが応答しない

#### 確認項目

1. **GitHub Actionsが実行されているか**
   ```
   https://github.com/USERNAME/REPO/actions
   ```
   
2. **ワークフローのログを確認**
   - 左サイドバーで失敗したジョブをクリック
   - エラーメッセージを確認

3. **トリガー条件を満たしているか**
   - コメントに `@claude` が含まれているか
   - Issue/PRのコメントか（通常のコメント欄では動作しない）

#### よくあるエラー

**エラー**: `CLAUDE_CODE_OAUTH_TOKEN not found`

**原因**: Secretが設定されていない、または名前が間違っている

**解決**:
```
Settings → Secrets and variables → Actions
→ CLAUDE_CODE_OAUTH_TOKEN が存在するか確認
→ 名前のスペルミスがないか確認
```

---

### 問題2: OAuth Tokenが期限切れ

#### 症状

```
Error: Invalid or expired token
```

#### 解決策

1. 新しいOAuth Tokenを生成
   ```bash
   npx claude setup-token
   ```

2. GitHubのSecretを更新
   - Settings → Secrets and variables → Actions
   - `CLAUDE_CODE_OAUTH_TOKEN` の **Update** をクリック
   - 新しいトークンを貼り付け

---

### 問題3: Permission denied

#### 症状

```
Error: Permission denied to access repository
```

#### 原因

1. Claude GitHub Appがインストールされていない
2. リポジトリへのアクセス権限がない

#### 解決策

**Claude GitHub Appの確認**:
```
https://github.com/settings/installations
```

- Claude が表示されているか
- 該当リポジトリにアクセス権限があるか

**権限の追加**:
1. Claude の **Configure** をクリック
2. **Repository access** でリポジトリを選択
3. **Save**

---

### 問題4: ワークフローが実行されない

#### 確認項目

1. **`.github/workflows/claude.yml` が存在するか**
   ```bash
   ls .github/workflows/
   ```

2. **YAMLの文法が正しいか**
   - https://yaml-online-parser.appspot.com/ でチェック
   - インデントは半角スペース2つ

3. **mainブランチにプッシュされているか**
   ```bash
   git log --oneline
   git push origin main
   ```

---

### 問題5: WSLでパスが認識されない

#### 症状

```
wsl: Failed to translate 'G:\マイドライブ...'
```

#### 原因

WSLは日本語を含むWindowsパスを直接認識できない。

#### 解決策

**方法1: PowerShellを使用（推奨）**
```powershell
cd "G:\マイドライブ\01_Obsidian\11_note\note_Obsidian"
git status
```

**方法2: WSLでパスを変換**
```bash
cd /mnt/g/マイドライブ/01_Obsidian/11_note/note_Obsidian
```

**方法3: WSL内でホームディレクトリから作業**
```bash
# リポジトリをWSLのホームにクローン
cd ~
git clone https://github.com/rm007080/note_Obsidian.git
cd note_Obsidian
```

---

## 参考情報

### 公式ドキュメント

- [Claude Code Action GitHub](https://github.com/anthropics/claude-code-action)
- [GitHub Actions ドキュメント](https://docs.github.com/en/actions)
- [GitHub Secrets ドキュメント](https://docs.github.com/en/actions/security-guides/encrypted-secrets)
- [Anthropic API ドキュメント](https://docs.anthropic.com/)

### 関連リンク

- [Claude GitHub App](https://github.com/apps/claude)
- [Anthropic Console](https://console.anthropic.com/)
- [GitHub Apps管理](https://github.com/settings/installations)

### このリポジトリ内の関連ファイル

```
note_Obsidian/
├── .github/
│   └── workflows/
│       └── claude.yml              # ワークフローファイル
├── Claude_Code_Action_セットアップ完全ガイド.md  # このファイル
├── SETUP_CLAUDE.md                 # クイックセットアップガイド
└── CLAUDE_TEMPLATE.txt             # テンプレート（OAuth Token含む）
```

---

## まとめ

### 今回の成果

✅ Claude Code Actionの完全セットアップ完了  
✅ 動作確認成功  
✅ エラー解決とトラブルシューティング経験  
✅ 再利用可能なテンプレート作成  
✅ 網羅的なドキュメント整備

### 次回以降の作業時間

- **初回**: 30-60分（今回）
- **2回目以降**: 2-3分

### 重要な概念の理解

1. **GitHub Actions**: ワークフロー、ジョブ、ステップ
2. **GitHub Secrets**: 機密情報の安全な管理
3. **OAuth認証**: トークンベースの認証
4. **環境の使い分け**: PowerShell vs WSL
5. **Git操作**: Conventional Commits

### 今後の活用

- ✅ Issueでの質問
- ✅ Pull Requestのレビュー
- ✅ コード実装の依頼
- ✅ 誤字脱字の修正
- ✅ ドキュメント生成

---

**作成者**: rm007080  
**最終更新**: 2025年11月3日  
**バージョン**: 1.0.0

