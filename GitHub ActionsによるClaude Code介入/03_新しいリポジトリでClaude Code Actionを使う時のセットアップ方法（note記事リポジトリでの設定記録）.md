---
created: 2025-11-04
tags:
aliases: 
related: 
---
素晴らしい質問です！効率化の観点から重要なポイントですね。

## 結論：リポジトリごとに設定が必要ですが、簡略化できます

---

## 設定の種類と再利用性

### 1回だけ設定すればOKなもの ✅

| 項目 | 設定場所 | 再利用性 |
|------|---------|---------|
| **OAuth Token生成** | ローカル環境 | ✅ 1年間有効（全リポジトリで使い回せる） |
| **Claude GitHub App** | GitHub Apps | ✅ 一度インストールすれば複数リポジトリに適用可能 |

### リポジトリごとに必要なもの ⚠️

| 項目 | 設定場所 | 理由 |
|------|---------|------|
| **Secrets設定** | 各リポジトリ | リポジトリごとに独立 |
| **claude.yml** | 各リポジトリの`.github/workflows/` | リポジトリごとに必要 |

---

## 効率化の方法

### 方法1: Organization Secrets（組織アカウント向け）

**組織アカウントを持っている場合**、Organization レベルで Secret を設定できます。

#### メリット：
- ✅ 1回の設定で全リポジトリで使える
- ✅ トークンの更新も1カ所で済む

#### 設定方法：
```
https://github.com/organizations/YOUR_ORG/settings/secrets/actions
```

**注意：** 個人アカウント（`rm007080`）ではこの機能は使えません。

---

### 方法2: テンプレートリポジトリの作成（推奨）

**1回の手間で、今後は数クリックで完了します。**

#### ステップ1: テンプレートリポジトリを作成

```
# Claude Code Action セットアップガイド

このリポジトリでClaude Code Actionを有効にする手順。

## 前提条件

- ✅ Claude Pro/Maxプランに加入済み
- ✅ OAuth Token を生成済み（1年間有効）
- ✅ Claude GitHub App をインストール済み

## セットアップ手順（所要時間：2分）

### 1. GitHub Secretの追加

1. リポジトリの Settings → Secrets and variables → Actions にアクセス
   ```
   https://github.com/YOUR_USERNAME/YOUR_REPO/settings/secrets/actions
   ```

2. 「New repository secret」をクリック

3. 以下を入力：
   - **Name**: `CLAUDE_CODE_OAUTH_TOKEN`
   - **Secret**: 生成済みのOAuth Token（`sk-ant-oat01-...`）

4. 「Add secret」をクリック

### 2. Claude GitHub Appのインストール（初回のみ）

1. https://github.com/apps/claude にアクセス

2. 「Configure」をクリック

3. このリポジトリを選択して「Save」

### 3. ワークフローファイルの作成（自動）

このリポジトリには既に `.github/workflows/claude.yml` が含まれています。

### 4. 動作確認

1. Issueを作成
2. コメントで `@claude こんにちは！` と入力
3. Claudeが自動で返信

## トラブルシューティング

### エラー: `fatal: not a git repository`
- ワークフローに `actions/checkout@v4` が含まれているか確認

### エラー: `CLAUDE_CODE_OAUTH_TOKEN not found`
- Secretが正しく設定されているか確認
- 名前のスペルミスがないか確認

### エラー: `Permission denied`
- Claude GitHub Appがインストールされているか確認
- リポジトリへのアクセス権限があるか確認

## 参考リンク

- [Claude Code Action 公式ドキュメント](https://github.com/anthropics/claude-code-action)
- [GitHub Actions ドキュメント](https://docs.github.com/en/actions)


```

#### ステップ2: GitHubでテンプレート化

1. GitHubの `note_Obsidian` リポジトリにアクセス
2. Settings → Template repository にチェック
3. これで新規リポジトリ作成時に「Use this template」が使える

![[Pasted image 20251104062133.png]]

---

### 方法3: GitHub CLIでスクリプト化（上級者向け）

```bash
#!/bin/bash
# setup-claude.sh - 新しいリポジトリにClaude Code Actionをセットアップ

REPO_NAME=$1
TOKEN="your-oauth-token-here"

# Secretを追加
gh secret set CLAUDE_CODE_OAUTH_TOKEN -b"$TOKEN" -R "$REPO_NAME"

# ワークフローファイルをコピー
cp .github/workflows/claude.yml "$REPO_NAME/.github/workflows/"

echo "✅ Setup complete for $REPO_NAME"
```

---

### 方法4: ワークフローファイルのコピー＆ペースト（最も簡単）

#### 今回作成したファイルを保存：

![[Pasted image 20251104062255.png]]

```
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

このファイルを**テンプレートとして保存**しておき、新しいリポジトリにコピー＆ペーストするだけ。

---

## 実際の作業フロー比較

### 初回（今回）：
```
⏱️ 約30-60分
1. OAuth Token生成（5分）
2. Claude GitHub Appインストール（2分）
3. Secretsの設定（3分）
4. claude.ymlの作成（5分）
5. トラブルシューティング（15-45分）
```

### 2回目以降（推奨手順）：
```
⏱️ 約2-5分
1. Secretsの設定（2分）← 必須
2. claude.ymlをコピー（1分）← テンプレートから
3. Claude GitHub App設定（1分）← 既存のアプリに追加するだけ
```

---

## 最も効率的な運用方法

### 📋 チェックリスト（2回目以降）

#### 事前準備（1回だけ）
- [ ] OAuth Tokenを安全な場所に保存（パスワードマネージャー等）
- [ ] `claude.yml` をテンプレートとして保存
- [ ] セットアップガイドを保存

#### 新しいリポジトリでの作業（毎回）
- [ ] Claude GitHub Appにリポジトリを追加（1分）
- [ ] Secretに `CLAUDE_CODE_OAUTH_TOKEN` を追加（1分）
- [ ] `claude.yml` をコピー（1分）
- [ ] 動作確認（1分）

---

## まとめ

| 項目 | 初回 | 2回目以降 |
|------|------|----------|
| **OAuth Token生成** | 必要 | 不要（1年間有効） |
| **Claude GitHub App** | インストール | リポジトリ追加のみ |
| **Secrets設定** | 必要 | 必要（コピペ） |
| **claude.yml** | 作成 | コピー＆ペースト |
| **所要時間** | 30-60分 | 2-5分 |

---

## 最終まとめ

### 質問への答え：

**「毎回手動で行う必要がありますか？」**

→ **一部は必要ですが、大幅に簡略化できます。**

### 今後の作業（2回目以降）：

| 作業 | 頻度 | 所要時間 |
|------|------|---------|
| OAuth Token生成 | ❌ 不要（1年ごと） | - |
| Claude GitHub App | 🔄 リポジトリ追加のみ | 1分 |
| Secrets設定 | ✅ 必要（コピペ） | 1分 |
| claude.yml | ✅ 必要（コピペ） | 1分 |
| **合計** | - | **約3分** |

### 今回保存したもの：

1. ✅ `SETUP_CLAUDE.md` - 詳細なセットアップガイド
2. ✅ `CLAUDE_TEMPLATE.txt` - クイックリファレンス（OAuth Token含む）
3. ✅ `.github/workflows/claude.yml` - 動作確認済みワークフロー

---

**次回から新しいリポジトリでClaude Code Actionを使う時は、3分でセットアップ完了です！** 🎉

何か他に質問があれば、遠慮なく聞いてください！