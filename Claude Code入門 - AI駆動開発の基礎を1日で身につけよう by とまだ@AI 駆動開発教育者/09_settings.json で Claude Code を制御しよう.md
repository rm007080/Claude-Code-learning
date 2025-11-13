---
title: "settings.json で Claude Code を制御しよう"
source: "https://zenn.dev/tmasuyama1114/books/claude_code_basic/viewer/memory_and_settings_settings_json"
author:
  - "[[Zenn]]"
published:
created: 2025-10-21
description:
tags:
  - "clippings"
related:
---
このチャプターの目次

1. [settings.json の役割を理解しよう](https://zenn.dev/tmasuyama1114/books/claude_code_basic/viewer/#settings.json-%E3%81%AE%E5%BD%B9%E5%89%B2%E3%82%92%E7%90%86%E8%A7%A3%E3%81%97%E3%82%88%E3%81%86)
2. [設定ファイルの優先順位](https://zenn.dev/tmasuyama1114/books/claude_code_basic/viewer/#%E8%A8%AD%E5%AE%9A%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E3%81%AE%E5%84%AA%E5%85%88%E9%A0%86%E4%BD%8D)
3. [基本的な構造を理解しよう](https://zenn.dev/tmasuyama1114/books/claude_code_basic/viewer/#%E5%9F%BA%E6%9C%AC%E7%9A%84%E3%81%AA%E6%A7%8B%E9%80%A0%E3%82%92%E7%90%86%E8%A7%A3%E3%81%97%E3%82%88%E3%81%86)
4. [権限設定（permissions）の仕組み](https://zenn.dev/tmasuyama1114/books/claude_code_basic/viewer/#%E6%A8%A9%E9%99%90%E8%A8%AD%E5%AE%9A%EF%BC%88permissions%EF%BC%89%E3%81%AE%E4%BB%95%E7%B5%84%E3%81%BF)
	1. [3つの判断パターン](https://zenn.dev/tmasuyama1114/books/claude_code_basic/viewer/#3%E3%81%A4%E3%81%AE%E5%88%A4%E6%96%AD%E3%83%91%E3%82%BF%E3%83%BC%E3%83%B3)
	2. [実践的な設定例](https://zenn.dev/tmasuyama1114/books/claude_code_basic/viewer/#%E5%AE%9F%E8%B7%B5%E7%9A%84%E3%81%AA%E8%A8%AD%E5%AE%9A%E4%BE%8B)
	3. [設定のポイント](https://zenn.dev/tmasuyama1114/books/claude_code_basic/viewer/#%E8%A8%AD%E5%AE%9A%E3%81%AE%E3%83%9D%E3%82%A4%E3%83%B3%E3%83%88)
5. [初心者向けの最小限設定](https://zenn.dev/tmasuyama1114/books/claude_code_basic/viewer/#%E5%88%9D%E5%BF%83%E8%80%85%E5%90%91%E3%81%91%E3%81%AE%E6%9C%80%E5%B0%8F%E9%99%90%E8%A8%AD%E5%AE%9A)
6. [その他の便利な設定](https://zenn.dev/tmasuyama1114/books/claude_code_basic/viewer/#%E3%81%9D%E3%81%AE%E4%BB%96%E3%81%AE%E4%BE%BF%E5%88%A9%E3%81%AA%E8%A8%AD%E5%AE%9A)
	1. [自動更新の設定](https://zenn.dev/tmasuyama1114/books/claude_code_basic/viewer/#%E8%87%AA%E5%8B%95%E6%9B%B4%E6%96%B0%E3%81%AE%E8%A8%AD%E5%AE%9A)
	2. [環境変数の設定](https://zenn.dev/tmasuyama1114/books/claude_code_basic/viewer/#%E7%92%B0%E5%A2%83%E5%A4%89%E6%95%B0%E3%81%AE%E8%A8%AD%E5%AE%9A)
7. [設定ファイルの作成と編集](https://zenn.dev/tmasuyama1114/books/claude_code_basic/viewer/#%E8%A8%AD%E5%AE%9A%E3%83%95%E3%82%A1%E3%82%A4%E3%83%AB%E3%81%AE%E4%BD%9C%E6%88%90%E3%81%A8%E7%B7%A8%E9%9B%86)
	1. [プロジェクト設定の作成](https://zenn.dev/tmasuyama1114/books/claude_code_basic/viewer/#%E3%83%97%E3%83%AD%E3%82%B8%E3%82%A7%E3%82%AF%E3%83%88%E8%A8%AD%E5%AE%9A%E3%81%AE%E4%BD%9C%E6%88%90)
	2. [グローバル設定の作成](https://zenn.dev/tmasuyama1114/books/claude_code_basic/viewer/#%E3%82%B0%E3%83%AD%E3%83%BC%E3%83%90%E3%83%AB%E8%A8%AD%E5%AE%9A%E3%81%AE%E4%BD%9C%E6%88%90)
8. [セキュリティのベストプラクティス](https://zenn.dev/tmasuyama1114/books/claude_code_basic/viewer/#%E3%82%BB%E3%82%AD%E3%83%A5%E3%83%AA%E3%83%86%E3%82%A3%E3%81%AE%E3%83%99%E3%82%B9%E3%83%88%E3%83%97%E3%83%A9%E3%82%AF%E3%83%86%E3%82%A3%E3%82%B9)
	1. [やってはいけない設定](https://zenn.dev/tmasuyama1114/books/claude_code_basic/viewer/#%E3%82%84%E3%81%A3%E3%81%A6%E3%81%AF%E3%81%84%E3%81%91%E3%81%AA%E3%81%84%E8%A8%AD%E5%AE%9A)
	2. [推奨する段階的アプローチ](https://zenn.dev/tmasuyama1114/books/claude_code_basic/viewer/#%E6%8E%A8%E5%A5%A8%E3%81%99%E3%82%8B%E6%AE%B5%E9%9A%8E%E7%9A%84%E3%82%A2%E3%83%97%E3%83%AD%E3%83%BC%E3%83%81)
9. [まとめ](https://zenn.dev/tmasuyama1114/books/claude_code_basic/viewer/#%E3%81%BE%E3%81%A8%E3%82%81)

Claude Code の動作を細かくコントロールできる仕組みがあります。

それが **settings.json** という設定ファイルです。

この Lesson では、settings.json を使った効果的な設定方法を学びます。

- [参考: settings.json で Claude Code のコマンドを制御しよう](https://qiita.com/tomada/items/45ae90809c714bb5c78e)

## settings.json の役割を理解しよう

settings.json は、Claude Code に対して **OK行動** と **NG行動** を伝えるためのファイルです。

子供にお使いを頼む時のルールに似ています。

- 「おつりでお菓子を買うのはOK」
- 「知らない人についていくのはダメ」
- 「1000円以内なら自分で判断していいよ」

settings.json も同じように動作します。

Claude Code に対して「これはやっていい」「これはダメ」「これは毎回聞いて」というルールを設定できるのです。

## 設定ファイルの優先順位

settings.json も CLAUDE.md と同じように、複数の場所に配置できます。

そして優先順位があります。

**優先順位（上が最優先）：**

1. **.claude/settings.local.json**
	- 個人用のプロジェクト設定（Git で無視）
	- 「自分だけの特別ルール」
2. **.claude/settings.json**
	- Git で管理したいプロジェクト設定
	- 「プロジェクト固有のルール」
3. **~/.claude/settings.json**
	- グローバル設定（全プロジェクト共通）
	- 「基本的なルール」

この順番で設定が読み込まれ、上位の設定が優先されます。

基本的には、2番目の `.claude/settings.json` から設定しましょう。

そして、色んなプロジェクトで使っているルールが増えてくれば、グローバル設定に切り出していきます。

## 基本的な構造を理解しよう

settings.json の基本構造を見てみましょう。

```json
{
  "permissions": {
    "allow": [
      // 自動的に許可するコマンド
    ],
    "deny": [
      // 絶対に実行しないコマンド
    ]
  }
}
```

この構造がすべての基本になります。

## 権限設定（permissions）の仕組み

### 3つの判断パターン

Claude Code がコマンドを実行する時の判断順序があります。

以下の順番で判断します。

1. **deny リストにある → 実行しない**
2. **allow リストにある → 自動実行**
3. **どちらにもない → ユーザーに確認**

信号機に例えるとわかりやすいかと思います。

- 赤信号（deny）：止まれ
- 青信号（allow）：進め
- 黄信号（どちらでもない）：注意して確認

### 実践的な設定例

React/Next.js プロジェクト向けの設定例を紹介します。

こちらは、Claude Code を使い倒している著者が実際に使っているルールです。

```json
{
  "permissions": {
    "allow": [
      "Bash(mkdir:*)",
      "Bash(touch:*)",
      "Bash(ls:*)",
      "Bash(tree:*)",
      "Bash(mv:*)",
      "Bash(cp:*)",
      "Bash(cat:*)",
      "Bash(grep:*)",
      "Bash(find:*)",
      "Bash(echo:*)",
      "Bash(do)",
      "Bash(then)",
      "Bash(else)",
      "Bash(fi)",
      "Bash(done)",
      "Bash(for:*)",
      "Bash(npm install:*)",
      "Bash(npm run dev:*)",
      "Bash(npm run build:*)",
      "Bash(npm run tsc:*)",
      "Bash(npm exec:*)",
      "Bash(npx:*)",
      "Bash(npm run test:*)",
      "Bash(npm run lint)",
      "Bash(npm run lint:*)",
      "Bash(npm run format:*)",
      "Bash(npm ls:*)",
      "Bash(curl:*)",
      "Bash(pkill:*)",
      "Bash(true)"
    ],
    "deny": [
      "Bash(sudo:*)",
      "Bash(git reset:*)",
      "Bash(git rebase:*)",
      "Read(.env:*)",
      "Read(id_rsa)",
      "Read(id_ed25519)",
      "Read(**/*token*)",
      "Read(**/*key*)",
      "Write(.env:*)",
      "Write(**/secrets/**)",
      "Bash(wget:*)"
    ]
  }
}
```

この設定では、開発中で必須となるファイルの閲覧や操作を許可しています。

一方で、破壊的な変更のコマンドは禁止しています。

機密情報の読み取りやインターネット上からのファイルダウンロードなど、セキュリティ上の問題がある操作も deny で禁止しています。

#### 補足： rm コマンドについて

ファイルなどの削除を行える `rm` については allow にも deny にも設定していません。

その場合、Claude Code が実行しようとするタイミングで許可を求めてくれます。

実行内容を確認してから Yes を選択し、許可を出しましょう。

ファイルやフォルダの削除も常時許可する場合は、以下の内容を allow の欄に追記します。

```json
"Bash(rm:*)",
```

### 設定のポイント

設定する際の重要なポイントを2つ紹介します。

**1\. ワイルドカード（\*）の使い方**

```json
"Bash(npm run test:*)"  // npm run test で始まるすべてのコマンド
"Read(**/*.env)"        // 任意の階層の .env ファイル
```

**2\. 安全性と利便性のバランス**

バランスを考えて設定することが大切です。

- よく使う安全なコマンドは allow に
- 破壊的な操作は deny に
- 判断が必要なものは指定しない

## 初心者向けの最小限設定

最初はシンプルな設定から始めましょう。

たとえば、フロントエンドプロジェクトであれば以下のようなところからスタートします。

```json
{
  "permissions": {
    "allow": [
      "Bash(ls:*)",
      "Bash(cat:*)",
      "Bash(npm run dev)",
      "Bash(npm run build)",
      "Bash(npm test)"
    ],
    "deny": [
      "Bash(rm -rf:*)",
      "Bash(sudo:*)",
      "Read(.env)"
    ]
  }
}
```

これで基本的な開発作業はスムーズに進められます。

慣れてきたら徐々に設定を追加していきましょう。

## その他の便利な設定

### 自動更新の設定

Claude Code の自動更新を有効化する設定です。

```json
{
  "autoUpdates": true  // Claude Code の自動更新を有効化
}
```

### 環境変数の設定

プロジェクトで使用する環境変数を設定できます。

```json
{
  "env": {
    "NODE_ENV": "development",
    "NEXT_TELEMETRY_DISABLED": "1"
  }
}
```

## 設定ファイルの作成と編集

### プロジェクト設定の作成

プロジェクト用の設定ファイルを作成する手順です。

```json
# .claude ディレクトリを作成
mkdir -p .claude

# エディタで開く
code .claude/settings.json
```

### グローバル設定の作成

全プロジェクト共通の設定を作成する手順です。

```json
# ホームディレクトリの設定
mkdir -p ~/.claude
code ~/.claude/settings.json
```

## セキュリティのベストプラクティス

### やってはいけない設定

以下のような設定は危険です。

```json
// 危険！すべてを許可
{
  "permissions": {
    "allow": ["*"]
  }
}
```

すべてを許可するのは避けましょう。

最悪、すべてのファイルを削除したり、危険なファイルをインターネット上からダウンロードも出来てしまいます。

### 推奨する段階的アプローチ

安全に設定を構築する方法として、以下のような考え方で設定ファイルを育てていきましょう。

1. **最初は最小限の allow から始める**
2. **作業しながら必要なものを追加**
3. **定期的に見直して不要なものを削除**

この順番で進めることで、安全性を保ちながら設定を育てられます。

## まとめ

settings.json を使いこなすことで、Claude Code との作業が効率的になります。

今回学んだ重要なポイントをおさらいしておきます。

- **3つの判断パターン** （deny → allow → 確認）
- **安全性と利便性のバランス** を考える
- **段階的に設定を育てる**