# プロジェクトでのMCP設定

## はじめに

この章では、プロジェクトでMCPサーバーを設定し、チームで共有する方法を学びます。`.mcp.json`ファイルの作成から、セキュリティを考慮した設定管理まで、実践的な手法を解説します。

## `.mcp.json`ファイルの作成

### 基本構造

プロジェクトルートに `.mcp.json` ファイルを作成します。

```json
{
  "mcpServers": {
    "server-name": {
      "command": "command-to-run",
      "args": ["arg1", "arg2"],
      "env": {
        "ENV_VAR": "value"
      }
    }
  }
}
```

### 実例: 基本的な設定

```json
{
  "mcpServers": {
    "context7": {
      "transport": "sse",
      "url": "https://mcp.context7.com/sse"
    },
    "playwright": {
      "command": "npx",
      "args": ["@playwright/mcp"]
    }
  }
}
```

## 設定ファイルの種類

### 1. プロジェクト設定: `.mcp.json`

**配置場所:**
```
project-root/
├── .mcp.json          ← ここ
├── .claude/
├── src/
└── package.json
```

**用途:**
- プロジェクト固有のMCPサーバー
- チームで共有する設定
- プロジェクトに必須のMCPサーバー

**例:**
```json
{
  "mcpServers": {
    "figma": {
      "command": "npx",
      "args": ["@anthropic-ai/figma-mcp"],
      "env": {
        "FIGMA_ACCESS_TOKEN": "${FIGMA_ACCESS_TOKEN}"
      }
    },
    "context7": {
      "transport": "sse",
      "url": "https://mcp.context7.com/sse"
    }
  }
}
```

### 2. グローバル設定: `~/.claude/settings.json`

**配置場所:**
```
~/.claude/
└── settings.json      ← ここ
```

**用途:**
- すべてのプロジェクトで使うMCPサーバー
- 個人の好みのMCPサーバー
- 汎用的なツール

**例:**
```json
{
  "mcpServers": {
    "chrome-devtools": {
      "command": "npx",
      "args": ["chrome-devtools-mcp"]
    },
    "playwright": {
      "command": "npx",
      "args": ["@playwright/mcp"]
    }
  }
}
```

### 3. プロジェクト個人設定: `.claude/settings.local.json`

**配置場所:**
```
project-root/
├── .claude/
│   └── settings.local.json   ← ここ
├── .mcp.json
└── src/
```

**用途:**
- プロジェクト内での個人的な設定
- チームには共有しないMCPサーバー
- 開発環境固有の設定

**例:**
```json
{
  "mcpServers": {
    "my-custom-tool": {
      "command": "node",
      "args": ["/Users/myname/tools/custom-mcp.js"]
    }
  }
}
```

### 設定の優先順位

```
高優先度
  ↓
.claude/settings.local.json（プロジェクト個人設定）
  ↓
.mcp.json（プロジェクト設定）
  ↓
~/.claude/settings.json（グローバル設定）
  ↓
低優先度
```

同じ名前のMCPサーバーが複数の設定ファイルに定義されている場合、優先度の高い設定が使われます。

## MCPサーバーの設定例

### stdio方式（ローカル実行）

```json
{
  "mcpServers": {
    "figma": {
      "command": "npx",
      "args": ["@anthropic-ai/figma-mcp"],
      "env": {
        "FIGMA_ACCESS_TOKEN": "${FIGMA_ACCESS_TOKEN}"
      }
    },
    "playwright": {
      "command": "npx",
      "args": ["@playwright/mcp"]
    },
    "custom-tool": {
      "command": "node",
      "args": ["./scripts/custom-mcp.js"],
      "cwd": "${PROJECT_ROOT}"
    }
  }
}
```

**要素の説明:**
- `command`: 実行するコマンド（`npx`, `node`, `python`など）
- `args`: コマンドの引数（配列）
- `env`: 環境変数（オブジェクト）
- `cwd`: 作業ディレクトリ（オプション）

### SSE方式（リモートサーバー）

```json
{
  "mcpServers": {
    "context7": {
      "transport": "sse",
      "url": "https://mcp.context7.com/sse"
    },
    "notion": {
      "transport": "sse",
      "url": "https://mcp.notion.com/sse",
      "headers": {
        "Authorization": "Bearer ${NOTION_API_KEY}"
      }
    }
  }
}
```

**要素の説明:**
- `transport`: `"sse"` を指定
- `url`: MCPサーバーのURL
- `headers`: HTTPヘッダー（認証など、オプション）

## 環境変数の管理

### セキュリティの原則

**❌ 絶対にやってはいけないこと:**
```json
{
  "mcpServers": {
    "figma": {
      "env": {
        "FIGMA_ACCESS_TOKEN": "figd_xxxxxxxxxxxxxxxxxxxxx"
      }
    }
  }
}
```
→ トークンを直接記述すると、Gitにコミットされてしまう危険性

