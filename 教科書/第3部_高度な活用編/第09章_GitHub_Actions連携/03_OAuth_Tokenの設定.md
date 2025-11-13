# OAuth Tokenの設定

## はじめに

この章では、OAuth Tokenの生成とGitHub Secretsへの追加を行います。これにより、Claude AIがあなたのサブスクリプションを使用してGitHub上で動作できるようになります。

## OAuth Tokenとは

### 概要

**OAuth Token**は、Claude Pro/Maxプランのユーザーが、追加料金なしでClaude AIをGitHub Actionsで使用するための認証トークンです。

### 特徴

| 項目 | 内容 |
|------|------|
| **有効期限** | 1年間 |
| **対象** | Claude Pro/Maxユーザー |
| **料金** | サブスクリプションに含まれる（追加料金なし） |
| **再利用** | 複数のリポジトリで使用可能 |
| **セキュリティ** | 一度しか表示されない |

### API Keyとの違い

| 項目 | OAuth Token | API Key |
|------|------------|---------|
| **対象ユーザー** | Pro/Maxのみ | 全ユーザー |
| **料金** | 無料（サブスク含む） | 従量課金 |
| **有効期限** | 1年間 | 無期限 |
| **生成方法** | `npx claude setup-token` | Anthropic Console |
| **Secret名** | `CLAUDE_CODE_OAUTH_TOKEN` | `ANTHROPIC_API_KEY` |

**推奨:** Pro/Maxユーザーは OAuth Token を使用

## 環境の選択

### PowerShell（推奨）

**Windows環境で最も簡単:**
- ✅ 日本語パスの問題なし
- ✅ シンプル
- ✅ Cursorのワークスペースと同じ環境

### WSL（可能だが複雑）

**注意点:**
- ⚠️ パス変換が必要（`G:\` → `/mnt/g/`）
- ⚠️ 日本語パスで問題が起きる可能性
- ⚠️ 余計な手順が増える

**推奨:** 特別な理由がなければPowerShellを使用

### macOS/Linux

**デフォルトのターミナルで実行:**
- ✅ 特別な設定不要
- ✅ そのまま実行可能

## OAuth Tokenの生成手順

### ステップ1: ターミナルを開く

**Windows:**
- PowerShellを開く（推奨）
- または、Cursorの統合ターミナル

**macOS/Linux:**
- ターミナルを開く

### ステップ2: トークン生成コマンドの実行

以下のコマンドを実行します。

```bash
npx claude setup-token
```

**注意:** パッケージ名は `claude` です。`@anthropic-ai/claude-cli` ではありません。

### ステップ3: ブラウザでの認証

コマンドを実行すると、ブラウザが自動的に開きます。

**認証フロー:**
```
1. コマンド実行
    ↓
2. ブラウザが自動的に開く
    ↓
3. Claude.aiにログイン（既にログイン済みの場合はスキップ）
    ↓
4. 「Authorize」ボタンをクリック
    ↓
5. ターミナルに戻るとトークンが表示される
```

### ステップ4: トークンの確認

ターミナルに以下のようなメッセージとトークンが表示されます。

```
✓ Long-lived authentication token created successfully!

Your OAuth token (valid for 1 year):

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

Store this token securely. You won't be able to see it again.

Use this token by setting: export CLAUDE_CODE_OAUTH_TOKEN=<token>
```

**重要なポイント:**
- ✅ **1年間有効**
- ⚠️ **一度しか表示されない**（必ず保存）
- ✅ **複数リポジトリで使い回せる**

### ステップ5: トークンをコピー

表示されたトークン全体を慎重にコピーします。

```
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
```

**コピー方法:**
1. トークン部分を選択
2. 右クリック → コピー
3. または、Ctrl+C（Cmd+C）

## トークンの安全な保存

### 保存場所の推奨

トークンは以下のいずれかに保存してください。

#### 方法1: パスワードマネージャー（最も推奨）

**推奨ツール:**
- 1Password
- Bitwarden
- LastPass
- Dashlane

**保存例（1Password）:**
```
タイトル: Claude OAuth Token
種類: Secure Note
内容:
  Token: sk-ant-oat01-bgQs8n35...
  有効期限: 2026年11月3日
  用途: GitHub Actions連携
