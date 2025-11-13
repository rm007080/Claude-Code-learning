# 認証とAPI設定

## はじめに

Claude Codeを使用するには、Anthropicのサービスへの認証が必要です。この章では、2つの認証方法（OAuth Token と API Key）の違い、それぞれの設定方法、そしてトラブルシューティングについて詳しく解説します。

## 認証方法の種類

Claude Codeには2つの認証方法があります:

### 1. OAuth Token（Claude App認証）

**概要**
- Claude.aiのProまたはMaxプランを使用
- ブラウザでログインして認証
- 定額制（月額固定料金）

**メリット**
- セットアップが簡単
- コスト管理がシンプル（定額）
- 使用量を気にしなくて良い
- 初心者におすすめ

**デメリット**
- 1日の使用量に制限がある
- Proプランは制限に達しやすい
- 複数デバイスでの共有が難しい

**向いている人**
- 初めてClaude Codeを使う人
- 個人開発者
- コスト管理をシンプルにしたい人
- 学習目的で使用する人

### 2. API Key（Anthropic Console認証）

**概要**
- Anthropic ConsoleでAPIキーを発行
- コンソールから直接設定
- 従量課金制（使った分だけ支払い）

**メリット**
- 使用量の上限がない
- 複数デバイス・チームメンバーで共有可能
- プログラムからの直接呼び出しが可能
- 詳細な使用量分析が可能

**デメリット**
- セットアップがやや複雑
- コスト管理が必要（予想外の高額請求のリスク）
- 月末まで正確な金額がわからない

**向いている人**
- 上級者・プロ開発者
- チームでの利用
- 企業での導入
- 使用量が少ない人（月数回程度）
- 細かいコスト管理をしたい人

## 方法1: OAuth Token認証（推奨）

### 事前準備

Claude.aiのProまたはMaxプランに加入している必要があります:

