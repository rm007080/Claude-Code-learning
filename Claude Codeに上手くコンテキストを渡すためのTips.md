---
title: "Claude Codeに上手くコンテキストを渡すためのTips"
source: "https://zenn.dev/knowledgework/articles/9b82d0c0a34ab4"
author:
  - "[[Zenn]]"
published: 2025-09-25
created: 2025-10-13
description:
tags:
  - "clippings"
related:
---
[株式会社ナレッジワーク](https://zenn.dev/p/knowledgework)

91

48[idea](https://zenn.dev/tech-or-idea)

この記事は [KNOWLEDGE WORK Blog Sprint](https://zenn.dev/knowledgework/articles/20fb768ee2e5e7) の25日目の記事になります。

Claude Code CLI を触っていると、「ちょっと面倒だな」と感じるポイントが出てきます。この記事では、実際に Cluade Code を使いながら見つけた工夫を3つの Tips として紹介します。

この記事はVSCodeユーザ向けに執筆しています。

## 課題: コンテキストに指定するファイルのパス指定

Claude Code CLI では、コンテキストとしてファイルの情報を渡すときに `@hoge/fuga.ts` のように書く必要があります。

![](https://storage.googleapis.com/zenn-user-upload/b3c61a9761d6-20250924.png)

この時、ファイルパスをサジェストしてくれるのですがその動作が重たかったり、いちいち今開いているファイルのパスを見に行ったりするのが億劫に感じる時があります。

## 解決策その１： VSCode拡張を使う

[Claude Code for VS Code](https://marketplace.visualstudio.com/items?itemName=anthropic.claude-code) を使うと、今開いているファイルを自動でコンテキストに載せてくれるので、パス指定を毎回書かずに済みます。

![](https://storage.googleapis.com/zenn-user-upload/b04019649173-20250924.png)

特定のファイルについてじっくりと調査や実装を進めたい場合はこの拡張を入れるだけでかなり効率がアップします。

また、

> Selection context: Selected text in the editor is automatically added to Claude’s context

とあるように、選択したテキストのみをコンテキストとして使用することも可能です。

## 解決策その2: 相対パスをサクッとコピーできるようにする

とはいえ「複数ファイルを一気にコンテキストに指定したい」シーンもあります。その場合は `@` を使って指定する必要があります。  
少しでもパス指定の負担を減らすために、VSCode の `keybindings.json` に以下を追加して、ショートカットで相対パスをコピーできるようにしておくと便利です。

```json
{
  "key": "alt+cmd+c",
  "command": "copyRelativeFilePath",
  "when": "editorTextFocus"
}
```

これでエディタで対象ファイルを開いた状態で Alt+Cmd+C を押せば、そのファイルの相対パスがクリップボードにコピーされます。

### keybindings.json の開き方

`cmd + P` から検索して開くことができます。  
![](https://storage.googleapis.com/zenn-user-upload/32e4b44cc38f-20250924.png)

## 課題: 指示がうまく伝わらない・伝え方がわからない

プロンプトを書いて指示することがAIを使ったコーディングの醍醐味ではありますが、曖昧な指示だと Claude が勝手に解釈してしまうこともあります。複雑なドメイン知識・ロジックを持つ関数の実装を手伝って欲しかったのに、自分の意図しない方向に Claude が舵を切って、余計に疲れてしまう…なんてこともあると思います。

## 解決策： まずは欲しい型を詳しく書く

そんなときはまず「欲しい型」を書いて、そのプロパティごとにコメントで細かく指示を出すのがおすすめです。

```typescript
// 日付ごとに ToDo をまとめた結果
export type TodosByDate = Record<
  string, // "YYYY-MM-DD" 形式の日付
  string[] // その日にやるタスク名
>

// この型に合うように関数を書いて
// 入力: todos: { id: string; title: string; dueDate: string }[]
// 出力: TodosByDate
```

このようにすることで、長文プロンプトを毎回書かなくても型定義自体が仕様書代わりになります。

## 課題: 指示の範囲を超えた意図しないことも対応してしまう

指示がうまく伝わるようになったかと思いきや、型エラーなどに過剰に反応して必要以上にファイルの差分を増やしてしまう時があります。デバック用に埋め込んでいる `console.log` を根こそぎ消し去っていったりと、「ありがたいけど、今欲しい差分ではない」と思うこともありがちかと思います。

## 解決策： 「#」を使ってCLAUDE.mdを更新する （to memorize）

コマンド最初に「#」をつけてテキストを打ち込むと、その内容を `CLAUDE.md` に保存してくれます。

![](https://storage.googleapis.com/zenn-user-upload/976944d5d4b0-20250922.png)

時間を見つけてじっくり `CLAUDE.md` を調整していくのももちろん大事ですが、指示を出していく中で都度思いついた追加の指示をメモ的に残していくのに便利です。

## まとめ

Claude Code に上手くコンテキストを渡すための Tips は以下の通りです。

1. VSCode 拡張やショートカットで @ファイルパス指定を楽にする
2. 型＋コメントで指示を簡潔に伝える
3. 気になることがあれば「#」を使ってCLAUDE.mdに追加する

小さな工夫を積み重ねることで、Claude とのやり取りがぐっとスムーズになります。  
皆さんも良きAIコーディングライフを！

[KNOWLEDGE WORK Blog Sprint](https://zenn.dev/knowledgework/articles/20fb768ee2e5e7) の26日目の執筆者は AI Group の Shisho さんです。ぜひ続けてお楽しみください。

91

48

### Discussion

![](https://static.zenn.studio/images/drawing/discussion.png)

記事についてコメントする  

[コミュニティガイドライン](https://zenn.dev/guideline) に則った投稿をしましょう。