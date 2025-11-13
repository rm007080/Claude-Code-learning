# 03. Hooksの活用

## この章で学ぶこと

Hooksは、特定のイベント発生時に自動的に処理を実行する仕組みです。この章では、Hooksの種類、設定方法、実践的な活用例を学びます。

## Hooksとは？

Hooksは、Claude Codeの特定のイベントに反応して、自動的にコマンドやスクリプトを実行する機能です。

### Hooksのメリット

1. **自動化**
   - 毎回手動で実行する必要がない
   - 作業の忘れを防ぐ

2. **一貫性**
   - 常に同じ処理が実行される
   - チーム全体で統一された動作

3. **効率化**
   - 繰り返し作業を自動化
   - 開発フローの最適化

4. **通知**
   - タスク完了を知らせる
   - 長時間タスクの監視

## Hooksの種類

Claude Codeでは主に2つのHooksが利用可能です。

### 1. Stop Hook

**発火タイミング**: Claude Codeが応答を停止した時

**用途**:
- タスク完了通知
- 後処理の実行
- レビューツールの起動

### 2. Notification Hook

**発火タイミング**: Claude Codeが通知を求めた時

**用途**:
- 確認が必要な操作の通知
- 重要な決定の記録
- アラートの送信

## Hooksの設定方法

### 設定ファイルの場所

Hooksは`settings.json`で設定します。

```
.claude/settings.json          # プロジェクト設定
.claude/settings.local.json    # 個人設定
~/.claude/settings.json        # グローバル設定
```

### 基本的な構造

```json
{
  "hooks": {
    "Stop": [
      {
        "matcher": "[条件]",
        "hooks": [
          {
            "type": "command",
            "command": "[実行するコマンド]"
          }
        ]
      }
    ],
    "Notification": [
      {
        "matcher": "[条件]",
        "hooks": [
          {
            "type": "command",
            "command": "[実行するコマンド]"
          }
        ]
      }
    ]
  }
}
```

## Stop Hookの活用

### 例1: タスク完了時の音声通知

**.claude/settings.local.json**:
```json
{
  "hooks": {
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "say \"Task completed!\""
          }
        ]
      }
    ]
  }
}
```

**動作**:
- Claude Codeがタスクを完了すると音声で通知

### 例2: タスク完了時のデスクトップ通知

**.claude/settings.local.json**:
```json
{
  "hooks": {
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "osascript -e 'display notification \"Claude Code completed a task\" with title \"Task Complete\"'"
          }
        ]
      }
    ]
  }
}
```

**動作**:
- macOSのNotification Centerに通知を表示

### 例3: 完了時にreviewitを起動

**.claude/settings.local.json**:
```json
{
  "hooks": {
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "~/.claude/scripts/launch-reviewit.sh /path/to/project"
          }
        ]
      }
    ]
  }
}
```

**スクリプト** (`~/.claude/scripts/launch-reviewit.sh`):
```bash
#!/bin/bash

REPO_PATH="${1:-$(pwd)}"

cd "$REPO_PATH" || exit 1

# Git差分がある場合のみreviewitを起動
if [[ -n $(git diff --name-only) || -n $(git diff --cached --name-only) || -n $(git ls-files --others --exclude-standard) ]]; then
  # 利用可能なポートを見つける
  max_attempts=1000
  attempt=0

  while [ $attempt -lt $max_attempts ]; do
    port=$((RANDOM % 6000 + 4000))

    if ! lsof -Pi :$port -sTCP:LISTEN -t >/dev/null 2>&1; then
      reviewit . --port $port &
      break
    fi

    ((attempt++))
  done

  if [ $attempt -eq $max_attempts ]; then
    echo "Error: Could not find an available port" >&2
    exit 1
  fi
fi
```

**スクリプトに実行権限を付与**:
```bash
chmod a+x ~/.claude/scripts/launch-reviewit.sh
```

**動作**:
- Claude Codeがタスクを完了
- Git差分がある場合、reviewitが起動
- 変更内容をWebUIでレビュー可能

### 例4: 条件付きの通知

**.claude/settings.local.json**:
```json
{
  "hooks": {
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "jq -r '.stop_hook_active' | grep -q false && say \"Task completed!\""
          }
        ]
      }
    ]
  }
}
```

**動作**:
- `stop_hook_active`がfalseの場合のみ通知
- 不要な通知を防ぐ

## Notification Hookの活用

### 例1: 確認が必要な操作の通知

**.claude/settings.local.json**:
```json
{
  "hooks": {
    "Notification": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "say \"Confirm, please!\""
          }
        ]
      }
    ]
  }
}
```

**動作**:
- Claude Codeが確認を求める時に音声で通知
- 作業に集中していても気づける

### 例2: 重要な操作のログ記録

**.claude/settings.local.json**:
```json
{
  "hooks": {
    "Notification": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "echo \"[$(date)] Notification triggered\" >> ~/.claude/notification.log"
          }
        ]
      }
    ]
  }
}
```

**動作**:
- 通知が発生するたびにログに記録
- 後で確認可能

## 複数のHooksを組み合わせる

### 例: タスク完了時に複数の処理を実行

**.claude/settings.local.json**:
```json
{
  "hooks": {
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "say \"Task completed!\""
          }
        ]
      },
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "osascript -e 'display notification \"Check the results\" with title \"Claude Code\"'"
          }
        ]
      },
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "~/.claude/scripts/launch-reviewit.sh $(pwd)"
          }
        ]
      }
    ]
  }
}
```

**動作**:
1. 音声で通知
2. デスクトップ通知を表示
3. reviewitを起動

## プロジェクト固有のHooks

### プロジェクトごとの通知メッセージ

