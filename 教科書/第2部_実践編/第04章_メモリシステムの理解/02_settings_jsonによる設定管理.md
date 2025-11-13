# 02. settings.jsonによる設定管理

## この章で学ぶこと

settings.jsonは、Claude Codeの動作を細かく制御するための設定ファイルです。この章では、settings.jsonの構造、主要な設定項目、プロジェクト設定とグローバル設定の使い分けを学びます。

## settings.jsonとは？

settings.jsonは、Claude Codeに対して「OK行動」と「NG行動」を伝えるための設定ファイルです。子供にお使いを頼む時のルール設定に似ています。

### 設定の比喩

- 「おつりでお菓子を買うのはOK」 → allowリスト
- 「知らない人についていくのはダメ」 → denyリスト
- 「1000円以内なら自分で判断していいよ」 → どちらにも含めない

## 設定ファイルの種類と優先順位

settings.jsonは3つの場所に配置でき、それぞれ異なる優先順位があります。

### 優先順位（上が最優先）

1. **`.claude/settings.local.json`** (最優先)
   - 個人用のプロジェクト設定
   - Gitで管理しない (.gitignoreに追加)
   - 自分だけの特別ルール

2. **`.claude/settings.json`** (次優先)
   - チームで共有するプロジェクト設定
   - Gitで管理
   - プロジェクト固有のルール

3. **`~/.claude/settings.json`** (最低優先)
   - グローバル設定
   - 全プロジェクト共通
   - 個人の基本ルール

### 設定の上書きルール

上位の設定が下位の設定を上書きします。

```
settings.local.json → settings.json → ~/.claude/settings.json
     (最優先)         (プロジェクト)      (グローバル)
```

## 基本構造

settings.jsonの基本的な構造を理解しましょう。

```json
{
  "permissions": {
    "allow": [
      // 自動的に許可するコマンド・操作
    ],
    "deny": [
      // 絶対に実行しないコマンド・操作
    ]
  },
  "autoUpdates": true,
  "env": {
    // 環境変数の設定
  },
  "hooks": {
    // Hooksの設定（後述）
  }
}
```

## 主要設定項目

### 1. permissions (権限設定)

最も重要な設定項目です。詳細は次の章で説明しますが、基本的な概念を理解しましょう。

#### 判断の優先順序

1. **denyリストにある** → 実行しない（赤信号）
2. **allowリストにある** → 自動実行（青信号）
3. **どちらにもない** → ユーザーに確認（黄信号）

#### 基本的な設定例

```json
{
  "permissions": {
    "allow": [
      "Bash(ls:*)",
      "Bash(cat:*)",
      "Bash(npm test)",
      "Read(src/**/*)"
    ],
    "deny": [
      "Bash(rm -rf:*)",
      "Bash(sudo:*)",
      "Read(.env)",
      "Write(/etc/*)"
    ]
  }
}
```

### 2. autoUpdates (自動更新)

Claude Codeの自動更新を制御します。

```json
{
  "autoUpdates": true  // または false
}
```

**推奨設定**: `true` (最新機能とセキュリティ修正を自動適用)

### 3. env (環境変数)

プロジェクトで使用する環境変数を設定できます。

```json
{
  "env": {
    "NODE_ENV": "development",
    "NEXT_TELEMETRY_DISABLED": "1",
    "LOG_LEVEL": "debug"
  }
}
```

**注意**: 機密情報（APIキー、パスワード等）は含めないでください。

### 4. preferredNotifChannel (通知設定)

タスク完了時の通知方法を設定します。

```json
{
  "preferredNotifChannel": "terminal_bell"  // または "none"
}
```

オプション:
- `"terminal_bell"`: ターミナルベル音
- `"none"`: 通知なし

## プロジェクト設定 vs グローバル設定

### プロジェクト設定の作成

プロジェクト固有の設定を作成します。

```bash
# ディレクトリ作成
mkdir -p .claude

# 設定ファイル作成
touch .claude/settings.json
```

**使用例: Reactプロジェクト**

```json
{
  "permissions": {
    "allow": [
      "Bash(npm run dev)",
      "Bash(npm run build)",
      "Bash(npm test:*)",
      "Bash(npm run lint)",
      "Read(src/**/*)",
      "Read(public/**/*)",
      "Write(src/**/*)"
    ],
    "deny": [
      "Read(.env*)",
      "Write(package.json)",
      "Bash(npm publish)"
    ]
  },
  "env": {
    "NODE_ENV": "development"
  }
}
```

### グローバル設定の作成

全プロジェクトで共通の設定を作成します。

```bash
# ディレクトリ作成
mkdir -p ~/.claude

# 設定ファイル作成
touch ~/.claude/settings.json
```