**✅ 正しい方法:**
```json
{
  "mcpServers": {
    "figma": {
      "env": {
        "FIGMA_ACCESS_TOKEN": "${FIGMA_ACCESS_TOKEN}"
      }
    }
  }
}
```
→ 環境変数を参照する

### 環境変数の設定方法

#### 方法1: シェルの設定ファイル（推奨）

**~/.zshrc または ~/.bashrc に追加:**
```bash
# Figma Access Token
export FIGMA_ACCESS_TOKEN="figd_xxxxxxxxxxxxxxxxxxxxx"

# Notion API Key
export NOTION_API_KEY="secret_xxxxxxxxxxxxxxxxxxxxx"
```

**適用:**
```bash
# 設定を読み込み
source ~/.zshrc  # または source ~/.bashrc

# 確認
echo $FIGMA_ACCESS_TOKEN
```

#### 方法2: `.env` ファイル（プロジェクト固有）

**プロジェクトルートに `.env` ファイルを作成:**
```bash
# .env
FIGMA_ACCESS_TOKEN=figd_xxxxxxxxxxxxxxxxxxxxx
NOTION_API_KEY=secret_xxxxxxxxxxxxxxxxxxxxx
```

**`.gitignore` に追加（重要）:**
```
# .gitignore
.env
.env.local
```

**direnvを使って自動読み込み:**
```bash
# direnvのインストール
brew install direnv  # macOS
sudo apt install direnv  # Linux

# .zshrcに追加
eval "$(direnv hook zsh)"

# プロジェクトディレクトリで
direnv allow
```

#### 方法3: 1Passwordなどのシークレット管理ツール

**1Password CLIを使う例:**
```bash
# 1Password CLIのインストール
brew install --cask 1password-cli

# .zshrcに追加
export FIGMA_ACCESS_TOKEN=$(op read "op://Private/Figma/token")
```

## テンプレートファイルの提供

### `.mcp.json.example` の作成

チームメンバーが簡単にセットアップできるように、テンプレートファイルを提供します。

**`.mcp.json.example`:**
```json
{
  "mcpServers": {
    "figma": {
      "command": "npx",
      "args": ["@anthropic-ai/figma-mcp"],
      "env": {
        "FIGMA_ACCESS_TOKEN": "${FIGMA_ACCESS_TOKEN}"
      }
    },
    "context7": {
      "transport": "sse",
      "url": "https://mcp.context7.com/sse"
    }
  }
}
```

**README.mdに手順を記載:**
```markdown
## MCP設定

### 初回セットアップ

1. `.mcp.json.example` を `.mcp.json` にコピー
   \`\`\`bash
   cp .mcp.json.example .mcp.json
   \`\`\`

2. Figmaアクセストークンを取得
   - Figmaにログイン
   - Settings → Account → Personal Access Tokens
   - トークンを生成

3. 環境変数を設定
   \`\`\`bash
   # ~/.zshrc に追加
   export FIGMA_ACCESS_TOKEN="your-token-here"

   # 設定を読み込み
   source ~/.zshrc
   \`\`\`

4. 動作確認
   \`\`\`bash
   claude
   > Figma MCPが利用可能か確認してください
   \`\`\`
```

### `.gitignore` の設定

**`.gitignore`:**
```
# MCP設定（機密情報を含む可能性）
.mcp.json

# 環境変数
.env
.env.local

# Claude Code個人設定
.claude/settings.local.json
```

**`.gitignore.example` または README.mdで説明:**
```markdown
## Git管理

以下のファイルは `.gitignore` に含まれています：
- `.mcp.json` - プロジェクト固有のMCP設定
- `.env` - 環境変数
- `.claude/settings.local.json` - 個人設定

これらは各自でセットアップしてください。
```

## チーム共有のベストプラクティス

### 方法1: 認証不要のMCPサーバーのみ共有

**`.mcp.json`（Gitにコミット）:**
```json
{
  "mcpServers": {
    "context7": {
      "transport": "sse",
      "url": "https://mcp.context7.com/sse"
    },
    "playwright": {
      "command": "npx",
      "args": ["@playwright/mcp"]
    }
  }
}
```

認証が必要なMCPサーバーは、各自が個人設定に追加します。

### 方法2: 環境変数を使った設定の共有

**`.mcp.json`（Gitにコミット）:**
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

**README.md:**
```markdown
## 必要な環境変数

以下の環境変数を設定してください：

- `FIGMA_ACCESS_TOKEN` - Figmaのアクセストークン
  - 取得方法: Figma Settings → Account → Personal Access Tokens

\`\`\`bash
# ~/.zshrc に追加
export FIGMA_ACCESS_TOKEN="your-token-here"
\`\`\`
```