```

#### 方法2: 暗号化されたテキストファイル

**macOS/Linux:**
```bash
# 暗号化されたファイルに保存
echo "CLAUDE_OAUTH_TOKEN=sk-ant-oat01-..." > ~/.claude_token
chmod 600 ~/.claude_token  # 自分だけが読める
```

**Windows:**
```powershell
# ファイルに保存（暗号化はWindows機能を使用）
Set-Content -Path "$env:USERPROFILE\.claude_token" -Value "CLAUDE_OAUTH_TOKEN=sk-ant-oat01-..."
```

#### 方法3: メモアプリ（非推奨）

**セキュリティリスクがあるため非推奨:**
- メモアプリは暗号化されていない場合が多い
- 同期によりクラウドに保存される可能性
- 他のアプリからアクセスされる可能性

### セキュリティの原則

**❌ 絶対にやってはいけないこと:**
- コードにコミット
- チャットやメールで送信
- スクリーンショットに含める
- 暗号化されていないファイルに保存
- 公開されたドキュメントに記載

**✅ すべきこと:**
- 安全な場所に保存
- 定期的に確認
- 不要になったら無効化
- アクセスログを監視

## GitHub Secretsへの追加

### ステップ1: GitHubリポジトリのSecretsページにアクセス

**方法1: 直接URL（最も簡単）**

URLを以下の形式で開きます。

```
https://github.com/[ユーザー名]/[リポジトリ名]/settings/secrets/actions
```

**例:**
```
https://github.com/johndoe/my-project/settings/secrets/actions
```

**方法2: 手動ナビゲーション**

1. GitHubでリポジトリにアクセス
2. **Settings**タブをクリック
3. 左サイドバー → **Secrets and variables** → **Actions**

### ステップ2: Secretsとは

**Secret（シークレット）の特徴:**

| 特徴 | 説明 |
|------|------|
| **暗号化保存** | 保存時に暗号化される |
| **読み取り不可** | 保存後は中身を見られない |
| **自動マスク** | ログに出力されても `***` で表示 |
| **アクセス制限** | GitHub Actionsからのみアクセス可能 |

**Variablesとの違い:**

| 項目 | Secrets 🔒 | Variables 📝 |
|------|-----------|-------------|
| **用途** | 機密情報 | 非機密の設定値 |
| **暗号化** | ✅ される | ❌ されない |
| **表示** | ❌ 見られない | ✅ いつでも見られる |
| **ログ** | 自動マスク | そのまま表示 |
| **例** | APIキー、パスワード | 環境名、バージョン |

### ステップ3: Secretの追加

1. **「New repository secret」**ボタンをクリック

2. 入力内容：

```
Name:   CLAUDE_CODE_OAUTH_TOKEN
Secret: sk-ant-oat01-bgQs8n35IxagaBiiiLwThz9pUrZjKJ7WJKh9B3MzQlfcc85hG-...
```

**Name（名前）の注意点:**
- ✅ 正確に `CLAUDE_CODE_OAUTH_TOKEN` と入力（大文字・小文字を区別）
- ❌ スペルミスに注意
- ❌ `GITHUB_` で始まる名前は使用不可（予約語）

**Secret（値）の注意点:**
- ✅ トークン全体をコピー＆ペースト
- ❌ 前後にスペースを入れない
- ❌ 改行を含めない

3. **「Add secret」**ボタンをクリック

### ステップ4: 確認

保存後、以下のように表示されます。

```
Repository secrets:
  CLAUDE_CODE_OAUTH_TOKEN    Updated now    [Update] [Remove]
```

**成功です！** Secretが正しく保存されました。

## 複数リポジトリでの使用

### 同じトークンを複数リポジトリで使う

1つのOAuth Tokenを、複数のリポジトリで使い回せます。

**手順:**
1. 各リポジトリのSecretsページにアクセス
2. 同じトークンを `CLAUDE_CODE_OAUTH_TOKEN` として追加
3. 繰り返し

**推奨:** トークンをパスワードマネージャーに保存しておけば、簡単にコピペできます。

### Organization Secretsの活用（組織アカウント）

組織アカウントの場合、Organization Secretsを使うと便利です。

**メリット:**
- ✅ 1回の設定で全リポジトリで使用可能
- ✅ トークンの更新も1カ所で済む

**設定場所:**
```
https://github.com/organizations/[組織名]/settings/secrets/actions
```

**注意:** 個人アカウントではOrganization Secretsは使用できません。

## トークンの更新

### 有効期限が切れる前に

OAuth Tokenは1年間有効です。期限が近づいたら、新しいトークンを生成します。

**更新手順:**

1. **新しいトークンを生成**
   ```bash
   npx claude setup-token
   ```

2. **GitHubのSecretを更新**
   - Secretsページにアクセス
   - `CLAUDE_CODE_OAUTH_TOKEN` の **「Update」**をクリック
   - 新しいトークンを入力
   - **「Update secret」**をクリック

3. **古いトークンを削除**（オプション）
   - セキュリティのため、古いトークンはパスワードマネージャーから削除

## トラブルシューティング

### 問題: `npm error 404 Not Found`

**症状:**
```bash
npm error 404 Not Found - GET https://registry.npmjs.org/@anthropic-ai%2fclaude-cli
```

**原因:** パッケージ名が間違っている

**解決策:**
```bash
# 正しいコマンド
npx claude setup-token

# 間違い（これは使わない）
npx @anthropic-ai/claude-cli setup-token
```

### 問題: ブラウザが開かない

**原因:** デフォルトブラウザの設定問題

**解決策:**
1. ターミナルに表示されるURLを手動でコピー
2. ブラウザに貼り付けてアクセス
3. 認証を完了

### 問題: トークンが表示されない

**原因:** 認証が完了していない

**解決策:**
1. ブラウザで「Authorize」をクリック
2. ターミナルに戻る
3. しばらく待つ（数秒〜数十秒）

### 問題: GitHub Secretが保存できない

**原因:** リポジトリの管理者権限がない

**解決策:**
- リポジトリの管理者に権限付与を依頼

## まとめ

### 完了したこと

- [x] OAuth Tokenの生成
- [x] トークンの安全な保存
- [x] GitHub Secretsへの追加
- [x] 複数リポジトリでの使用方法の理解

### 重要なポイント

1. **1年間有効**: 定期的な更新が必要
2. **再利用可能**: 複数リポジトリで使える
3. **セキュリティ**: 安全な場所に保存
4. **Secret名**: `CLAUDE_CODE_OAUTH_TOKEN` を正確に入力

### 次章の内容

次の章では、ワークフローファイル（`.github/workflows/claude.yml`）の作成を行います。これにより、GitHub上で `@claude` メンションが動作するようになります。
