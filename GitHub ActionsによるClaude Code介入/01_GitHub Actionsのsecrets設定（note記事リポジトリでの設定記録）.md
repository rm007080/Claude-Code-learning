---
created: 2025-11-04
tags:
aliases: 
related: 
---
GitHubの「secrets」
![[Pasted image 20251104052812.png]]

完璧です！正しいページにアクセスできています。画面を解説します。

## 画面の解説

### 「Actions secrets and variables」ページ

現在表示されているのは、**GitHub Actionsで使用する機密情報を安全に保管する場所**です。

---

## 「Secrets」とは何か？

**Secret（シークレット）** = **機密情報を安全に保存する仕組み**

### 例えば：
- 🔑 API Key（AnthropicのAPIキーなど）
- 🔐 OAuth Token（今回取得したトークン）
- 🔒 パスワード
- 🗝️ アクセストークン

### なぜSecretsを使うのか？

❌ **悪い例：コードに直接書く**
```yaml
# 危険！誰でも見られる
api_key: "sk-ant-oat01-bgQs8n35..."
```

✅ **良い例：Secretsを使う**
```yaml
# 安全！暗号化されて保存される
api_key: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}
```

### Secretsの特徴：
- 🔒 **暗号化されて保存**される
- 👁️ 一度保存すると**中身は見られない**（編集や削除のみ可能）
- 🚫 ログに出力されても**自動的にマスク**される（`***`と表示）
- ✅ GitHub Actionsのワークフローからのみアクセス可能

---

## 画面の説明

### 1. **Environment secrets（環境シークレット）**
```
This environment has no secrets.
```
- 特定の環境（本番環境、開発環境など）専用のシークレット
- 今回は使用しません

### 2. **Repository secrets（リポジトリシークレット）** ← **ここを使います！**
```
This repository has no secrets.
[New repository secret] ← この緑のボタンをクリック
```
- **このリポジトリ全体**で使えるシークレット
- 今回のOAuth Tokenはここに保存します

---

## 次の作業

### 「New repository secret」ボタンをクリックしてください

すると、以下の入力フォームが表示されます：

```
Name: [入力欄]
Secret: [入力欄]
[Add secret]ボタン
```

### 入力内容：

1. **Name（名前）**：
   ```
   CLAUDE_CODE_OAUTH_TOKEN
   ```
   - 大文字・小文字を正確に入力してください
   - この名前でワークフローから参照します

2. **Secret（値）**：
   ```
   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
   ```
   - WSLで生成されたトークンを貼り付け

3. **「Add secret」をクリック**

---

## セキュリティの仕組み

保存後、以下のようになります：

```
Repository secrets:
  CLAUDE_CODE_OAUTH_TOKEN    [Update] [Remove]
```

- ✅ 値は**暗号化されて保存**される
- ✅ 中身は**二度と表示されない**
- ✅ GitHub Actionsのワークフロー内でのみ `${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}` として使用可能

---

**「New repository secret」ボタンをクリックして、入力フォームが表示されたら教えてください！**

---
素晴らしい質問です！この2つの違いを説明します。

## Secrets vs Variables の違い

### 📊 比較表

| 項目 | **Secrets** 🔒 | **Variables** 📝 |
|------|---------------|-----------------|
| **用途** | 機密情報（パスワード、トークン） | 非機密の設定値 |
| **暗号化** | ✅ 暗号化される | ❌ 暗号化されない |
| **表示** | ❌ 保存後は見られない | ✅ いつでも見られる |
| **ログ** | 自動的にマスク（`***`） | そのまま表示される |
| **編集** | 上書きのみ（中身は見られない） | 自由に編集可能 |

---

## Secrets（シークレット）🔒

### 使うべきもの：
- 🔑 **API Key**（例：`sk-ant-...`）
- 🔐 **OAuth Token**（今回のトークン）
- 🔒 **パスワード**
- 🗝️ **アクセストークン**
- 💳 **クレジットカード情報**

### 特徴：
```yaml
# ワークフローでの使用例
- name: Run Claude
  env:
    TOKEN: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}  # 暗号化されている
```

**ログ出力例：**
```
TOKEN: ***  # 自動的にマスクされる
```

---

## Variables（変数）📝

### 使うべきもの：
- 🌐 **環境名**（例：`production`, `staging`）
- 📦 **バージョン番号**（例：`v1.0.0`）
- 🔗 **公開URL**（例：`https://example.com`）
- ⚙️ **設定値**（例：`timeout: 30`）
- 📁 **ディレクトリパス**（例：`/app/data`）

### 特徴：
```yaml
# ワークフローでの使用例
- name: Deploy
  env:
    ENVIRONMENT: ${{ vars.DEPLOY_ENV }}  # 平文で保存
    VERSION: ${{ vars.APP_VERSION }}
```

