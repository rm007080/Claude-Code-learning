---
title: Claude Code 内で専門家を作成！新機能 Sub agents で用途専門AIを作って品質改善してみた
source: https://qiita.com/tomada/items/a6fba5876dbc9304ca5a
author:
  - "[[tomada]]"
published: 2025-07-28
created: 2025-11-03
description: こんにちは、とまだです。 Claude Code で数日前に追加された Sub agent という機能、もう使ったでしょうか？ 正直、名前だけじゃ何の機能かピンと来ませんでした。 こちらはざっくり言うと 「Claude Code 内にさらに専門家を作る」 機能です。 ...
tags:
  - clippings
related:
---
![](https://relay-dsp.ad-m.asia/dmp/sync/bizmatrix?pid=c3ed207b574cf11376&d=x18o8hduaj&uid=4193563)

こんにちは、とまだです。

Claude Code で数日前に追加された **Sub agent** という機能、もう使ったでしょうか？

正直、名前だけじゃ何の機能かピンと来ませんでした。

こちらはざっくり言うと **「Claude Code 内にさらに専門家を作る」** 機能です。

特にコードレビューや UI/UX 改善など、専門的な視点が必要なときに役立ちそうです。

今回は、私が運営している Learning Next というサイトで Sub agents を作って使ってみた体験を共有します。

（追記：SubAgents の観点を含めて Claude Code との比較記事も書きました）

## Sub agents とは？

まずは公式の説明をそのまま引用＆翻訳してみます。

> **サブエージェントとは何ですか？**
> 
> サブエージェントとは、Claude Codeがタスクを委譲できる事前設定されたAIペルソナのことです。各サブエージェントには以下の特徴があります：
> 
> - 特定の目的と専門分野を持っている
> - メインの会話とは別の独自のコンテキストウィンドウを使用する
> - 使用を許可された特定のツールで設定できる
> - その動作を導くカスタムシステムプロンプトが含まれている
> 
> Claude Codeがサブエージェントの専門分野に一致するタスクに遭遇すると、その専門化されたサブエージェントにタスクを委譲することができます。サブエージェントは独立して作業し、結果を返します。

ここで一番大事なのが **メインの会話とは別の独自のコンテキストウィンドウを使用する** ということです。

もう少し説明します。

これまでも、Claude Code で実装したあとに「レビューをして」とか「ドキュメントに沿ってUI/UXを改善して」など、Claude Code に追加で指示を出すことがありました。

これは「継続して同じ人に作業させる」ようなものです。

そのため、そこまでの実装過程の視点を良くも悪くも引き継ぎますし、何より長いコンテキストをそのまま引き継ぐため、トークンをたくさん消費してしまいます。

一方、Sub Agents は言うなれば **これまでの流れとは関係なく動く** というのが特徴のようです。

これによりフレッシュな視点でレビューをできますし、何よりゼロからチェックや作業を始めるため、トークンを節約できます。

もちろん、情報を引き継がないため、それはそれでデメリットではあります。

ただ、コードレビューだったり、なかなか抜け出せないバグ・テスト修正のタイミングで活用すると、独立した視点をうまく使えそうです。

## 実際に Sub agents を作ってみる

### まずは確認から

Claude Code を起動し、以下のコマンドを実行します。

```text
/agents
```

最初は以下のように何もない的な表示です。

```bash
Agents
No agents found
❯ Create new agent

No agents found. Create specialized subagents that Claude can delegate to.
Each subagent has its own context window, custom system prompt, and specific tools.
Try creating: Code Reviewer, Code Simplifier, Security Reviewer, Tech Lead, or UX Reviewer.

Built-in (always available):
general-purpose
```

推奨される Sub agents として、Code ReviewerやUX Reviewerなどが提案されています。

他にもセキュリティ観点とかテックリードなど、色々と例がありますね。

### Apple風デザイナーを作ってみる

では、実際に作ってみましょう。

「Create new agent」を選択すると、保存場所を選ぶ画面が出ます。

```text
Create new agent

Step 1: Choose location
❯ 1. Project (.claude/agents/)
  2. Personal (~/.claude/agents/)
```

プロジェクト専用にしたいので「Project」を選択。

次に作成方法です。

推奨の「Generate with Claude」を選びます。

そして役割を説明します。

デフォルトでは `Expert ...` のように、入力例が表示されています。

```text
Create new agent
Step 3: Describe what this agent should do and when it should be used

Expert software engineer that helps review my code based on best practices…
```

私はこう入力しました。

```text
優秀なWebデザイナーであり、Apple風で高級感のあるデザインシステムに則りつつ、
使いやすさや見やすさ、Core Web Vitals を意識したUI/UXが得意な人
```

次はツール選択画面が出てきます。

```text
Create new agent

Step 4: Select tools
❯ [ Continue ]
────────────────────────────
  ☒ All tools
  ☒ Read-only tools
  ☒ Edit tools
  ☒ Execution tools
  ☒ MCP & Other tools
```

コードも編集してもらいたいので「All tools」のままで。

色も選べます。  
Claude Code のコンソール内で Sub agents が呼び出されると、指定した色で区別されます。

```text
Create new agent
Step 5: Choose background color
Choose background color
❯ Automatic color
    Red
    Blue
    Green
```

これは、どの Sub agents が作業中か視覚的に分かるための工夫ですね。  
今回はデフォルトの Automatic color にしておきました。

最後に確認画面。

```text
Create new agent

Final step: Confirm and save

Name: apple-design-expert

Location: .claude/agents/apple-design-expert.md

Tools: All tools

Description (tells Claude when to use this agent):
  Use this agent when you need to create or review UI/UX designs that follow Apple's design principles with a focus on luxury aesthetics, usability, readability, and
  Core Web Vitals performance. This includes designing interfaces, reviewing d…
```

Enterを押すと、Sub agents の完成です！

## 作られた設定ファイルを覗いてみる

`.claude/agents/apple-design-expert.md` というファイルができました。

中を見ると、かなり詳しい設定が書かれています。

```md
---
name: apple-design-expert
description: Use this agent when you need to (...略)
---

You are an elite web designer specializing in Apple-inspired design systems with a deep understanding of luxury aesthetics, usability principles, and Core Web Vitals optimization. Your expertise combines visual excellence with technical performance.

**Your Design Philosophy:**
...
```

ざっくり書くと、以下のような内容です。

- **デザイン哲学**: ミニマリズム、明確性、余白の重要性
- **Apple デザイン原則**: 明快さ、控えめさ、奥行き、一貫性
- **技術基準**: Core Web Vitals の目標値（LCP < 2.5s など）
- **プロセス**: ユーザーニーズ分析から実装まで
- **アクセシビリティ**: WCAG AA準拠、タッチターゲット44px以上

ある程度、期待した視点をもったWebデザイナーが定義されたようです。

## 実際に使ってクーポンページを改善

では実際に使ってみましょう。

私が先日作った Udemy 講座の [クーポン配布ページ](http://localhost:3001/coupons/claude-code-vibe-coding) を直前に作っていたので、こちらを改善してみます。

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/364501/054084db-6f40-4998-80c4-e9e8a6aeb745.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F364501%2F054084db-6f40-4998-80c4-e9e8a6aeb745.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=50da01ed8994b632e3b803afecf539d7)

ひとまず適当に作ったんですが、無駄に横長だったり、要素が最低限すぎたりと、ちょうど改善したかったところです。

【ちょっと宣伝】  
他にも自作 Vibe Coding 講座のクーポン配布してるので、よかったら使ってくださいませ！  
[https://school.learning-next.app/coupons](https://school.learning-next.app/coupons)

### Sub agents を呼び出す

ドキュメントによると、Claude Code で以下のように呼び出す例がありました。

```text
Use the code-reviewer sub agent to check my recent changes
```

コマンドというよりは自然言語で呼び出すようですので、試しに日本語でやってみます。

```text
apple-design-expert を使ってデザインを最適化してください。
対象のパスは \`/coupons/claude-code-vibe-coding\` です。
```

入力すると、以下のように `apple-design-expert` という Sub agents の名前と共に、赤い表示となりました。

Sub agents を呼び出しているようです。

[![スクリーンショット 2025-07-27 10.05.22.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/364501/67394802-1a0d-43ef-af38-c5bcb600ed28.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F364501%2F67394802-1a0d-43ef-af38-c5bcb600ed28.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=bc1caee20a451eef626a726d040c5a5f)

しばらく待つと以下のように報告があがりました。

```bash
⏺ クーポン詳細ページのデザイン最適化完了報告

  実施した変更内容
  1. ダークモード関連の削除
  - Apple風の装飾的要素（グラデーション背景、blur効果）を削除
  - backdrop-blur、半透明要素など、ダークモード前提のスタイルを削除
  - システムがダークモード非対応のため、ライトモード専用のデザインに統一

  2. デザインシステムへの準拠
  - カラーパレット: zinc系カラーシステムに統一
    - テキスト: zinc-950（メイン）、zinc-700（セカンダリ）、zinc-600（キャプション）
    - 背景: 白背景とzinc-50を基調に
  - 角丸: rounded-3xlからrounded-lg/rounded-2xlに変更
  - 余白: 過度なpadding（p-10）を適切なサイズ（p-6）に調整
  ...（以下省略）
```

デザイン改善の一部を抜粋すると、以下のようになってくれました。

[![改善後のクーポンページ](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/364501/7ca51f89-2c55-46a2-aa80-3303c13223fd.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F364501%2F7ca51f89-2c55-46a2-aa80-3303c13223fd.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=bdb84f02889b35eddfa5e07aa04761ff)

見た目のベースは変えず、要素を追加したり、レイアウトを調整してくれています。

ただデザインを整えただけじゃなく、以下のように要素追加が行われています。

- クーポンの「節約額」を強調
- ボタンに「クーポン適用済み」と明記
- 「30日間の返金保証付き」で安心感を演出

デザインだけでなく、ユーザー体験まで考えた改善をしてくれたようです。

ちょっと想定外でしたが基本のデザインシステムは抑えつつ、見やすくデザインが改善されています。

## Sub agents を自動で使わせるコツ

今回は明示的に Sub agents を使うように指定していました。

一方、公式ドキュメントによると、必要なタイミングで自動的に Sub agents を呼び出すコツがあるようです。

今回で言うと `.claude/agents/apple-design-expert.md` の冒頭に書いてある `description` に追記します。

こちらに `MUST BE USED` という文字があると、より積極的に使われるようになります。

```yaml
---
name: apple-design-expert
description: MUST BE USED. Use this agent when you need (略)
```

この設定をして、あえて Sub agents を指定せずにデザイン改善を依頼してみましょう。

```bash
> \`/coupons\` という、Udemyクーポン一覧ページのデザインを最適化してください

  apple-design-expert(Optimize Udemy coupons page design)
  ⎿  Found 2 files (ctrl+r to expand)
     Found 8 files (ctrl+r to expand)
     +1 more tool use
```

[![Sub agents の色付き表示](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/364501/06a68e43-9bd2-403e-bb57-3db02656451a.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F364501%2F06a68e43-9bd2-403e-bb57-3db02656451a.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=9a80232be3b0372fa946bca599a27133)

色付きで表示され、自動的に apple-design-expert が使われました！

### 使われない時もある

ただし、プロンプトによっては使われないこともありました。

Sub agent が使われたプロンプト：

```text
> \`/coupons\` という、Udemyクーポン一覧ページのデザインを最適化してください
```

Sub agent 使われなかったプロンプト：

```text
> \`/coupons\` という、Udemyクーポン一覧ページのUI/UXを最適化してください
```

「デザイン」という言葉があるかどうかで変わっているのかもしれません。

AI が相手ですので確率の話ではありますが、確実に使いたい場合は明示的に Sub agents に合わせた指示を書くとよいでしょう。

## プロジェクト専用の専門家を育てよう

`/agents` コマンドから作られた Sub agents ファイルは、あくまで **汎用的な専門家** の視点を持っていました。

今回使ってみて感じたのは、Sub agents の本当の価値は「プロジェクトに特化した専門家を育てられる」ことです。

例えば私の Learning Next サイトの場合、アホほど専用ドキュメントを用意しています。

```bash
$ tree -L 2
.
├── knowledge # 外部ライブラリや特定の実装など
│   ├── blog-implementation-guide.md
│   ├── clerk_l10n.md
│   ├── clerk-auth-billing-complete-guide.md
│   ├── complete-ai-chat-implementation-guide.md
│   └── supabase-complete-guide.md
├── project # プロジェクト内の設計やドキュメントなど（さらに階層分け）
│   ├── architecture.md
│   ├── context.md
│   ├── core
│   ├── design
│   ├── patterns
│   ├── quick-start.md
│   ├── references
│   ├── strategy
│   ├── topics
│   └── workflow
├── settings.local.json
├── strategy # サイトの要件定義書など
│   ├── business-product-strategy.md
│   ├── execution-workflow.md
│   ├── README.md
│   ├── requirements
│   └── requirements-to-implementation.md
...
```

しかし、毎度毎度、指示のたびに個別のドキュメントを渡すのは結構面倒です。  
複数のドキュメントが絡むこともありますしね。

そこで、Webデザイナーとしての Sub agents は、以下のように関連ドキュメントを参照するようにカスタマイズしました。

```md
---
name: apple-design-expert
description: MUST BE USED. Learning Next専用のAppleデザインエキスパート。日本のプログラミング教育プラットフォームに特化したUI/UXデザインの作成・レビューを行います。Appleのデザイン原則と高級感のある美学、ユーザビリティ、Core Web Vitalsパフォーマンスに重点を置きます。これには、インターフェースの設計、デザイン実装のレビュー、既存デザインの改善提案、美的卓越性と技術的パフォーマンスのバランスを取ったデザインシステムの作成が含まれます。
---

# Learning Next Apple デザインエキスパート

あなたはLearning Nextプロジェクト専用のAppleインスパイアードデザインシステムエキスパートです。
日本のプログラミング教育プラットフォームの特殊性を理解し、高級感と実用性を両立したUI/UXを提供します。

## 📚 参照すべきデザインドキュメント

### 必須参照ドキュメント
- **\`.claude/project/references/design-system.md\`** - デザインシステムの全体像とガイド一覧
- **\`.claude/project/design/designer/visual-design.md\`** - ビジュアルデザイン仕様（カラー、タイポグラフィ、アイコン）
- **\`.claude/project/design/designer/component-guide.md\`** - Catalystコンポーネントの使用ガイド
- **\`.claude/project/design/ux-designer/interaction-design.md\`** - インタラクション設計とアニメーション

### 追加参照（必要に応じて）
- **\`docs-copilot/design-rules/LN_DESIGN_GUIDE.md\`** - 基本デザイン原則
- **\`docs-copilot/design-rules/LN_COMPONENT_GUIDE.md\`** - コンポーネント実装パターン
- **\`docs-copilot/design-rules/LN_IMPLEMENTATION_GUIDE.md\`** - 実装チェックリスト
- **\`docs-copilot/design-rules/LN_LAYOUT_INTERACTION_GUIDE.md\`** - レイアウト・インタラクション

## 🎨 Appleデザイン哲学（Learning Next適用版）

### 1. **明確性（Clarity）** - 学習効果重視
- 情報階層の明確化で学習進捗を分かりやすく表示
- コース・レッスン構造の直感的な表現
- プログラミング概念の視覚的説明サポート

### 2. **控えめさ（Deference）** - 学習内容を主役に
- UI要素は学習コンテンツを邪魔しない
- 集中力を維持する落ち着いたデザイン
- 不要な装飾を排除し、学習体験に集中

### 3. **奥行き（Depth）** - 情報の重要度を表現
- カード、モーダル、ドロップダウンでの視覚的階層
- 進捗状況やスコアの立体的表現
- ホバー効果での直感的フィードバック

### 4. **一貫性（Consistency）** - 学習パターンの定着
- 全画面で統一されたインタラクションパターン
- 一貫したスペーシング（8pxグリッド）とタイポグラフィ
- 予測可能なナビゲーション体験

### 5. **洗練性（Elegance）** - プロフェッショナル感の演出
- 信頼感を与える品質の高いビジュアル
- 学習者のモチベーション向上を支援
- エンジニアが「使いたくなる」上質なツール感

### プログラミング教育特有のUX

学習体験の最適化
- 長時間の学習でも疲れない色彩設計
- コードブロックの可読性重視
- エラー・成功フィードバックの教育的配慮

進捗可視化
- モチベーション維持のための達成感演出
- 学習の継続を促すビジュアル設計
- スキルアップの実感を与える表現

技術トピック表示
- Devicon統一サイズ（classNameでの上書き禁止）
- 技術スタック配色の統一（bg-zinc-100等）
- プログラミング言語・フレームワークの視覚的区別

## 🛠️ デザイン実装時の考え方

### プロジェクト固有の技術要件
- **技術スタック**: Next.js 15 + TypeScript + Catalyst UI + Tailwind CSS v4.1
- **パフォーマンス目標**: Core Web Vitals最適化（LCP < 2.5s、FID < 100ms、CLS < 0.1）
- **アクセシビリティ**: WCAG AA準拠、TouchTarget 44px以上
- **レスポンシブ**: モバイルファースト設計

### 実装パターンの参照先
具体的なコード実装例は以下のドキュメントを参照してください：
- **コンポーネント実装**: \`.claude/project/design/designer/component-guide.md\`
- **インタラクション実装**: \`.claude/project/design/ux-designer/interaction-design.md\`
- **パフォーマンス最適化**: \`.claude/framework/workflow/performance.md\`

## 📋 デザインプロセス

### 1. ドキュメント参照
まず上記の必須参照ドキュメントを確認し、プロジェクトのデザインシステムを理解する

### 2. コンテキスト分析
- 学習シナリオとユーザーの状況把握
- プログラミング教育での特殊要件確認
- 既存のLearning Nextデザインパターンとの整合性

### 3. デザイン提案
- Appleデザイン原則に基づいた設計
- Learning Next固有の要件への対応
- パフォーマンスとアクセシビリティの考慮

### 4. 品質確認
- デザインシステム準拠性の確認
- チェックリストに基づく品質評価
- 改善提案の具体化

**目標**: プログラミング学習者が「毎日使いたくなる」美しく機能的なデザインの実現
```

これで今後、デザインを調整するときには `apple-design-expert` を呼び出すだけでOKになります。

### こんな Sub agents があると便利そう

実際に使ってみて、こんな Sub agents があると便利だと思いました。

- **SEOスペシャリスト**: プロジェクト固有のSEO戦略を理解
- **パフォーマンス最適化エンジニア**: 特定のフレームワークに最適化
- **テストエンジニア**: プロジェクトのテスト規約に準拠

他にも事前にドキュメントを用意し、特定のライブラリやフレームワークに詳しい Sub agents を作ることで、より具体的で実用的なアドバイスがもらえそうです。

## まとめ

Claude Code の Sub agents 機能について、実際に使ってみた感想をまとめます。

**良かった点：**

- 役割分担が明確になり、指示が簡潔になった
- 視覚的に誰が作業しているか分かる
- 複数の専門家の視点が得られる

**工夫が必要な点：**

- 自動的に使われるには設定の調整（ `MUST BE USED` を追記）が必要
- プロンプトの書き方で使われたり使われなかったりする

**より使いこなすために：**

- プロジェクト内のドキュメントを参照させる
- より細かい観点で役割を分割する
- チェック時の実行スクリプトを組み込む

こういった工夫で、プロジェクトに最適化された Sub agents に育てていくことが重要です。

そうすることで、コンテキストウィンドウを効率的に使いながら、より専門的なサポートが受けられるようになります。

個人開発でも「専門家チームと一緒に開発している」ような体験ができるのは、なかなか面白いですね。

みなさんも、ぜひ自分のプロジェクトに特化した Sub agents を作ってみてください！

いくつか Sub agents のアイデアもあるので、作ったら別途記事にしたいと思います。

## Claude Code の関連記事

他にも Claude Code や AI 駆動開発の記事を書いていますので、よかったらこちらもご覧ください。

- [【Claude Code】マネできる！個人開発するときに最初に用意したドキュメント24種と機能要件書を全公開](https://qiita.com/items/e27292b65f723c4633d9)
- [【コピペOK】個人開発でApple風デザインルールを作ったら統一感のあるカッコいいUIにできた話](https://qiita.com/tomada/items/decece613046a61b11a3)
- [【Claude Code】指示書が肥大化してきたので観点別に指示を分けて開発効率を改善した話](https://qiita.com/items/cec7f1eb2a5eb83b91f2)
- [【テンプレ配布】Kiroの仕様駆動開発をClaude Codeで再現する方法！ドキュメント3点セットで実現](https://qiita.com/tomada/items/a67aa0739b95273bf487)

## ちょっと宣伝： Claude Code などの Udemy コース作ってます

Claude Code での Vibe Coding や Next.js、Ruby on Rails などの Udemy コースを開講しており、ありがたいことに **複数のベストセラー** をいただいてます。

[![image.png](https://qiita-image-store.s3.ap-northeast-1.amazonaws.com/0/364501/5c561ccf-623b-48ff-adc7-b708312102b9.png)](https://qiita-user-contents.imgix.net/https%3A%2F%2Fqiita-image-store.s3.ap-northeast-1.amazonaws.com%2F0%2F364501%2F5c561ccf-623b-48ff-adc7-b708312102b9.png?ixlib=rb-4.0.0&auto=format&gif-q=60&q=75&s=6c186be89a9614726cdbccdaa64923f8)

自社サイトでは **最低価格で受講できるクーポン** も配布しているので、よかったら見てやってください。

ご意見や、新講座の要望も大歓迎です！

[0](https://qiita.com/tomada/items/#comments)

コメント一覧へ移動

X（Twitter）でシェアする

Facebookでシェアする

はてなブックマークに追加する

この記事は以下の記事からリンクされています

- [Claude Code vs Codex CLI どっちを選ぶ？両方使ってきた経験から観点別にポイントを解説](https://qiita.com/tomada/items/c369d5f28142a2599a36)

## Qiita Conference 2025 Autumn 11月5日(水)~7日(金)開催！

![](https://cdn.qiita.com/assets/public/official_campaigns/qiita_conference_2025_autumn/image-conference_2025_autumn_ogp-d35d2500cd0420d93f2ed69a8d162d74.png)

Qiita Conferenceは、AI時代のエンジニアに贈るQiita最大規模のテックカンファレンスです！

基調講演ゲスト(敬称略)

piacere、牛尾 剛、Esteban Suarez、和田 卓人、seya、ミノ駆動、市谷 聡啓、からあげ、岩瀬 義昌、まつもとゆきひろ、みのるん、 Null-Sensei

[イベント詳細を見る](https://qiita.com/official-campaigns/conference/2025-autumn)

## Qiita Advent Calendar 開催！

[20](https://qiita.com/tomada/items/a6fba5876dbc9304ca5a/likers)

いいねしたユーザー一覧へ移動

16