**.claude/settings.local.json** (プロジェクトA):
```json
{
  "hooks": {
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "say \"Project A task completed!\""
          }
        ]
      }
    ]
  }
}
```

**.claude/settings.local.json** (プロジェクトB):
```json
{
  "hooks": {
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "say \"Project B task completed!\""
          }
        ]
      }
    ]
  }
}
```

**動作**:
- プロジェクトごとに異なる通知メッセージ
- どのプロジェクトの作業が完了したか分かる

## 実践的なHooks活用例

### 例1: ビルド成功時の通知

**.claude/settings.local.json**:
```json
{
  "hooks": {
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "~/.claude/scripts/check-build-success.sh"
          }
        ]
      }
    ]
  }
}
```

**スクリプト** (`~/.claude/scripts/check-build-success.sh`):
```bash
#!/bin/bash

# 最後のビルドログをチェック
if grep -q "Build succeeded" build.log 2>/dev/null; then
  say "Build succeeded!"
  osascript -e 'display notification "Build completed successfully" with title "Success"'
else
  say "Build failed!"
  osascript -e 'display notification "Build failed. Check the logs." with title "Error"'
fi
```

### 例2: テスト完了時の結果通知

**.claude/settings.local.json**:
```json
{
  "hooks": {
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "~/.claude/scripts/test-result-notification.sh"
          }
        ]
      }
    ]
  }
}
```

**スクリプト** (`~/.claude/scripts/test-result-notification.sh`):
```bash
#!/bin/bash

# package.jsonがあるディレクトリでテスト結果をチェック
if [ -f "package.json" ]; then
  # 最後のnpm testの結果をチェック
  npm test --silent 2>&1 | tail -n 5 | grep -q "PASS"

  if [ $? -eq 0 ]; then
    say "All tests passed!"
  else
    say "Some tests failed!"
  fi
fi
```

### 例3: 長時間タスクの時間計測

**.claude/settings.local.json**:
```json
{
  "hooks": {
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "~/.claude/scripts/task-timer.sh stop"
          }
        ]
      }
    ]
  }
}
```

**スクリプト** (`~/.claude/scripts/task-timer.sh`):
```bash
#!/bin/bash

TIMER_FILE="$HOME/.claude/task_timer"

case "$1" in
  start)
    date +%s > "$TIMER_FILE"
    ;;
  stop)
    if [ -f "$TIMER_FILE" ]; then
      start_time=$(cat "$TIMER_FILE")
      end_time=$(date +%s)
      duration=$((end_time - start_time))

      minutes=$((duration / 60))
      seconds=$((duration % 60))

      say "Task completed in $minutes minutes and $seconds seconds"
      rm "$TIMER_FILE"
    fi
    ;;
esac
```

**使用方法**:
```bash
# タスク開始時
~/.claude/scripts/task-timer.sh start

# Claude Codeで作業
> [長時間かかる作業]

# タスク完了時（自動実行）
# "Task completed in X minutes and Y seconds"
```

## デバッグとトラブルシューティング

### Hooksが動作しない場合

#### 確認1: JSON構文エラー

```bash
# settings.jsonの構文チェック
cat .claude/settings.local.json | jq .
```

エラーが表示される場合は、構文を修正してください。

#### 確認2: コマンドのパス

```bash
# コマンドが実行可能か確認
which say
which osascript

# スクリプトの実行権限を確認
ls -la ~/.claude/scripts/
```

#### 確認3: Claude Codeの再起動

```bash
> /exit
claude
```

### Hooksのデバッグ

**ログ出力を追加**:
```json
{
  "hooks": {
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "echo \"Stop hook triggered at $(date)\" >> ~/.claude/hook-debug.log && say \"Task completed!\""
          }
        ]
      }
    ]
  }
}
```

**ログを確認**:
```bash
tail -f ~/.claude/hook-debug.log
```

## preferredNotifChannelとの併用

### terminal_bellとの組み合わせ

**.claude/settings.local.json**:
```json
{
  "preferredNotifChannel": "terminal_bell",
  "hooks": {
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "say \"Task completed!\""
          }
        ]
      }
    ]
  }
}
```

**動作**:
1. ターミナルベル音が鳴る（標準機能）
2. 音声で通知（Hook）

## ベストプラクティス

### 1. 個人設定はsettings.local.jsonに

```json
// .claude/settings.local.json (Git管理外)
{
  "hooks": {
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "say \"Task completed!\""
          }
        ]
      }
    ]
  }
}
```

### 2. チーム設定はsettings.jsonに

```json
// .claude/settings.json (Git管理)
{
  "hooks": {
    "Stop": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "npm run post-task"
          }
        ]
      }
    ]
  }
}
```

### 3. 環境に依存しないコマンドを使う

```bash
# ❌ macOS専用
"command": "say \"Task completed!\""

# ✅ クロスプラットフォーム
"command": "echo \"Task completed\""
```

### 4. エラーハンドリング

```bash
# エラーを無視
"command": "some-command 2>/dev/null || true"

# エラーをログに記録
"command": "some-command 2>> ~/.claude/error.log || true"
```

## まとめ

### Hooksの価値

1. **自動化**: 手動作業の削減
2. **通知**: 長時間タスクの完了を知らせる
3. **一貫性**: 常に同じ処理を実行
4. **柔軟性**: シェルスクリプトで自由にカスタマイズ

### 主要なHooks

- **Stop Hook**: タスク完了時
- **Notification Hook**: 確認が必要な時

### 実践的な活用

- 音声通知
- デスクトップ通知
- reviewitの自動起動
- ログ記録
- テスト結果の通知

### 次のステップ

次の章では、通知とタスク完了の自動化をさらに深く学びます。reviewitの詳細な活用方法、カスタム通知スクリプトの作成など、より高度なテクニックを習得します。
