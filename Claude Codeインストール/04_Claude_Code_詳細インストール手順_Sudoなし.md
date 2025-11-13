# Claude Code 詳細インストール手順書 (Windows + WSL, sudo不要版)

このドキュメントは、管理者権限 (`sudo`) を使用せずに、Windows環境にWSLを導入し、ターミナル版のClaude Codeをインストールするための詳細な手順書です。

この方法は、システムのコア部分に変更を加えないため、より安全です。WSLの初期環境に`curl`コマンドがプリインストールされていることを前提としています（近年のバージョンでは標準です）。

---

## 前提条件

*   Windows 10 または Windows 11
*   管理者権限でコマンドを実行できること（最初のWSLインストール時のみ）
*   Cursor または VS Code がインストールされていること

---

## Step 1: WSL (Windows Subsystem for Linux) のインストール

Windows上でLinux環境を動かすためのWSLをインストールします。このステップのみWindowsの管理者権限が必要です。

1.  **PowerShellを管理者として開く**
    *   スタートメニューで「PowerShell」と検索し、表示された「Windows PowerShell」を右クリックして「管理者として実行」を選択します。

2.  **WSLインストールコマンドの実行**
    *   開いたPowerShellの画面で、以下のコマンドを実行します。
      ```powershell
      wsl --install
      ```
    *   インストールが完了すると、PCの再起動を求められます。指示に従ってPCを再起動してください。

3.  **Linux (Ubuntu) の初期設定**
    *   再起動後、自動的にUbuntuのセットアップウィンドウが開きます。（開かない場合は、スタートメニューから「Ubuntu」を探して起動してください）
    *   `Enter new UNIX username:` と表示されたら、Linux環境で使用したい **半角英数字のユーザー名** を入力してEnterキーを押します。
    *   `New password:` と表示されたら、Linux環境で使用する **パスワード** を入力してEnterキーを押します。（入力内容は画面に表示されません）
    *   `Retype new password:` と表示されたら、確認のためにもう一度同じパスワードを入力してEnterキーを押します。
    *   これでLinux環境の準備は完了です。一度このウィンドウは閉じて構いません。

---

## Step 2: 開発環境のセットアップ (Node.js, `sudo`不要)

`sudo`を使わずに、ユーザーのホームディレクトリ内に安全に開発環境を構築します。

1.  **WSL (Ubuntu) を起動する**
    *   PowerShellを開き（管理者である必要はありません）、以下のコマンドでWSLを起動します。
      ```powershell
      wsl
      ```
    *   コマンドラインの表示が `PS C:\...>` から `username@hostname:~$` のようなLinuxの形式に変わります。

2.  **nvm (Node Version Manager) をインストールする**
    *   `nvm`のインストーラをダウンロードして実行します。`curl`コマンドが見つからないというエラーが出た場合にのみ、[トラブルシューティング](#トラブルシューティング)を参照してください。
      ```bash
      curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
      ```

3.  **nvmを有効にする**
    *   インストールした`nvm`コマンドを現在のターミナルで使えるようにするため、設定を再読み込みします。
      ```bash
      source ~/.bashrc
      ```

4.  **Node.jsとnpmをインストールする**
    *   `nvm`を使って、推奨版（LTS - Long Term Support）のNode.jsをインストールします。`npm`はNode.jsに付属しています。このコマンドはユーザーの権限で安全に実行されます。
      ```bash
      nvm install --lts
      ```

5.  **インストールを確認する**
    *   以下のコマンドを実行し、それぞれバージョン番号が表示されれば成功です。
      ```bash
      node -v
      npm -v
      ```

---

## Step 3: Claude Code のインストール

`nvm`で環境構築した場合、グローバルなインストールも`sudo`なしで安全に実行できます。

1.  **Claude Codeインストールコマンドの実行**
    *   引き続きWSL (Ubuntu) のターミナルで、以下のコマンドを実行します。
      ```bash
      npm install -g @anthropic-ai/claude-code
      ```

2.  **インストールを確認する**
    *   以下のコマンドを実行して、ヘルプメッセージが表示されれば成功です。
      ```bash
      claude --help
      ```
    *   確認が終わったら、`exit` と入力してPowerShellの画面に戻ります。

---

## Step 4: PowerShellとの連携設定

このステップは `sudo` を使わない手順でも全く同じです。

1.  **PowerShellプロファイルを開く**
    *   PowerShellの画面で、以下のコマンドを実行して設定ファイルをエディタで開きます。
      ```powershell
      code $PROFILE
      ```
    *   もしファイルが存在しない場合は、先に `New-Item -Path $PROFILE -Type File -Force` を実行してから、再度 `code $PROFILE` を実行してください。

2.  **設定コードを追記する**
    *   開いた設定ファイルの末尾に、以下のコードをコピー＆ペーストします。
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

1.  **Claude Codeを起動する**
    *   新しく開いたPowerShellで、`claude` と入力して起動します。

2.  **プロジェクトの初期化 (初回のみ)**
    *   分析したいプロジェクトのフォルダで`claude`を起動した後、`/init` コマンドを一度だけ実行します。

---

## トラブルシューティング

### `curl: command not found` というエラーが出る場合

万が一、お使いのWSL環境に`curl`がインストールされていない場合は、残念ながら`curl`のインストールに一度だけ`sudo`を使用する必要があります。

WSLターミナルで以下のコマンドを実行し、パスワードを入力してください。これが完了すれば、以降のステップは`sudo`なしで進められます。

```bash
sudo apt update && sudo apt install curl -y
``` 