### 方法3: 組織のシークレット管理ツールを使用

**例: AWS Secrets Manager、1Password、Vault**

**`.mcp.json`:**
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

**セットアップスクリプト（`scripts/setup-secrets.sh`）:**
```bash
#!/bin/bash

# 1Passwordから取得
export FIGMA_ACCESS_TOKEN=$(op read "op://Team/Figma/token")

echo "環境変数をセットアップしました"
echo "Claude Codeを起動してください: claude"
```

## プロジェクトタイプ別の推奨設定

### Webアプリケーション

**`.mcp.json`:**
```json
{
  "mcpServers": {
    "context7": {
      "transport": "sse",
      "url": "https://mcp.context7.com/sse"
    },
    "playwright": {
      "command": "npx",
      "args": ["@playwright/mcp"]
    },
    "chrome-devtools": {
      "command": "npx",
      "args": ["chrome-devtools-mcp"]
    }
  }
}
```

### デザインシステム開発

**`.mcp.json`:**
```json
{
  "mcpServers": {
    "figma": {
      "command": "npx",
      "args": ["@anthropic-ai/figma-mcp"],
      "env": {
        "FIGMA_ACCESS_TOKEN": "${FIGMA_ACCESS_TOKEN}"
      }
    },
    "context7": {
      "transport": "sse",
      "url": "https://mcp.context7.com/sse"
    },
    "playwright": {
      "command": "npx",
      "args": ["@playwright/mcp"]
    }
  }
}
```

### ライブラリ開発

**`.mcp.json`:**
```json
{
  "mcpServers": {
    "context7": {
      "transport": "sse",
      "url": "https://mcp.context7.com/sse"
    }
  }
}
```

## トラブルシューティング

### 問題: MCPサーバーが起動しない

**症状:**
```
Error: Failed to start MCP server 'figma'
```

**確認事項:**

1. **コマンドが正しいか**
   ```bash
   # 手動で実行してみる
   npx @anthropic-ai/figma-mcp
   ```

2. **環境変数が設定されているか**
   ```bash
   echo $FIGMA_ACCESS_TOKEN
   ```

3. **パッケージがインストールされているか**
   ```bash
   npm list -g @anthropic-ai/figma-mcp
   ```

### 問題: 環境変数が読み込まれない

**症状:**
```
Error: FIGMA_ACCESS_TOKEN is not defined
```

**解決策:**

1. **シェルを再起動**
   ```bash
   # 設定を再読み込み
   source ~/.zshrc

   # 確認
   echo $FIGMA_ACCESS_TOKEN
   ```

2. **Claude Codeを再起動**
   ```bash
   # 一度終了
   exit

   # 再起動
   claude
   ```

### 問題: MCPサーバーが正しく動作しない

**デバッグ方法:**

```bash
# MCPサーバーのログを確認
claude mcp logs figma

# MCPサーバーを再起動
claude mcp restart figma

# MCPサーバーの状態を確認
claude mcp status
```

## セットアップチェックリスト

### 新規プロジェクトでのセットアップ

- [ ] `.mcp.json.example` をコピーして `.mcp.json` を作成
- [ ] 必要な環境変数を設定（`~/.zshrc` または `.env`）
- [ ] `.mcp.json` を `.gitignore` に追加
- [ ] README.mdにセットアップ手順を記載
- [ ] チームメンバーに共有
- [ ] 動作確認

### 既存プロジェクトへのMCP追加

- [ ] 現在のMCP設定を確認（`claude mcp list`）
- [ ] 新しいMCPサーバーを `.mcp.json` に追加
- [ ] 必要な環境変数を設定
- [ ] 動作確認
- [ ] README.mdを更新
- [ ] チームに通知

## まとめ

### 重要なポイント

1. **セキュリティ**: 機密情報は環境変数で管理
2. **チーム共有**: `.mcp.json.example` とREADME.mdで手順を明確に
3. **設定の分離**: プロジェクト設定とグローバル設定を使い分ける
4. **テンプレート提供**: 新メンバーのオンボーディングを容易に

### ファイル構成の例

```
project-root/
├── .mcp.json               # Gitに含めない（.gitignoreに追加）
├── .mcp.json.example       # Gitにコミット（テンプレート）
├── .gitignore              # .mcp.jsonを含める
├── .env                    # Gitに含めない
├── .claude/
│   └── settings.local.json # Gitに含めない（個人設定）
├── README.md               # セットアップ手順を記載
└── src/
```

### 次章の内容

次の章では、MCPを活用した実践例を学びます。具体的なセットアップ手順と活用方法を、実際のプロジェクトに基づいて詳しく解説します。