1. [https://claude.ai/](https://claude.ai/) にアクセス
2. アカウント作成またはログイン
3. ProまたはMaxプランにアップグレード

### 認証手順

**Step 1: Claude Codeを起動**

ターミナルで以下を実行:

```bash
claude
```

初回起動の場合、設定画面が表示されます。

**Step 2: 認証方法の選択**

以下のような選択肢が表示されます:

```
How would you like to authenticate?

❯ 1. Claude App (Pro or Max subscription)
  2. Anthropic Console (API key)
```

十字キーで **1. Claude App** を選択し、Enterキーを押します。

**Step 3: ブラウザで認証**

自動的にブラウザが開き、以下の画面が表示されます:

```
Claude Code would like to:
- Access your Claude account
- Use Claude API on your behalf

[承認する] [キャンセル]
```

「承認する」をクリックします。

**Step 4: 認証完了**

ブラウザに「認証が完了しました」と表示されます。

ターミナルに戻ると、Claude Codeが使用可能になります:

```
╭────────────────────────────────────────────────────────────────╮
│ ✻ Welcome to Claude Code!                                      │
│                                                                │
│   /help for help, /status for your current setup              │
│                                                                │
│   cwd: /Users/username/projects                               │
╰────────────────────────────────────────────────────────────────╯
```

**これで認証完了です！**

### WSL環境での特別な手順

WSL（Windows Subsystem for Linux）を使用している場合、ブラウザ認証後に追加の手順があります:

**1. ブラウザで認証を完了**

通常通り、ブラウザで「承認する」をクリックします。

**2. 認証コードの取得**

認証完了後、ブラウザに以下のようなコードが表示されます:

```
認証が完了しました。
以下のコードをターミナルに入力してください:

ABC123DEF456GHI789
```

このコードをコピーします。

**3. ターミナルにコードを入力**

WSLターミナルに戻ると、以下のように表示されています:

```
Enter the authorization code:
```

コピーしたコードを貼り付けてEnterキーを押します。

**4. 認証完了**

Claude Codeが使用可能になります。

### 認証情報の保存場所

認証情報は以下の場所に保存されます:

```
~/.claude/credentials.json
```

このファイルには認証トークンが含まれているため、**他人と共有しないでください**。

### 認証の更新

OAuth Tokenには有効期限があります。期限切れの場合、以下のようなメッセージが表示されます:

```
Authentication expired. Please re-authenticate.
```

再度`claude`を起動すると、自動的に再認証フローが開始されます。

---

## 方法2: API Key認証（上級者向け）

### 事前準備

Anthropic ConsoleでAPIキーを発行する必要があります。

**Step 1: Anthropic Consoleにアクセス**

1. ブラウザで [https://console.anthropic.com/](https://console.anthropic.com/) を開く
2. アカウント作成またはログイン

**Step 2: 支払い方法の登録**

APIキーを使用するには、支払い方法の登録が必須です:

1. 左メニューから「Billing」をクリック
2. 「Add payment method」をクリック
3. クレジットカード情報を入力
4. 保存

**Step 3: APIキーの生成**

1. 左メニューから「API Keys」をクリック
2. 「Create Key」をクリック
3. キーの名前を入力（例: `claude-code-dev`）
4. 「Create」をクリック

生成されたAPIキーが表示されます:

```
sk-ant-api03-XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

**重要**: このAPIキーは**一度しか表示されません**。必ずコピーして安全な場所に保存してください。

### Claude CodeでAPI Keyを設定

**方法A: 初回起動時に設定**

```bash
claude
```

認証方法の選択で「2. Anthropic Console (API key)」を選択:

```
How would you like to authenticate?

  1. Claude App (Pro or Max subscription)
❯ 2. Anthropic Console (API key)
```

APIキーの入力を求められます:

```
Enter your Anthropic API key:
```

コピーしたAPIキーを貼り付けてEnterキーを押します。

**方法B: 環境変数で設定**

APIキーを環境変数として設定することもできます:

**macOS / Linux / WSL:**

```bash
echo 'export ANTHROPIC_API_KEY="sk-ant-api03-YOUR-KEY-HERE"' >> ~/.bashrc
source ~/.bashrc
```

zshの場合:

```bash
echo 'export ANTHROPIC_API_KEY="sk-ant-api03-YOUR-KEY-HERE"' >> ~/.zshrc
source ~/.zshrc
```

**Windows (PowerShell):**

```powershell
[System.Environment]::SetEnvironmentVariable('ANTHROPIC_API_KEY', 'sk-ant-api03-YOUR-KEY-HERE', 'User')
```

PowerShellを再起動後、設定が反映されます。

**方法C: 設定ファイルで管理**

`~/.claude/credentials.json`を直接編集:

```json
{
  "apiKey": "sk-ant-api03-YOUR-KEY-HERE"
}
```

### API Keyの動作確認

```bash
claude --version
```

エラーが表示されず、バージョン番号が表示されればOKです。

実際に起動してみます:

```bash
claude
```

以下のように表示されれば成功:

```
╭────────────────────────────────────────────────────────────────╮
│ ✻ Welcome to Claude Code!                                      │
│   Using Anthropic API Key                                     │
╰────────────────────────────────────────────────────────────────╯
```

「Using Anthropic API Key」と表示されていれば、API Key認証で動作しています。

### API使用量の確認

Anthropic Consoleで使用量を確認できます:

1. [https://console.anthropic.com/](https://console.anthropic.com/) にアクセス
2. 左メニューから「Usage」をクリック
3. リアルタイムの使用量とコストが表示される

**重要**: 定期的に確認して、予想外の高額請求を防ぎましょう。

### 使用量制限の設定（推奨）

予算オーバーを防ぐため、使用量の上限を設定できます:

1. Anthropic Console → 「Billing」
2. 「Usage Limits」セクション
3. 「Set monthly limit」をクリック
4. 上限金額を入力（例: $50）
5. 保存

上限に達すると、自動的にAPIの使用が停止されます。

---

## 認証方法の切り替え

OAuth TokenとAPI Keyを切り替えることができます。

### OAuth Token → API Keyに切り替え

**Step 1: 認証情報を削除**

```bash
rm ~/.claude/credentials.json
```

**Step 2: Claude Codeを起動**

```bash
claude
```

再度認証方法の選択画面が表示されるので、API Keyを選択。

### API Key → OAuth Tokenに切り替え

同様に、認証情報を削除してから再起動します:

```bash
rm ~/.claude/credentials.json
claude
```

OAuth Tokenを選択。

---

## トラブルシューティング

### 認証エラー: 「Invalid authentication」

**原因**
- APIキーが間違っている
- APIキーが無効化されている
- 認証トークンが期限切れ

**解決方法**

1. 認証情報を削除:
   ```bash
   rm ~/.claude/credentials.json
   ```

2. 再認証:
   ```bash
   claude
   ```

### ブラウザが開かない

**原因**
- デフォルトブラウザが設定されていない
- ファイアウォールがブロックしている

**解決方法（OAuth Token）**

手動で認証URLを開く:

```bash
claude
# 表示されるURLをコピー
# ブラウザに貼り付けて認証
```

**解決方法（API Key）**

OAuth Tokenではなく、API Keyを使用:

```bash
rm ~/.claude/credentials.json
claude
# API Keyを選択
```

### API使用量が予想以上に多い

**原因**
- Opusモデルの長時間使用
- 大量のトークンを消費する作業

**解決方法**

1. Sonnetモデルに切り替え:
   ```bash
   > /model
   # Sonnetを選択
   ```

2. /compactで会話を圧縮:
   ```bash
   > /compact
   ```

3. 使用量制限を設定（Anthropic Console）

### 環境変数が反映されない

**原因**
- シェルの設定ファイルが読み込まれていない
- 間違ったファイルに追加した

**解決方法**

使用しているシェルを確認:

```bash
echo $SHELL
```

- `/bin/bash` → `.bashrc`に追記
- `/bin/zsh` → `.zshrc`に追記

設定を反映:

```bash
source ~/.bashrc  # または ~/.zshrc
```

### 複数デバイスで同じ認証を使いたい

**OAuth Tokenの場合**

各デバイスで個別に認証が必要です。

**API Keyの場合**

同じAPIキーを各デバイスで設定すれば共有できます:

```bash
# デバイスAとデバイスBで同じAPIキーを設定
export ANTHROPIC_API_KEY="sk-ant-api03-SAME-KEY"
```

**注意**: チーム全体で同じAPIキーを共有すると、使用量の管理が難しくなります。チームメンバーごとに別のAPIキーを発行することを推奨します。

---

## セキュリティのベストプラクティス

### APIキーの管理

**やるべきこと**
- APIキーは安全な場所に保存
- 環境変数で管理（ハードコードしない）
- 定期的にキーをローテーション
- 使用量を定期的に確認

**やってはいけないこと**
- APIキーをGitリポジトリにコミット
- APIキーを他人と共有（本人以外）
- APIキーをSlackなどで送信
- APIキーをスクリーンショットに含める

### .gitignoreの設定

プロジェクトのルートに`.gitignore`を作成:

```gitignore
# Claude Code認証情報
.claude/credentials.json
.env

# APIキーを含む可能性のあるファイル
*.key
*.secret
config.local.json
```

### 認証トークンの有効期限

OAuth Tokenには有効期限があります:

- 通常: 30日
- 期限切れ前に自動更新を促すメッセージが表示される
- 期限切れ後は再認証が必要

API Keyには有効期限がありませんが、セキュリティのため定期的に更新することを推奨します。

---

## どちらの認証方法を選ぶべきか

### 判断フローチャート

```
あなたの状況は？
│
├─ 初めてClaude Codeを使う
│  └→ 【OAuth Token】
│     理由: セットアップが簡単、定額で安心
│
├─ 個人開発、定期的に使う
│  └→ 【OAuth Token】
│     理由: コスト管理がシンプル
│
├─ チームで使う、使用量管理が必要
│  └→ 【API Key】
│     理由: 詳細な使用量分析、複数人での共有
│
├─ 月に数回しか使わない
│  └→ 【API Key】
│     理由: 使わない月は課金されない
│
└─ 企業での導入、監査が必要
   └→ 【API Key】
      理由: 詳細なログ、コスト管理
```

### 推奨

**初心者・個人開発者**
- OAuth Token（Claude App Pro）
- 月額$20で使い放題
- セットアップ簡単

**プロフェッショナル・チーム**
- API Key
- 使用量に応じた課金
- 詳細な管理が可能

---

## まとめ

Claude Codeの認証方法:

**OAuth Token（推奨）**
- ブラウザで簡単認証
- 定額制で安心
- 初心者向け

**API Key（上級者向け）**
- Anthropic Consoleで発行
- 従量課金制
- 詳細な管理が可能

どちらの方法でも、認証完了後は同じようにClaude Codeを使用できます。

まずはOAuth Tokenで始めて、必要に応じてAPI Keyに移行するのがおすすめです。

次章では、Claude Codeの初期設定と動作確認について解説します。
