---
title: "GitHub CLIが便利すぎる"
source: "https://zenn.dev/caru/articles/aa088436f69277"
author:
  - "[[Zenn]]"
published: 2024-10-20
created: 2025-11-11
description:
tags:
  - "clippings"
related:
---
42

12[tech](https://zenn.dev/tech-or-idea)

## はじめに

こんにちは。 [@caru\_ini](https://x.com/caru_ini) です。  
皆さんは、GitHubの公式コマンドラインツール「GitHub CLI」をご存知ですか？多くの開発者の方がGitHubのWebインターフェースを使用していると思いますが、GitHub CLIを活用することで、GitHubの操作を効率化できます。  
個人的に使っていたのですが、便利すぎて皆さんにこの便利さを伝えたい！ということで、本記事を書くことにしました。

本記事では、GitHub CLIの基本的な使い方から応用テクニックまでを紹介し、あなたの開発生産性を飛躍的に向上させる方法をお伝えします。

## なぜGitHub CLI?

GitHub CLIを使用すれば、例えば、以下のようなことができます！

1. コマンドラインから直接GitHubの操作ができるため、作業時間を短縮できる
2. 複雑な操作も簡単なコマンドで実行できるため、ミスを減らせる
3. 複数アカウントの運用が容易になる

それでは、GitHub CLIの詳細と使い方を見ていきましょう。

## GitHub CLIとは

GitHub CLIは、GitHubの操作をコマンドラインから行うためのツールです。Git統合やIssue/Pull Requestの操作など、GitHubの様々な操作をコマンドラインから行うことができます。

## インストール

各OSごとのインストール方法を紹介します。

### macOS

```bash
brew install gh
```

### Linux / BSD

手動でリポジトリをaptに追加してインストールします。  
(ちょっと長いですね...)

```bash
(type -p wget >/dev/null || (sudo apt update && sudo apt-get install wget -y)) \
 && sudo mkdir -p -m 755 /etc/apt/keyrings \
 && wget -qO- https://cli.github.com/packages/githubcli-archive-keyring.gpg | sudo tee /etc/apt/keyrings/githubcli-archive-keyring.gpg > /dev/null \
 && sudo chmod go+r /etc/apt/keyrings/githubcli-archive-keyring.gpg \
 && echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/githubcli-archive-keyring.gpg] https://cli.github.com/packages stable main" | sudo tee /etc/apt/sources.list.d/github-cli.list > /dev/null \
 && sudo apt update \
 && sudo apt install gh -y
```

### Windows

winget(パッケージマネージャー)を使用してインストールします。  
最近のWindows10/11は、デフォルトでインストールされていると思います。

```bash
winget install --id GitHub.cli
```

## ログイン

インストールが完了したら、まずはGitHubアカウントでログインしましょう。

`XXXX-XXXX` の形式でコードが表示されるので、ブラウザでそのコードを入力して認証します。

```bash
gh auth status
```

以下のように表示されればOKです。

```bash
✓ Logged in to github.com account <アカウント名> (/home/user/.config/gh/hosts.yml)
- Active account: true
- Git operations protocol: https
- Token: gho_************************************
- Token scopes: 'gist', 'read:org', 'repo', 'workflow'
```

## アカウントの切り替え

複数アカウントを持っている場合は、アカウントを追加して以下のコマンドで切り替えます。

```bash
gh auth switch
```

以下のように表示されます

```bash
✓ Switched active account for github.com to <アカウント名>
```

## 基本的な使い方

### リポジトリの作成

新しいリポジトリを作成するには、以下のコマンドを使用します。

```bash
gh repo create <リポジトリ名>
```

リポジトリ名を省略すると、対話式でリポジトリの作成ができます。

#### よく使うオプション

- `-c`, `--clone`: 作成と同時にクローン
- `--private`: プライベートリポジトリを作成
- `-p`, `--template`: リポジトリのテンプレートを指定

### リポジトリのクローン

既存のリポジトリをクローンするには、以下のコマンドを使用します。

```bash
gh repo clone <リポジトリ名 / URL> <ローカルパス>
```

このコマンドは基本的にGitのクローンと同じですが、リモートが設定された状態でクローンされるので便利です。ローカルパスを省略すると、リポジトリ名でディレクトリが作成されます。  
一見 `git clone` と同じように見えますが、GitHub上のリポジトリの場合、以下のように書くこともできますよ！

- `(ユーザー名 or Org名)/リポジトリ名`

```bash
gh repo clone caru-ini/test-repo
```

- リポジトリ名だけ(自分のリポジトリの場合)

```bash
gh repo clone test-repo
```

#### Gitコマンドの上書き

`--` より先のオプションをGitコマンドに渡すことができます。

```bash
gh repo clone <リポジトリ名 / URL> <ローカルパス> -- --depth=1
```

### Gitのセットアップ

GitHub CLIでGitをセットアップすることで、簡単にGitを使い始めることができます。通常sshキーなどで設定しますが、これだけでOKなので、非常に便利です。

```bash
gh auth setup-git
```

このコマンドを実行すると、グローバルの`.gitconfig` に以下の設定が追加されます。

```bash
[credential "https://github.com"]
        helper =
        helper = !/usr/bin/gh auth git-credential
[credential "https://gist.github.com"]
        helper =
        helper = !/usr/bin/gh auth git-credential
```

Gitにユーザー名とメールアドレスを設定していない場合は、追加しておきましょう。

### 開いているリポジトリのwebページを開く

GitのremoteにGitHubのリポジトリが設定されている場合は、以下のコマンドでリポジトリのwebページを開くことができます。

```bash
gh browse
```

個人的には、いちいちGitHubを開いてからリポジトリを探すのが面倒なので、このコマンドをよく使います！

### Issueの作成

新しいIssueを作成するには、以下のコマンドを使用します。

```bash
gh issue create
```

必須オプションの指定がない場合は、対話式でIssueを作成できます。

#### よく使うオプション

- `-t`, `--title`: タイトル
- `-b`, `--body`: 本文
- `-a`, `--assignee`: 担当者
- `-l`, `--label`: ラベル
- `-w`, `--web`: ブラウザでIssueを開く

#### 使用例

```bash
gh issue create -t "Bug Report" -b "This is a bug report" -a "caru-ini" -l "bug" -l "critical"
```

### Issueの一覧

Issueの一覧を表示するには、以下のコマンドを使用します。

```bash
gh issue list
```

このコマンドは、Issueの一覧を色付きで表示するので、非常に見やすいです。

#### よく使うオプション

- `-s`, `--state`: ステータス(open | closed | all)
- `-A`, `--author`: 作成者
- `-a`, `--assignee`: 担当者
- `-l`, `--label`: ラベル

### Pull Requestの作成

Pull Requestを作成するには、まず作成したいブランチに移動し、以下のコマンドを実行します。

```bash
gh pr create
```

必須オプションの指定がない場合は、対話式でPull Requestを作成できます。

#### よく使うオプション

- `-t`, `--title`: タイトル
- `-b`, `--body`: 本文
- `-a`, `--assignee`: 担当者
- `-l`, `--label`: ラベル
- `-d`, `--draft`: 下書きを作成

#### 使用例

```bash
gh pr create -t "Fix bug" -b "This is a bug fix" -a "caru-ini" -l "bug"
```

### Pull Requestの一覧

Pull Requestの一覧を表示するには、以下のコマンドを使用します。

```bash
gh pr list
```

### Pull Requestをチェックアウト

Pull Requestのブランチをチェックアウトする場合は、以下のコマンドを実行します。

```bash
gh pr checkout <Request番号>
```

## 拡張機能を使う

GitHub CLIの機能を拡張する方法も紹介します。

### GitHub Copilot

GitHub Copilotは、GitHub CLIでも使用できます。以下のコマンドでインストールできます。

```bash
gh extension install github/gh-copilot
```

インストール後は、 `gh copilot` コマンドが使えるようになります。

#### コマンドの説明

```bash
gh copilot explain <コマンド>
```

このコマンドは、指定したコマンドの説明をしてくれます。コマンドの指定がない場合、対話式で説明したいコマンドを入力できます。

#### コマンドの提案

```bash
gh copilot suggest <やりたいこと>
```

やりたいことを入力すると、そのコマンドを実行するためのコマンドを提案してくれます。最初にコマンドの種類を聞かれるので、適切なものを選択します。

- `generic shell`: 一般的なシェルコマンド
- `gh`: GitHub CLIのコマンド
- `git`: Gitのコマンド

## まとめ

最後まで読んでいただき、ありがとうございます！  
今回は、GitHub CLIの基本的な使い方とGitHub CLIの拡張機能のGitHub Copilotを紹介しました。Git操作だけでなく、GitHubもコマンドラインから操作できるのは、刺さる人には刺さるツールといえると思います。  
今回紹介した機能はGitHub CLIの一部に過ぎません。公式ドキュメントを参照して、さらに多くの便利な機能を探索してみてください！

## 参考

- [GitHub CLI公式ドキュメント](https://cli.github.com/)
- [GitHub Copilot拡張機能](https://github.com/github/gh-copilot)

## 宣伝

私は、大学でコンピューターサイエンスを学びながら、ReactやTypeScript、Next.jsなどのフロントエンド技術を使って、ゆる～く開発したり、ハッカソンに出たりしてます！  
お友達が少ないので、ぜひフォローしていただけると嬉しいです!

[GitHubで編集を提案](https://github.com/caru-ini/zenn-articles/blob/main/articles/aa088436f69277.md)

42

12

42

12