---
title: "Claude Code のオプションを使いこなそう｜Claude Code入門 - AI駆動開発の基礎を1日で身につけよう"
source: "https://zenn.dev/tmasuyama1114/books/claude_code_basic/viewer/setup_and_basic_operations_claude_code_options"
author:
  - "[[Zenn]]"
published:
created: 2025-10-21
description:
tags:
  - "clippings"
related:
---
Claude Code をインストールできたら、  
次は起動オプションについて学びましょう。

起動オプションを知っていると、作業効率が大きく変わります。

基本的な使い方から順番に見ていきましょう。

## 起動オプションとは？

起動オプションは、Claude Code を起動する際に  
追加できる指示のことです。

通常は `claude` と入力するだけで起動しますが、  
オプションを使うともっと便利になります。

たとえば、昨日の作業の続きから始めたり、  
素早く質問だけしたりできるのです。

## 基本の起動方法

### 通常の対話モード

一番シンプルな起動方法から始めましょう。

```bash
claude
```

このコマンドで Claude Code との対話が始まります。

質問したり、コードを書いてもらったり、  
自由に会話できる状態になります。

そして `/exit` と入力すると、元のターミナルに戻り、Claude Code から抜け出すことができます。

## 便利な起動オプション

### 1\. 最新セッションの継続（--continue）

直前の作業の続きから始められる機能です。

```bash
$ claude --continue
```

このオプションが活躍する場面：

- 休憩から戻ってきた時
- エラーで一度終了してしまった時
- 昨日の作業の続きをしたい時

作業の流れが途切れずに済むので、とても実用的な機能です。

### 2\. 過去のセッションから選択（--resume）

過去の会話履歴から選んで再開できます。

```bash
$ claude --resume

# 短縮形も使えます
$ claude -r
```

実行すると過去のセッションが一覧表示されます。

```bash
$ claude --resume

    Modified    Created     # Messages Git Branch     Summary
❯ 1. 1m ago      12m ago              8 -              Simple HTML File Creation Assistance
  2. 31m ago     35m ago              5 -              Tailwind CSS Layout Design from Japanese Image
  ...
```

番号を選ぶだけで、その時の会話から再開できる仕組みです。

`--continue` との違いは、直前以外の会話にも遡れることです。

### 3\. 質問を直接投げる（--prompt）

対話モードに入らずに、  
直接質問して答えをもらう方法です。

```bash
$ claude -p "index.html の中身を説明して"
```

素早く答えが欲しい時に重宝します。

ただし、ファイルの編集を伴うような命令の場合だと許可設定が必要になるため、個人的にはあまり使う場面は多くありません。

```bash
$ claude -p "index.html の中に追記し、現在時刻を表示するJavaScriptのコードを書いて"

# 返答
index.htmlに現在時刻を表示するコードを追記する許可をお願いします。1秒ごとに更新れる時計を追加します。
```

### 4\. 権限チェックをスキップ（--dangerously-skip-permissions）

⚠️ **上級者向け・要注意オプション**

Claude Code は通常、  
危険なコマンドを実行する前に確認を求めます。

このオプションを使うと、  
その確認をスキップしてしまいます。

```bash
claude --dangerously-skip-permissions
```

**使っても良い場面：**

- VM（仮想マシン）内での作業
- テスト環境での自動化
- 完全に信頼できる環境

**絶対に使ってはいけない場面：**

- 本番環境
- 重要なデータがあるマシン
- 初心者の練習時

最悪、 `sudo rm -rf /` のような危険なコマンドも実行される可能性があります。

慎重に扱うべきオプションです。

### 5\. アップデートの確認と実行

Claude Code を最新版に更新するコマンドです。

```bash
$ claude update

Current version: 1.0.67
Checking for updates...
```

定期的にアップデートすることで、  
新機能や改善を受けられます。

## 日常的な開発作業での流れ

開発の流れに合わせた使い分けの例です。

```bash
# 朝一番：昨日の続きから
claude --continue

# 新しいタスク：通常起動
claude

# ちょっとした質問：プロンプトモード
claude -p "このコードのレビューをして"
```

作業内容に応じて使い分けると良いでしょう。

## 知っておくと便利な小技

### 画像を添付する

クリップボードの画像をClaude Code に渡す方法です。

```bash
# スクリーンショットを撮る（Mac）
cmd + shift + ctrl + 4

# Claude Code を起動
claude

# Ctrl + V で画像を貼り付け
```

デザインを見せて実装を依頼する時などにとても便利な機能です。

### エイリアスを設定する

よく使うコマンドは短縮形を作ると便利です。

```bash
# ~/.bashrc または ~/.zshrc に追加
alias cc="claude"
alias ccr="claude --resume"
alias ccc="claude --continue"
alias ccp="claude -p"
```

設定後は短いコマンドで起動できます。

```bash
cc  # claude の代わりに
ccr  # claude --resume の代わりに
```

タイピングの手間が省けて快適です。

## まとめ

Claude Code の起動オプションは、作業効率を大きく向上させる機能です。

特に重要な3つのオプション：

- `--continue` ：作業の継続
- `--resume` ：過去のセッションから選択
- `-p` ：クイック質問

まずはこの3つから使い始めて、慣れてきたら他のオプションも試してみてください。

次のレッスンでは、対話モード内で使える便利な操作方法を学びます。

### 参考リンク

- [Claude Code チュートリアル（Zenn）](https://zenn.dev/pepabo/articles/898cdc4839acb8)
- [Claude Code 公式ドキュメント](https://docs.anthropic.com/en/docs/claude-code/cli-reference)