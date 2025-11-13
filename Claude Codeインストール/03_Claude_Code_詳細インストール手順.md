# Claude Code 詳細インストール手順書 (Windows + WSL)

このドキュメントは、Windows環境にWSL (Windows Subsystem for Linux) を導入し、ターミナル版のClaude Codeをインストールして、PowerShellから直接利用できるようにするための詳細な手順書です。

---

## 前提条件

*   Windows 10 または Windows 11
*   管理者権限でコマンドを実行できること
*   Cursor または VS Code がインストールされていること

---

## Step 1: WSL (Windows Subsystem for Linux) のインストール

Windows上でLinux環境を動かすための公式な仕組みであるWSLをインストールします。

1.  **PowerShellを管理者として開く**
    *   スタートメニューで「PowerShell」と検索し、表示された「Windows PowerShell」を右クリックして「管理者として実行」を選択します。

2.  **WSLインストールコマンドの実行**
    *   開いたPowerShellの画面で、以下のコマンドをコピー＆ペーストして実行します。
      ```powershell
      wsl --install
      ```
    *   インストールが完了すると、PCの再起動を求められます。指示に従ってPCを再起動してください。

3.  **Linux (Ubuntu) の初期設定**
    *   再起動後、自動的にUbuntuのセットアップウィンドウが開きます。（開かない場合は、スタートメニューから「Ubuntu」を探して起動してください）
    *   `Enter new UNIX username:` と表示されたら、Linux環境で使用したい **半角英数字のユーザー名** を入力してEnterキーを押します。
    *   `New password:` と表示されたら、Linux環境で使用する **パスワード** を入力してEnterキーを押します。（入力内容は画面に表示されませんが、正常に入力されています）
    *   `Retype new password:` と表示されたら、確認のためにもう一度同じパスワードを入力してEnterキーを押します。
    *   これでLinux環境の準備は完了です。一度このウィンドウは閉じて構いません。

---

## Step 2: 開発環境のセットアップ (Node.js)

Claude Codeは、JavaScriptの実行環境である **Node.js** 上で動作し、そのパッケージ管理ツールである **npm** を使ってインストールします。

ここでは、Node.jsの複数バージョンを管理できる **nvm (Node Version Manager)** というツールを使って、Node.jsとnpmを安全にインストールします。`nvm` を利用することで、システム全体に影響を与えず、パーミッション（権限）の問題を回避しながら開発環境を構築できるため推奨される方法です。

1.  **WSL (Ubuntu) を起動する**
    *   PowerShellを開き（管理者である必要はありません）、以下のコマンドでWSLを起動します。
      ```powershell
      wsl
      ```
    *   コマンドラインの表示が `PS C:\...>` から `username@hostname:~$` のようなLinuxの形式に変わります。これ以降のコマンドは、このLinux環境内で実行します。

2.  **パッケージ情報を更新し、`curl`をインストールする**
    *   `nvm`のインストールに必要な`curl`コマンドをインストールし、同時にシステムのソフトウェア情報を最新の状態にします。途中でパスワードを求められたら、Step 1-3で設定したパスワードを入力してください。
      ```bash
      sudo apt update && sudo apt install curl -y
      ```

3.  **nvm (Node Version Manager) をインストールする**
    *   以下のコマンドをコピー＆ペーストして実行し、`nvm`をインストールします。
      ```bash
      curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
      ```

4.  **nvmを有効にする**
    *   インストールした`nvm`コマンドをすぐに使えるようにするため、以下のコマンドを実行して設定を再読み込みします。
      ```bash
      source ~/.bashrc
      ```

5.  **Node.jsとnpmをインストールする**
    *   `nvm`を使って、推奨版（LTS - Long Term Support）のNode.jsをインストールします。npmはNode.jsに付属しているため、同時にインストールされます。
      ```bash
      nvm install --lts
      ```

6.  **インストールを確認する**
    *   以下のコマンドを実行し、それぞれバージョン番号（例: `v20.11.1` や `10.2.4`）が表示されれば、正しくインストールされています。
      ```bash
      node -v
      npm -v
      ```

---

## Step 3: Claude Code のインストール

準備が整ったので、Claude Code本体をインストールします。

1.  **Claude Codeインストールコマンドの実行**
    *   引き続きWSL (Ubuntu) のターミナルで、以下のコマンドを実行します。
      ```bash
      npm install -g @anthropic-ai/claude-code
      ```

2.  **インストールを確認する**
    *   以下のコマンドを実行して、Claude Codeのヘルプメッセージが表示されればインストール成功です。
      ```bash
      claude --help
      ```
    *   確認が終わったら、`exit` と入力してPowerShellの画面に戻ります。

---

## Step 4: PowerShellとの連携設定

WindowsのPowerShellから `claude` と入力するだけで、WSL内のClaude Codeを呼び出せるように設定します。

1.  **PowerShellプロファイルを開く**
    *   PowerShellの画面（WSLを`exit`した後の画面）で、以下のコマンドを実行します。これにより、設定ファイルがCursor (またはVS Code) で開きます。
      ```powershell
      code $PROFILE
      ```
    *   もしファイルが存在しないというエラーが出た場合は、先に `New-Item -Path $PROFILE -Type File -Force` を実行してから、再度 `code $PROFILE` を実行してください。

2.  **設定コードを追記する**
    *   開いた設定ファイル（`Microsoft.PowerShell_profile.ps1`）の末尾に、以下のコードをそのままコピー＆ペーストしてください。
      ```powershell
      function claude {
          wsl.exe -e bash -ic "claude $args"
      }
      ```

3.  **設定を保存して閉じる**
    *   ファイルを保存して、エディタを閉じてください。

4.  **PowerShellを再起動する**
    *   **重要**: 設定を反映させるため、現在開いているPowerShellウィンドウをすべて閉じ、新しいPowerShellウィンドウを開き直してください。

---

## Step 5: 利用開始

すべての設定が完了しました。

1.  **Claude Codeを起動する**
    *   新しく開いたPowerShellで、以下のコマンドを実行します。
      ```powershell
      claude
      ```
    *   Claudeのプロンプトが表示されれば成功です。

2.  **プロジェクトの初期化 (初回のみ)**
    *   分析したいプロジェクトのフォルダでPowerShellを開き、`claude` を起動した後、最初に一度だけ以下のコマンドを実行してプロジェクトをClaudeに認識させます。
      ```
      /init
      ```

以上で、いつでもPowerShellから `claude` コマンドでAIと対話できる環境が整いました。 