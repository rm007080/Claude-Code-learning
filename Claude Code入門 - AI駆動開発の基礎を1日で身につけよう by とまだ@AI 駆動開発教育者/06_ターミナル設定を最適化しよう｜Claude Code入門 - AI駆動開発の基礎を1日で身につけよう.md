---
title: "ターミナル設定を最適化しよう｜Claude Code入門 - AI駆動開発の基礎を1日で身につけよう"
source: "https://zenn.dev/tmasuyama1114/books/claude_code_basic/viewer/setup_and_basic_operations_terminal_settings"
author:
  - "[[Zenn]]"
published:
created: 2025-10-21
description:
tags:
  - "clippings"
related:
---
Claude Code を使い始めて、改行ができなかったり、長い文章が入力しづらかったりして困っていませんか？

ターミナルの設定を少し調整するだけで、Claude Code がとても使いやすくなります。

ここまでも少し触れてきましたが、快適に Claude Code を使うためのターミナル設定について解説していきます。

## プロンプトでの入力中に改行できるようにしよう

Claude Code で複数行のメッセージを送りたい時、Enter キーを押すとそのまま送信されてしまいます。

コードを含む質問や、詳細な指示を出したい時に困りますよね。

### 基本の改行方法

どのターミナルでも使える方法から紹介します。

```
> React のコンポーネントを作って。\
名前は Button で、\
props として label と onClick を受け取るようにして
```

`\` （バックスラッシュ）+ Enter で改行できます。Windows の場合は `¥` （円マーク）+ Enter です。

この方法の良い点は、どんな環境でも確実に動作することです。ただし、毎回バックスラッシュを入力するのは少し面倒かもしれません。

### Mac Terminal.app の設定

Mac の標準ターミナルを使っている方は、Option + Enter で改行できるように設定できます。

**設定手順：**

1. ターミナルを開く
2. メニューバーから「ターミナル」→「設定」を選択（または Cmd +,）
3. 「プロファイル」タブをクリック
4. 左側のリストから使用中のプロファイルを選択
5. 「キーボード」タブを開く
6. 「Option キーを Meta キーとして使用」にチェック

設定が完了したら、以下のように使えます。

```
> 以下のエラーを解決して [Option + Enter]
TypeError: Cannot read property 'name' of undefined [Option + Enter]
at UserProfile.js:15:23
```

### iTerm2 の詳細設定

iTerm2 は多くのエンジニアが使う高機能ターミナルです。

まず iTerm2 の中で Claude Code を起動し、Claude Code の自動設定を試してみましょう。

```
> /terminal-setup
```

このコマンドが成功すると、Shift + Enter で改行できるようになります。

もし自動設定がうまくいかない場合は、手動で設定します。

**手動設定の手順：**

1. iTerm2 を開く
2. メニューバーから「iTerm2」→「Settings」を選択
3. 「Profiles」タブをクリック
4. 左側のプロファイルリストから使用中のものを選択
5. 「Keys」タブを開く
6. 下部の「Left Option Key」を「Esc+」に設定
7. 「Right Option Key」も「Esc+」に設定

設定後、Option + Enter で改行できるようになります。

### VS Code ターミナルの設定

VS Code の統合ターミナルを使っている場合も、自動設定を使えます。

```
> /terminal-setup
```

自動設定後は Shift + Enter で改行できます。

## まとめ

ターミナル設定は最初は面倒に感じるかもしれませんが、一度設定してしまえばずっと快適に使えます。

他にもいくつか設定項目がありますが、実行する環境ごとに大きく異なるため、公式ドキュメントへのリンクだけお付けしておきます。

- [ターミナルセットアップの最適化](https://docs.anthropic.com/ja/docs/claude-code/terminal-config)

### 参考リンク

- [ターミナルセットアップの最適化](https://docs.anthropic.com/ja/docs/claude-code/terminal-config)