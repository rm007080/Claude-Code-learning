---
title: "Claude Code のインストールと初期設定｜Claude Code入門 - AI駆動開発の基礎を1日で身につけよう"
source: "https://zenn.dev/tmasuyama1114/books/claude_code_basic/viewer/introduction_installation_and_first_steps"
author:
  - "[[Zenn]]"
published:
created: 2025-10-21
description:
tags:
  - "clippings"
related:
---
Claude Code を使い始めるには、まず開発環境の準備とインストールが必要です。

本記事では、各OS別のインストール手順と、最初のコマンドを実行するまでの流れを解説します。  
手順通りに進めれば、確実に Claude Code を動かせるようになります。

## 必要な環境と前提条件

Claude Code を使用するには、以下の環境が必要です。

### 対応OS

- **macOS**: 10.15以上
- **Windows**: 10以上（2025年7月以降はWSL不要）
- **Linux**: Ubuntu 20.04以上

### 必要なソフトウェア

- **Node.js**: バージョン18以上（推奨：22 LTS）
- **npm**: Node.js に付属

### Anthropic アカウント

- Proプラン（月額$20）またはMaxプラン（月額$100）を推奨
- 無料アカウントでも利用可能（APIキー利用のため従量課金）

### その他

- インターネット接続（認証とAI処理に必須）
- ターミナルの基本操作の知識

## Node.js のインストール

Claude Code は Node.js 上で動作するため、最初に Node.js をインストールします。

### macOS の場合

Homebrew を使用してインストールします。

```bash
# Homebrewがインストールされていない場合
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"

# Node.jsをインストール
brew install node

# バージョン確認（18以上であることを確認）
node --version
```

### Windows の場合

Windowsでは、2025年7月以降はWSLを使わずに直接インストールが可能です。

#### 方法1: 直接インストール（推奨）

**手順1: Node.jsのインストール**