**ログ出力例：**
```
ENVIRONMENT: production  # そのまま表示される
VERSION: v1.0.0
```

---

## 実例で理解する

### ❌ 間違った使い方

```yaml
# 危険！APIキーをVariablesに保存
API_KEY: ${{ vars.API_KEY }}  # 誰でも見られる！
```

```yaml
# 無駄！環境名をSecretsに保存
ENV: ${{ secrets.ENVIRONMENT }}  # 暗号化する必要がない
```

### ✅ 正しい使い方

```yaml
# 正しい！機密情報はSecrets
- name: Call API
  env:
    API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}  # 機密情報
    OAUTH_TOKEN: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}  # 機密情報

# 正しい！設定値はVariables
- name: Deploy
  env:
    ENVIRONMENT: ${{ vars.DEPLOY_ENV }}  # 非機密（production, staging等）
    REGION: ${{ vars.AWS_REGION }}  # 非機密（us-east-1等）
```

---

## 今回の場合

### 今回使うのは：**Secrets** 🔒

**理由：**
- OAuth Token は**機密情報**
- 他人に知られると、あなたのアカウントで勝手にAPIを使われる
- 必ず**Secrets**タブから保存

---

## 使い分けの判断基準

### 🤔 判断フローチャート

```
この情報は他人に知られても大丈夫？
  ↓
NO → Secrets を使う 🔒
YES → Variables を使う 📝
```

### 具体例：

| 情報 | 判断 | 使用 |
|------|------|------|
| APIキー `sk-ant-...` | ❌ 知られたらダメ | **Secrets** |
| OAuth Token | ❌ 知られたらダメ | **Secrets** |
| デプロイ先 `production` | ✅ 公開OK | **Variables** |
| アプリバージョン `v1.0.0` | ✅ 公開OK | **Variables** |
| データベースパスワード | ❌ 知られたらダメ | **Secrets** |
| ポート番号 `8080` | ✅ 公開OK | **Variables** |

---

## まとめ

今回の作業では：

✅ **Secretsタブを使用**
- Name: `CLAUDE_CODE_OAUTH_TOKEN`
- Secret: `sk-ant-oat01-...`（WSLで生成したトークン）

❌ Variablesタブは**今回は使用しない**

---

**理解できましたか？「New repository secret」ボタンをクリックして、次に進みましょう！**

---
完璧です！入力フォームが表示されましたね。次の手順を実行してください。

## 入力手順

### 1. **Name（名前）** を入力

現在 `YOUR_SECRET_NAME` と表示されている入力欄に、以下を**正確に**入力してください：

```
CLAUDE_CODE_OAUTH_TOKEN
```

**注意点：**
- ✅ すべて**大文字**
- ✅ アンダースコア `_` を正確に
- ❌ スペースは入れない

### 2. **Secret（値）** を入力

大きなテキストエリア（現在空欄）に、WSLで生成されたトークンを貼り付けてください：

```
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
```

**トークンの取得方法：**
- WSLのターミナルをスクロールして、前に表示されたトークンをコピー
- または、ターミナルに残っていれば選択してコピー

### 3. **「Add secret」ボタンをクリック**

緑色の「Add secret」ボタンをクリックします。

---

## 入力例（確認用）

```
Name:
┌─────────────────────────────────────────┐
│ CLAUDE_CODE_OAUTH_TOKEN                 │
└─────────────────────────────────────────┘

Secret:
┌─────────────────────────────────────────┐
│ sk-ant-oat01-bgQs8n35IxagaBiiiLwThz... │
│                                         │
│                                         │
└─────────────────────────────────────────┘

[Add secret] ← クリック
```

---

## 完了後の確認

保存が成功すると、以下のような画面に戻ります：

```
Repository secrets:
  CLAUDE_CODE_OAUTH_TOKEN    Updated now    [Update] [Remove]
```

---

## ⚠️ 重要な注意事項

1. **トークンは正確にコピー**
   - 先頭や末尾にスペースが入らないように
   - トークン全体をコピー（`sk-ant-oat01-` から最後まで）

2. **名前のスペルミスに注意**
   - `CLAUDE_CODE_OAUTH_TOKEN` を正確に入力
   - 後でワークフローがこの名前を参照します

3. **保存後は中身が見られない**
   - 一度保存すると、値は二度と表示されません
   - 間違えた場合は「Update」で上書きできます

---

**入力して「Add secret」をクリックしたら、結果を教えてください！**

次のステップ（GitHub Actionsワークフローファイルの作成）に進みます。

---

素晴らしい質問です！その疑問は非常に重要なポイントです。