**使用例: 個人の基本ルール**

```json
{
  "permissions": {
    "allow": [
      "Bash(ls:*)",
      "Bash(cat:*)",
      "Bash(mkdir:*)",
      "Bash(touch:*)",
      "Bash(echo:*)"
    ],
    "deny": [
      "Bash(sudo:*)",
      "Bash(rm -rf:*)",
      "Read(**/*secret*)",
      "Read(**/*token*)",
      "Read(**/*key*)",
      "Read(id_rsa)",
      "Read(id_ed25519)"
    ]
  },
  "autoUpdates": true
}
```

### ローカル設定の作成

個人用のプロジェクト設定を作成します（Git管理外）。

```bash
# 設定ファイル作成
touch .claude/settings.local.json

# .gitignoreに追加
echo ".claude/settings.local.json" >> .gitignore
```

**使用例: 個人の実験的設定**

```json
{
  "permissions": {
    "allow": [
      "Bash(npm install:*)"  // 自分だけ自動許可
    ]
  },
  "env": {
    "DEBUG": "true"
  }
}
```

## 実践的な設定例

### フロントエンド開発向け設定

```json
{
  "permissions": {
    "allow": [
      // ファイル操作
      "Bash(ls:*)",
      "Bash(cat:*)",
      "Bash(mkdir:*)",
      "Bash(touch:*)",
      "Bash(mv:*)",
      "Bash(cp:*)",

      // 開発サーバー
      "Bash(npm run dev)",
      "Bash(npm run start)",

      // ビルド
      "Bash(npm run build)",
      "Bash(npm run build:*)",

      // テスト
      "Bash(npm test)",
      "Bash(npm run test:*)",
      "Bash(npm run test:watch)",

      // Lint/Format
      "Bash(npm run lint)",
      "Bash(npm run lint:*)",
      "Bash(npm run format)",
      "Bash(npm run format:*)",

      // 型チェック
      "Bash(npm run typecheck)",
      "Bash(npm run tsc:*)",

      // ファイル読み書き
      "Read(src/**/*)",
      "Read(public/**/*)",
      "Read(tests/**/*)",
      "Write(src/**/*)",
      "Write(tests/**/*)"
    ],
    "deny": [
      // 危険なコマンド
      "Bash(sudo:*)",
      "Bash(rm -rf:*)",
      "Bash(git reset --hard:*)",
      "Bash(git push --force:*)",

      // 機密情報
      "Read(.env*)",
      "Read(**/*secret*)",
      "Read(**/*token*)",
      "Write(.env*)",

      // 重要ファイル
      "Write(package.json)",
      "Write(package-lock.json)",
      "Write(tsconfig.json)"
    ]
  },
  "env": {
    "NODE_ENV": "development",
    "NEXT_TELEMETRY_DISABLED": "1"
  }
}
```

### バックエンド開発向け設定

```json
{
  "permissions": {
    "allow": [
      // 基本コマンド
      "Bash(ls:*)",
      "Bash(cat:*)",
      "Bash(grep:*)",
      "Bash(find:*)",

      // 開発サーバー
      "Bash(npm run dev)",
      "Bash(npm start)",

      // テスト
      "Bash(npm test:*)",
      "Bash(npm run test:unit)",
      "Bash(npm run test:integration)",

      // データベース
      "Bash(npm run db:migrate)",
      "Bash(npm run db:seed)",
      "Bash(npx prisma:*)",

      // ファイル操作
      "Read(src/**/*)",
      "Read(tests/**/*)",
      "Write(src/**/*)",
      "Write(tests/**/*)"
    ],
    "deny": [
      // 危険なコマンド
      "Bash(sudo:*)",
      "Bash(rm:*production*)",
      "Bash(npm run db:drop)",
      "Bash(npm run db:reset)",

      // 機密情報
      "Read(.env*)",
      "Read(**/*secret*)",
      "Read(**/*key*)",
      "Read(config/secrets/**/*)",
      "Write(.env*)",
      "Write(config/secrets/**/*)",

      // 本番環境
      "Bash(*production*)",
      "Write(**/*production*)"
    ]
  },
  "env": {
    "NODE_ENV": "development",
    "LOG_LEVEL": "debug"
  }
}
```

### モノレポ向け設定

```json
{
  "permissions": {
    "allow": [
      // Turboコマンド
      "Bash(turbo run dev)",
      "Bash(turbo run build)",
      "Bash(turbo run test:*)",
      "Bash(turbo run lint)",

      // pnpmコマンド
      "Bash(pnpm install:*)",
      "Bash(pnpm run:*)",
      "Bash(pnpm test:*)",

      // ワークスペース操作
      "Bash(pnpm --filter:*)",

      // ファイル操作
      "Read(packages/**/*)",
      "Read(apps/**/*)",
      "Write(packages/**/*)",
      "Write(apps/**/*)"
    ],
    "deny": [
      "Bash(sudo:*)",
      "Read(packages/*/.env*)",
      "Read(apps/*/.env*)",
      "Write(pnpm-workspace.yaml)",
      "Bash(pnpm publish:*)"
    ]
  }
}
```

