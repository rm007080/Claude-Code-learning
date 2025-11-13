---
title: "スラッシュコマンドを攻略しよう｜Claude Code入門 - AI駆動開発の基礎を1日で身につけよう"
source: "https://zenn.dev/tmasuyama1114/books/claude_code_basic/viewer/setup_and_basic_operations_slash_commands"
author:
  - "[[Zenn]]"
published:
created: 2025-10-21
description:
tags:
  - "clippings"
related:
---
Claude Code の対話中に使える「スラッシュコマンド」は、作業効率を大きく左右する大事な機能です。今回は、よく使うコマンドから便利な隠れ機能まで、実践的な使い方を詳しく解説していきます。

## スラッシュコマンドとは？

スラッシュコマンドは、対話中に `/` で始まるコマンドを入力することで、Claude Code の動作を制御できる機能です。

[公式ドキュメント](https://docs.anthropic.com/ja/docs/claude-code/slash-commands) によると、現在20以上のコマンドが用意されています。

その中でも、よく使うものを紹介していきます。

## 会話管理

### /clear - 会話履歴をクリア

最も重要なコマンドの一つです。

```
> /clear
```

このコマンドは会話履歴を完全にリセットします。

以下のタイミングで使うと効果的です。

- 新しいタスクを始める時
- 全く別の機能を実装する時
- エラーが続いて混乱した時

タスクが変わったら必ず `/clear` を実行することで、Claude Code の精度を維持できます。

### /compact - 会話を要約してトークンを節約

```
> /compact
```

現在の会話を要約して、トークン使用量を削減します。

長い会話が続いた時には活用しましょう。

同じプロジェクトでも、ここまでの作業を引き継ぎつつ、別な作業に移る時には実行しておきます。

なお、このコマンドを実行しなくても自動的に要約はされますが、次第に精度が落ちてくる上、トークンももったいないので、区切りの良いタイミングで実行することをおすすめします。

## プロジェクト管理

### /init - プロジェクト用の CLAUDE.md を生成

```
> /init
```

プロジェクト全体を分析して、 `CLAUDE.md` ファイルを自動生成します。

生成される内容には以下が含まれます。

- プロジェクトの概要
- 技術スタック
- ディレクトリ構造
- 主要なコンポーネント
- コーディング規約

実行後はこのようなファイルが作成されます。

```markdown
# CLAUDE.md

This file provides guidance to Claude Code when working with this repository.

## Architecture
- Frontend: React 18 with TypeScript
- Backend: Node.js/Express
- Database: PostgreSQL
...
```

新しいプロジェクトを始めたら、まず `/init` を実行しましょう。

## 設定・情報確認

### /config - 設定の表示/変更

```
> /config
```

現在の設定を表示し、対話的に変更できます。

設定可能な項目の例：

- テーマ（dark/light）
- エディタ統合
- 通知設定
- 使用モデル

### /help - ヘルプとコマンド一覧表示

```
> /help
```

利用可能なすべてのスラッシュコマンドと説明を表示します。コマンドを忘れた時はまずこれを実行しましょう。

## 権限の管理・確認

### /permissions - 権限設定の管理

```
> /permissions
```

Claude Code が実行できるコマンドや操作の権限を管理します。

設定例：

```json
{
  "allow": [
    "Bash(npm test)",
    "Read(src/**/*)"
  ],
  "deny": [
    "Bash(rm -rf *)",
    "Write(/etc/*)"
  ]
}
```

詳しい使い方については、後のレッスンで説明します。

## その他の便利コマンド

### モデル切り替えのテクニック

```
> /model
```

利用するAIモデルを切り替えられます。

選択可能なモデル：

- **Default**: 使用量に応じて自動切り替え
- **Opus**: 最高性能（コスト高）
- **Sonnet**: バランス型

高度な設計などを行う際は Default または Opus を選択しておき、使用制限に近づいたら Sonnet に切り替えることで、作業を継続できます。

切り替えが面倒な方は、常に Default で大丈夫です。

### /doctor - インストールの健全性チェック

```
> /doctor
```

Claude Code の環境を診断します。

チェック内容：

- Node.js バージョン
- 必要なパッケージ
- 環境変数
- ネットワーク接続

問題がある場合は、解決方法も提示されます。

## 実践的な使い方

### 効率的なワークフロー

基本的には以下のような流れで作業すると効率的です。

```bash
# 1. 新規プロジェクト開始
> /init

# 2. 機能実装
> ユーザー認証機能を実装して

# 3. 会話が長くなったら要約
> /compact 認証機能の内容を保持

# 4. 別のタスクに移る
> /clear
```

## まとめ

スラッシュコマンドを使いこなすと、Claude Code の効率が飛躍的に向上します。

特に重要なのは以下の3つです。

- `/clear` - タスク切り替え時は必須
- `/init` - プロジェクト開始時の第一歩
- `/compact` - トークン管理の要

これらのコマンドを適切に使いこなして、快適な開発環境を構築しましょう。

次のレッスンでは、ターミナル設定を最適化する方法を学んでいきます！

### 参考リンク

- [Claude Code スラッシュコマンド公式ドキュメント](https://docs.anthropic.com/ja/docs/claude-code/slash-commands)
- [Claude Code 実践ガイド（Zenn）](https://zenn.dev/hokuto_tech/articles/86d1edb33da61a)