1. [Node.js公式サイト](https://nodejs.org/) から Windows Installer をダウンロード
2. LTS版（22.x）を選択してインストール
3. インストール中は全てデフォルト設定でOK

**手順2: インストール確認**

コマンドプロンプトを開いて以下を実行：

```bash
node --version
npm --version
```

#### 方法2: WSL経由でのインストール（上級者向け）

より Linux に近い環境で開発したい場合は、WSL を使用することもできます。

**手順1: WSLのインストール**

PowerShell を管理者権限で開き、以下を実行します。

```powershell
# WSL2とUbuntu 22.04を同時にインストール
wsl --install -d Ubuntu-22.04
```

インストール完了後、PCを再起動してください。

**手順2: Ubuntuの初期設定**

1. 再起動後、「Ubuntu 22.04」が自動的に起動します
2. 起動しない場合は、スタートメニューから「Ubuntu」を検索して起動
3. ユーザー名とパスワードの設定を求められるので入力
	- ユーザー名：英数字のみ推奨
	- パスワード：入力時は表示されません

**手順3: Node.jsのインストール（nvm推奨）**

Ubuntu ターミナル内で以下を実行します。

```bash
# パッケージリストを更新
sudo apt update && sudo apt upgrade -y

# nvmをインストール（Node.jsのバージョン管理ツール）
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash

# 設定を反映
source ~/.bashrc

# Node.js 22 LTSをインストール
nvm install 22
nvm use 22
nvm alias default 22

# バージョン確認
node --version
npm --version
```

## Claude Code のインストール

Node.js の準備ができたら、Claude Code をインストールします。

```bash
npm install -g @anthropic-ai/claude-code
```

### インストール時のトラブルシューティング

**権限エラーが発生した場合**

グローバルインストールの権限がない場合は、npm の設定を変更します。

```bash
# npmグローバルディレクトリを作成
mkdir -p ~/.npm-global

# npmの設定を変更
npm config set prefix ~/.npm-global

# パスを追加（bashの場合）
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc

# パスを追加（zshの場合）
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.zshrc
source ~/.zshrc

# 再度インストール
npm install -g @anthropic-ai/claude-code
```

**インストール確認**

以下のコマンドでバージョンが表示されれば成功です。

```bash
claude --version
#=> 例: 1.0.67 (Claude Code)
```

## 初回起動と認証設定

### Claude Code の起動

作業用ディレクトリを作成し、Claude Code を起動します。

```bash
# ホームディレクトリに移動
cd ~

# 作業用ディレクトリを作成
mkdir my-claude-project
cd my-claude-project

# Claude Codeを起動
claude
```

### 表示モードの選択

起動すると、以下のように表示されるかと思います。

```bash
╭──────────────────────────╮
│ ✻ Welcome to Claude Code │
╰──────────────────────────╯

 Let's get started.

 Choose the text style that looks best with your terminal:
 To change this later, run /theme

 ❯ 1. Dark mode✔
   2. Light mode
   3. Dark mode (colorblind-friendly)
   4. Light mode (colorblind-friendly)
   5. Dark mode (ANSI colors only)
   6. Light mode (ANSI colors only)

 Preview
 ╭──────────────────────────────────────────────────────────────────────────────────────────────────╮
 │   1   function greet() {                                                                         │
 │   2 -    console.log("Hello, World!");                                                           │
 │   2 +    console.log("Hello, Claude!");                                                          │
 │   3   }                                                                                          │
 ╰──────────────────────────────────────────────────────────────────────────────────────────────────╯
```

特にこだわりがなければデフォルトの Dark mode が選択された状態で Enter を押します。

### 利用アカウントの設定

次に、以下のような画面が表示されるかと思います。

![ClaudeCodeセットアップ](https://res.cloudinary.com/zenn/image/fetch/s--BuK_fpY0--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/995daff0d000450360a5728f.png%3Fsha%3Dfe35ccc8f6d6691764b8c2d0334e0e456b7fe984)

大事なのは、以下の選択肢の部分です。

初回起動時に認証方法を選択することになります。

1. **Claude App（ProまたはMaxプラン）**
	- Claude.ai のアカウントでログイン
	- 初心者におすすめ
	- **定額**
2. **Anthropic Console（APIキー方式）**
	- Anthropic Console でAPIキーを発行して利用
	- 開発者向けの詳細な利用が可能
	- **従量課金**

デフォルトでは、Claude のサブスクリプションを利用する方法が選択されています。

多くの方はこちらかと思いますので、そのまま Enter を選択します。

すると、ブラウザで以下のように表示されますので **承認する** を選択します。

![ClaudeCodeログイン](https://res.cloudinary.com/zenn/image/fetch/s--P2ufr_-t--/c_limit%2Cf_auto%2Cfl_progressive%2Cq_auto%2Cw_1200/https://storage.googleapis.com/zenn-user-upload/deployed-images/a1197a84d3ab9fa9cf4e1149.png%3Fsha%3D22be6ee84a53ca5d518be387b4b37d5e28e16d4b)

なお、WSL を利用している場合は承認後にコードが表示されるかと思いますので、そちらを Claude Code 側に入力してあげれば認証が完了します。

最初にセキュリティ上の注意点（AI なので間違いやリスクもあり得る旨）が表示されますので、読んでから Enter を押します。

これで、Claude のサブスクリプションを使って、Claude Code が定額で使い放題となりました。

## ターミナルセットアップ

利用しているOSや環境によりますが、以下のように表示される場合があります。

こちらは Mac を使用している場合の表示です。

1 番を選択して Yes を押すと、今後 Shift + Enter キーで改行しながら入力できるので、おすすめです。

## 基本的な使い方

認証が完了すると、Claude Code との対話が始まります。

以下のように表示されれば、プロンプト（Claude Code への指示）の入力待ち状態です。

```bash
╭────────────────────────────────────────────────────────────────────────────────────────────────────╮
│ > Try "fix typecheck errors"                                                                       │
╰────────────────────────────────────────────────────────────────────────────────────────────────────╯
  ? for shortcuts
```

### プロジェクトの初期化

最初に `/init` コマンドでプロジェクトを初期化します。

```bash
> /init
```

このコマンドにより、 `CLAUDE.md` ファイルが生成されます。  
このファイルには、プロジェクトの構造や規約が記録されます。

空のプロジェクトで起動すれば特に書くことはありませんが、既存のプロジェクトであれば色々と追記されます。

詳しくは後のレッスンで学習します。

### 補足：コマンド入力例について

なお、今後カリキュラムの中では、以下のように `>` をつけて入力コマンドやプロンプトを示すことがあります。

```bash
> /init
```

ここで `>` というのは、Claude Code の入力欄で常に表示されている記号を表しています。  
そのため、ここで実際に入力するのは `/init` という文字だけであることに注意してください。

### 基本的な操作例

では、試しに軽く使ってみましょう。

例えば、以下のような内容を試してみるといいでしょう。

**HTMLファイルの作成**

```bash
> 簡単なHTMLファイルを作成してください
```

初回実行時、特に VS Code のターミナルで実行している時は以下のように表示されるかと思います。

```bash
╭────────────────────────────────────────────────────────────────────────────────────────────────────╮
│                                                                                                    │
│  Opened changes in Visual Studio Code ⧉                                                            │
│  Save file to continue…                                                                            │
│                                                                                                    │
│ Do you want to make this edit to index.html?                                                       │
│ ❯ 1. Yes                                                                                           │
│   2. Yes, and don't ask again this session (shift+tab)                                             │
│   3. No, and tell Claude what to do differently (esc)                                              │
│                                                                                                    │
╰────────────────────────────────────────────────────────────────────────────────────────────────────╯
```

これはファイル編集をして良いかどうか聞かれています。

さらに、それぞれの選択肢の意味は以下のようになっています。

- 1番の選択肢：今回だけ許可
- 2番の選択肢：常に許可
- 3番の選択肢：拒否

基本的には2番の選択肢に十字キーを使って合わせ、Enter を押してあげればOKです。

これで実際に作られたファイルは以下のようなものでした。  
Claude Code を起動した（ `claude` コマンドを実行した）フォルダの中に、 `index.html` が作られています。

```html
<!DOCTYPE html>
<html lang="ja">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>シンプルなHTMLページ</title>
</head>
<body>
    <h1>ようこそ！</h1>
    <p>これは簡単なHTMLファイルです。</p>
    <ul>
        <li>項目1</li>
        <li>項目2</li>
        <li>項目3</li>
    </ul>
</body>
</html>
```

## よく使うコマンド一覧

Claude Code には便利なスラッシュコマンドが用意されています。

詳しくは後ほど学んでいきましょう。

| コマンド | 説明 |
| --- | --- |
| `/init` | プロジェクトを分析してCLAUDE.mdを生成 |
| `/clear` | 会話履歴をクリア |
| `/help` | ヘルプを表示 |
| `/exit` | Claude Codeを終了（Ctrl+Cでも可） |

## 環境別の注意点

### macOS

- ターミナルは標準のTerminal.appかiTermを推奨
- zsh がデフォルトシェルなので、設定ファイルは `.zshrc`

### Windows (WSL)

- Windows Terminal の使用を推奨
- ファイルパスの違いに注意（ `/mnt/c/` が C ドライブ）
- WSL2 の使用を推奨（パフォーマンスが向上）

## トラブルシューティング

パスが通っていない可能性があります。

```bash
# 現在のパスを確認
echo $PATH

# npmのインストール先を確認
npm config get prefix

# パスを追加（例：bashの場合）
echo 'export PATH="$PATH:$(npm config get prefix)/bin"' >> ~/.bashrc
source ~/.bashrc
```

### WSLでnpmが見つからない

WSL を再起動し、Node.js を再インストールしてください。

```bash
# WSLを再起動（PowerShellから）
wsl --shutdown
wsl

# Node.jsを再インストール
sudo apt update
sudo apt install nodejs npm
```

### 認証エラー

以下を確認してください。

- ブラウザのポップアップブロッカーを無効化
- ファイアウォールやプロキシの設定
- 別のブラウザで試す

## まとめ

Claude Code のインストールと初期設定が完了しました。

基本的なコマンドも実行できるようになったので、次回はより実践的な使い方を学んでいきます。

### 参考資料

- [Claude Code 公式セットアップガイド](https://docs.anthropic.com/ja/docs/claude-code/setup)
- [Node.js 公式サイト](https://nodejs.org/ja)
- [WSL のインストール（Microsoft公式）](https://learn.microsoft.com/ja-jp/windows/wsl/install)