## 初心者向けの最小限設定

まずはシンプルな設定から始めましょう。

```json
{
  "permissions": {
    "allow": [
      // ファイル確認
      "Bash(ls:*)",
      "Bash(cat:*)",

      // 開発
      "Bash(npm run dev)",
      "Bash(npm run build)",
      "Bash(npm test)"
    ],
    "deny": [
      // 危険な操作
      "Bash(rm -rf:*)",
      "Bash(sudo:*)",

      // 機密情報
      "Read(.env)"
    ]
  }
}
```

慣れてきたら徐々に設定を追加していきましょう。

## 設定ファイルの作成手順

### ステップ1: ディレクトリ作成

```bash
# プロジェクト設定用
mkdir -p .claude

# グローバル設定用
mkdir -p ~/.claude
```

### ステップ2: ファイル作成

```bash
# プロジェクト設定
touch .claude/settings.json

# ローカル設定（Git管理外）
touch .claude/settings.local.json

# グローバル設定
touch ~/.claude/settings.json
```

### ステップ3: .gitignoreに追加

```bash
# ローカル設定をGit管理から除外
echo ".claude/settings.local.json" >> .gitignore
```

### ステップ4: 設定を記述

エディタで設定ファイルを開き、必要な設定を記述します。

```bash
# VSCodeで開く
code .claude/settings.json
```

## 設定の検証

### 設定が正しく読み込まれているか確認

```bash
# Claude Codeを起動
claude

# 設定を確認
> /config
```

### 権限の動作確認

```bash
# allowリストのコマンドを実行（確認なしで実行されるはず）
> npm testを実行してください

# denyリストのコマンドを実行（実行が拒否されるはず）
> sudo apt-get updateを実行してください

# どちらにもないコマンド（確認が求められるはず）
> git commitを実行してください
```

## トラブルシューティング

### 設定が反映されない

**原因1: JSONの構文エラー**

```bash
# JSONの妥当性を確認
cat .claude/settings.json | jq .
```

エラーが表示される場合は、構文を修正してください。

**原因2: ファイルパスが間違っている**

```bash
# ファイルの存在確認
ls -la .claude/settings.json
ls -la ~/.claude/settings.json
```

**原因3: セッションの再起動が必要**

```bash
> /exit
# 再度起動
claude
```

### 優先順位がわからない

どの設定が適用されているか確認する方法:

```bash
> 現在の権限設定を教えてください
```

## ベストプラクティス

### 1. 段階的に設定を育てる

最初から完璧を目指さず、必要に応じて追加していきましょう。

```
Week 1: 最小限の設定でスタート
Week 2: よく使うコマンドをallowに追加
Week 3: 危険なコマンドをdenyに追加
Week 4: 環境変数などの詳細設定
```

### 2. チーム設定と個人設定を分ける

```
.claude/settings.json        → チームで共有（Git管理）
.claude/settings.local.json  → 個人用（Git管理外）
~/.claude/settings.json      → 全プロジェクト共通（個人）
```

### 3. コメントで設定の理由を記述

JSONではコメントが使えませんが、README.mdに設定の意図を記載しましょう。

```markdown
# Claude Code 設定について

## 権限設定の方針
- `npm install`は手動確認（セキュリティリスク）
- `npm test`は自動許可（安全な操作）
- `git commit`は手動確認（意図しないコミット防止）
```

### 4. 定期的に見直す

月に1回程度、設定を見直して最適化しましょう。

- 使わなくなった設定を削除
- 新しいワークフローに合わせて追加
- セキュリティ設定の見直し

## まとめ

### 重要ポイント

1. **3つの設定ファイル**を理解する
   - settings.local.json（最優先・個人）
   - settings.json（プロジェクト・共有）
   - ~/.claude/settings.json（グローバル・個人）

2. **段階的に設定を育てる**
   - 最初はシンプルに
   - 必要に応じて追加
   - 定期的に見直し

3. **安全性を最優先**
   - 危険なコマンドはdenyに
   - 機密情報は保護
   - よく使う安全な操作のみallowに

4. **チームと個人を分離**
   - 共有すべき設定とそうでない設定を区別
   - .gitignoreを適切に設定

### 次のステップ

次の章では、permissions設定の詳細、特にallow/denyの高度な使い方とセキュリティのベストプラクティスを学びます。
