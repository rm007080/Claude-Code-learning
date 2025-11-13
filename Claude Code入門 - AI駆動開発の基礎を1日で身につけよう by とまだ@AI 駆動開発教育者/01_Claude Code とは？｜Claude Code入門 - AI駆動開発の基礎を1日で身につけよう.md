---
title: "Claude Code とは？｜Claude Code入門 - AI駆動開発の基礎を1日で身につけよう"
source: "https://zenn.dev/tmasuyama1114/books/claude_code_basic/viewer/introduction_claude_code_overview"
author:
  - "[[Zenn]]"
published:
created: 2025-10-21
description:
tags:
  - "clippings"
related:
---
Claude Code は、Anthropic が提供するエージェント型のAIコーディングツールです。

ターミナルから直接使用でき、長時間の複雑なタスクを自律的に実行する能力を持っています。  
本記事では、Claude Code の基本概念と特徴について解説します。

## エージェント型開発ツールとは

従来のAIコーディングツールは、主に「補完」や「提案」を中心としていました。  
一方、Claude Code は「エージェント型」として設計されています。

エージェント型とは、単なる補助ではなく、自律的に判断して行動するAIのことです。  
開発者が大まかな指示を出すと、以下のような作業を連続して実行します。

- プロジェクト全体の構造を理解
- 必要なファイルを特定して読み込み
- コードの生成・修正・削除
- テストの実行と結果の確認
- エラーの自動修正

これらの作業を長時間でも実行できるのが、Claude Code の大きな特徴です。

## Claude Code の主要機能

ここでは、まず Claude Code の特徴や、できることを簡単に紹介します。

※示しているコマンドは例であり、インストールなどは次回以降の Lesson で解説します。

### 1\. ターミナルベースの操作

IDEプラグインではなく、ターミナルから直接使用します。

```bash
# Claude Code を起動
claude
```

他の AI エージェントツールは IDE（エディタ）に組み込まれるものが多かったですが、Claude Code 環境を選ばずに使用できるため、開発の柔軟性が大幅に向上します。

### 2\. CLAUDE.md による永続的なコンテキスト

プロジェクトごとに「CLAUDE.md」というファイルを生成し、プロジェクトの規約や構造を記憶します。

```bash
> /init  # プロジェクトを分析してCLAUDE.mdを生成
```

このファイルには主に以下の情報が含まれます。

- プロジェクトの構造とアーキテクチャ
- コーディング規約
- 使用している技術スタック
- 重要な設計内容

一度生成すれば、その後のセッションでも同じコンテキストを維持できます。

### 3\. 画像からのコード生成

デザイン画像をドラッグ&ドロップするだけで、UIコードを生成できます。

```bash
# 画像ファイルをペーストして貼り付けたとき
> [Image #1]
```

生成されるコードは、プロジェクトの技術スタックに合わせて自動調整されます。

## 性能とベンチマーク

Claude Code は、業界標準のベンチマークで高いスコアを記録しています。

**SWE-bench（ソフトウェアエンジニアリング能力を測定）**

- Claude Code: 72.7%
- 他の主要ツール: 40-50%程度

**長時間タスクの成功率**

- 1時間以上のタスク: 90%以上
- エラー自動修正率: 85%

複雑なタスクでも高い精度を維持できることを示していることがわかります。

## 導入に必要な知識

誰でも開発を簡単に行うためのものですが、効果的に使用するには以下の基礎知識があると開発がスムーズになります。

**ターミナル操作**

- 基本的なコマンドライン操作
- ファイルシステムの理解
- パスの概念

**開発環境**

- Node.js のインストールと管理
- npmやyarnの基本操作
- Git の基本的な使い方

## まとめ

Claude Code はターミナルで動作する自律的なAI開発アシスタントです。

長時間の複雑なタスクを高精度で実行できる能力により、開発生産性を大幅に向上させることができます。  
特に大規模なリファクタリングや技術移行において、その真価を発揮します。

次回は、Claude Code のインストールと初期設定について詳しく解説します。

## 動画でも解説しています

「テキストだとわかりにくい」という方は、動画でも解説していますのでぜひご覧ください！

<iframe src="https://www.youtube-nocookie.com/embed/Xr_HhLuzOy8" allow="accelerometer; clipboard-write; encrypted-media; gyroscope; picture-in-picture" allowfullscreen=""></iframe>

### 参考資料

- [Claude Code 公式ドキュメント](https://docs.anthropic.com/en/docs/claude-code/introduction)
- [Awesome Claude Code (GitHub)](https://github.com/hesreallyhim/awesome-claude-code) - コミュニティによるリソース集
- [Claude Code チュートリアル (Zenn)](https://zenn.dev/pepabo/articles/898cdc4839acb8)