## 名前の決まりについて

### 結論：**厳密な決まりはありません**が、**推奨される命名規則**があります

---

## なぜ「正確に」と言ったのか

### 理由：後でワークフローファイルで**この名前を参照する**から

```yaml
# GitHub Actionsのワークフローファイル例
- uses: anthropics/claude-code-action@v1
  with:
    github_token: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}
                          ↑
                    ここで使う名前と一致させる必要がある
```

**もし名前が違うと：**
```yaml
# Secretの名前: CLAUDE_CODE_OAUTH_TOKEN
# ワークフローで参照: MY_TOKEN
github_token: ${{ secrets.MY_TOKEN }}  # ❌ 見つからない！エラー
```

---

## 命名規則（一般的なルール）

### GitHub Secretsの命名規則：

| ルール | 説明 | 例 |
|--------|------|-----|
| **大文字推奨** | 環境変数の慣習に従う | `API_KEY` ✅ / `api_key` ❌ |
| **アンダースコア** | 単語の区切り | `OAUTH_TOKEN` ✅ / `OAuthToken` ❌ |
| **わかりやすい名前** | 用途が明確 | `ANTHROPIC_KEY` ✅ / `KEY1` ❌ |
| **予約語を避ける** | `GITHUB_` で始まらない | `MY_TOKEN` ✅ / `GITHUB_TOKEN` ❌ |

### 技術的な制限：

- ✅ 使える文字：`A-Z`、`0-9`、`_`（アンダースコア）
- ❌ 使えない文字：小文字、スペース、ハイフン、特殊文字
- ❌ 予約語：`GITHUB_`で始まる名前（GitHubの内部用）

---

## 名前の選択肢

### オプション1：公式推奨の名前（今回の提案）

```
CLAUDE_CODE_OAUTH_TOKEN
```

**メリット：**
- ✅ 公式ドキュメントと一致
- ✅ 他の人が見てもわかりやすい
- ✅ コピペで動くサンプルコードが多い

### オプション2：自分で決める名前

```
ANTHROPIC_TOKEN
MY_CLAUDE_TOKEN
CLAUDE_AUTH
```

**メリット：**
- ✅ 自分の命名規則に合わせられる
- ✅ 短くてシンプル

**デメリット：**
- ❌ ワークフローファイルで自分で調整が必要

---

## 実例：名前が違う場合

### Secretの登録：
```
名前: MY_CLAUDE_TOKEN
値: sk-ant-oat01-...
```

### ワークフローファイルの調整が必要：
```yaml
# 公式サンプル（動かない）
- uses: anthropics/claude-code-action@v1
  with:
    claude_code_oauth_token: ${{ secrets.CLAUDE_CODE_OAUTH_TOKEN }}  # ❌

# 修正版（自分の名前に合わせる）
- uses: anthropics/claude-code-action@v1
  with:
    claude_code_oauth_token: ${{ secrets.MY_CLAUDE_TOKEN }}  # ✅
```

---

## 推奨される命名パターン

### 目的別の命名例：

```
# API Key の場合
ANTHROPIC_API_KEY
OPENAI_API_KEY
AWS_ACCESS_KEY

# OAuth Token の場合
CLAUDE_CODE_OAUTH_TOKEN  ← 今回これ
GITHUB_OAUTH_TOKEN

# データベース関連
DATABASE_PASSWORD
DB_CONNECTION_STRING

# その他
SLACK_WEBHOOK_URL
DEPLOYMENT_KEY
```

---

## 結論：どうすればいい？

### 推奨：公式の名前をそのまま使う

```
CLAUDE_CODE_OAUTH_TOKEN
```

**理由：**
1. ✅ 公式ドキュメントのサンプルがそのまま動く
2. ✅ 後で調整する必要がない
3. ✅ 他の人（将来の自分含む）が見てもわかりやすい
4. ✅ トラブルシューティング時に検索しやすい

### もし変えたい場合：

自由に変えてOKですが：
- ⚠️ ワークフローファイルで同じ名前を使う必要がある
- ⚠️ 大文字とアンダースコアのルールを守る

---

## まとめ

| 質問 | 答え |
|------|------|
| **厳密な決まりはある？** | ❌ ない（自由に決められる） |
| **推奨される名前は？** | ✅ `CLAUDE_CODE_OAUTH_TOKEN` |
| **変えてもいい？** | ✅ OK（ただしワークフローで調整が必要） |
| **なぜ「正確に」？** | ✅ 後でこの名前を使うから |

---

**結論：`CLAUDE_CODE_OAUTH_TOKEN` をそのまま使うのが最も簡単で確実です。**

この名前で進めてよろしいですか？それとも別の名前にしたいですか？