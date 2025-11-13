# Windows WSL環境でのインストール

## はじめに

この章では、Windows環境でClaude Codeを使用するための詳細な手順を解説します。2025年7月以降はWSL不要で直接インストールできますが、より安定した開発環境を構築したい場合は、WSL（Windows Subsystem for Linux）の使用も有効です。両方の方法を解説します。

## インストール方法の選択

### 方法1: 直接インストール（推奨）

**メリット**
- セットアップが簡単
- Windowsネイティブで動作
- PowerShellから直接使用可能

**デメリット**
- 一部のツールでLinux環境を前提とする場合がある
- 開発環境としてはWSLの方が柔軟

**向いている人**
- 初心者
- シンプルな環境で使いたい
- Windowsメインの開発

**注**: Windows向けのネイティブインストールは、Git Bashやcurlツールが必要です。

### 方法2: WSL経由（全バージョン対応）

**メリット**
- Linux環境で動作（開発者に人気）
- 多くの開発ツールと互換性が高い
- より本格的な開発環境

**デメリット**
- 初期セットアップがやや複雑
- WSLの基礎知識が必要

**向いている人**
- より本格的な開発環境を構築したい
- LinuxやmacOSからの移行
- チーム開発でLinux環境を使用

それぞれの詳細手順を見ていきましょう。

---

## 方法1: Windows直接インストール（2025年7月以降）

### Step 1: Node.jsのインストール

**1-1. Node.js公式サイトにアクセス**

ブラウザで以下のURLを開きます:
```
https://nodejs.org/
```

**1-2. インストーラーのダウンロード**

- 「LTS（推奨版）」の「Windows Installer (.msi)」をクリック
- 通常は64-bit版を選択
- ファイルがダウンロードされるまで待つ

**1-3. インストーラーの実行**

1. ダウンロードした`.msi`ファイルをダブルクリック
2. 「Next」をクリック
3. ライセンス同意にチェックを入れて「Next」
4. インストール先はデフォルトのままで「Next」
5. カスタム設定もデフォルトのままで「Next」
6. 「Automatically install the necessary tools...」にチェック（推奨）
7. 「Install」をクリック
8. 管理者権限の確認が出たら「はい」

**1-4. インストールの確認**

PowerShellまたはコマンドプロンプトを**新しく開いて**以下を実行:

```powershell
node --version
```

```powershell
v22.5.1
```
のようにバージョンが表示されればOK

```powershell
npm --version
```

```powershell
10.2.4
```
のようにバージョンが表示されればOK

**Note**: インストール前に開いていたターミナルでは反映されません。必ず新しく開き直してください。

### Step 2: Claude Codeのインストール

**2-1. PowerShellを開く**

- スタートメニューで「PowerShell」と検索
- 「Windows PowerShell」をクリック（管理者権限は不要）

**2-2. Claude Codeのインストール**

**方法A: ネイティブインストール（推奨）**

公式推奨の方法です:

```powershell
# Git Bash または WSL内で実行
curl -fsSL https://claude.ai/install.sh | bash
```

**方法B: npmインストール**

Node.jsが既にインストールされている場合:

```powershell
npm install -g @anthropic-ai/claude-code
```

**注意**: sudoは使用しないでください（権限の問題が発生する可能性があります）

インストールには数分かかることがあります。以下のようなログが表示されます:

```
added 150 packages in 45s
```

**2-3. インストールの確認**

```powershell
claude --version
```

```
1.0.67 (Claude Code)
```
のようにバージョンが表示されればインストール成功！

### Step 3: 初回起動と認証

**3-1. Claude Codeを起動**

```powershell
claude
```

**3-2. テーマの選択**

初回起動時、以下のようなテーマ選択画面が表示されます:

```
Choose the text style that looks best with your terminal:

❯ 1. Dark mode
  2. Light mode
  3. Dark mode (colorblind-friendly)
  4. Light mode (colorblind-friendly)
  5. Dark mode (ANSI colors only)
  6. Light mode (ANSI colors only)
```

十字キーで選択し、Enterを押します。おすすめは「1. Dark mode」。

**3-3. 認証方法の選択**

```
How would you like to authenticate?

❯ 1. Claude App (Pro or Max subscription)
  2. Anthropic Console (API key)
```

- **Claude AppのProまたはMaxプランを使う場合**: 1を選択
- **APIキーを使う場合**: 2を選択

初心者の方は「1」を選択することをおすすめします。

**3-4. ブラウザでの認証**

1を選択した場合、自動的にブラウザが開きます:

1. Claude.aiのログイン画面が表示される
2. メールアドレスとパスワードでログイン
3. 「承認する」をクリック
4. PowerShellに戻ると認証完了

**これで準備完了です！**

---

## 方法2: WSL経由インストール（全バージョン対応）

より本格的な開発環境を構築する方法です。

### Step 1: WSLのインストール

**1-1. PowerShellを管理者として開く**

1. スタートメニューで「PowerShell」と検索
2. 「Windows PowerShell」を**右クリック**
3. 「管理者として実行」を選択

**1-2. WSLインストールコマンドの実行**

管理者PowerShellで以下を実行:

```powershell
wsl --install
```

または、特定のディストリビューション（推奨: Ubuntu 22.04）を指定:

```powershell
wsl --install -d Ubuntu-22.04
```

実行すると以下のようなメッセージが表示されます:

```
インストール中: 仮想マシン プラットフォーム
インストール中: Linux 用 Windows サブシステム
インストール中: Ubuntu 22.04
要求された操作は正常に終了しました。変更を有効にするには、システムを再起動する必要があります。
```

**1-3. PCの再起動**

指示に従ってPCを再起動してください。

**1-4. Ubuntuの初期設定**

再起動後、自動的に「Ubuntu」ウィンドウが開きます。開かない場合は:

1. スタートメニューで「Ubuntu」と検索
2. 「Ubuntu 22.04」をクリック

初回起動時、以下の設定を求められます:

```
Enter new UNIX username:
```

- 半角英数字でユーザー名を入力（例: `user123`）
- Enterキーを押す

```
New password:
```

- パスワードを入力（**画面には表示されません**）
- Enterキーを押す

```
Retype new password:
```

- 同じパスワードをもう一度入力
- Enterキーを押す

セットアップが完了すると、以下のような画面になります:

```
username@hostname:~$
```

**これでLinux環境の準備完了です。**

### Step 2: Node.jsのインストール（WSL内）

**2-1. パッケージリストの更新**

WSL（Ubuntuターミナル）内で以下を実行:

```bash
sudo apt update && sudo apt upgrade -y
```

初回は時間がかかる場合があります（5-10分程度）。

パスワードを求められたら、Step 1-4で設定したパスワードを入力してください。

**2-2. 必要なツールのインストール**

```bash
sudo apt install curl -y
```

**2-3. nvm（Node Version Manager）のインストール**

nvmを使うと、Node.jsのバージョン管理が簡単になります:

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.7/install.sh | bash
```

**2-4. nvmの有効化**

インストール後、設定を反映させます:

```bash
source ~/.bashrc
```

**2-5. nvmが使えることを確認**

```bash
nvm --version
```

```
0.39.7
```
のようにバージョンが表示されればOK。

**2-6. Node.jsのインストール**

LTS版（推奨版）をインストール:

```bash
nvm install --lts
```

または、特定のバージョンを指定:

```bash
nvm install 22
nvm use 22
nvm alias default 22
```

**2-7. インストールの確認**

```bash
node --version
```

```
v22.5.1
```

```bash
npm --version
```

```
10.2.4
```

両方でバージョンが表示されればOK！

### Step 3: Claude Codeのインストール（WSL内）

**3-1. Claude Codeのインストール**

**方法A: ネイティブインストール（推奨）**

```bash
curl -fsSL https://claude.ai/install.sh | bash
```

**方法B: npmインストール**

```bash
npm install -g @anthropic-ai/claude-code
```

**注意**: sudoは使用しないでください

**3-2. インストールの確認**

```bash
claude --version
```

```
1.0.67 (Claude Code)
```

**3-3. 初回起動**

```bash
claude
```

初回起動時の設定は、方法1と同じです（テーマ選択、認証など）。

**3-4. WSLでの認証について**

WSL環境では、ブラウザ認証後に**コード**が表示されることがあります:

1. ブラウザで認証完了
2. コードが表示される（例: `ABC123DEF456`）
3. WSLターミナルに戻る
4. 「Enter the code:」と表示されたらコードを入力
5. Enterキーで完了

### Step 4: PowerShellからWSL版Claude Codeを呼び出す（オプション）

WindowsのPowerShellから直接`claude`コマンドを使えるようにします。

**4-1. PowerShellプロファイルを開く**

PowerShell（管理者権限不要）で:

```powershell
code $PROFILE
```

ファイルが存在しない場合:

```powershell
New-Item -Path $PROFILE -Type File -Force
code $PROFILE
```

**4-2. 関数を追加**

開いたファイルに以下を追記:

```powershell
function claude {
    wsl.exe -e bash -ic "claude $args"
}
```

保存して閉じます（Ctrl+S → ファイルを閉じる）。

**4-3. PowerShellを再起動**

現在のPowerShellを全て閉じて、新しく開き直します。

**4-4. 動作確認**

```powershell
claude --version
```

WSL内のClaude Codeのバージョンが表示されればOK！

これで、**PowerShellから直接`claude`コマンドが使える**ようになりました。

---

## Windows Terminalのセットアップ（推奨）

より使いやすいターミナル環境を構築します。

### Windows Terminalのインストール

**方法1: Microsoft Storeから**

1. スタートメニューで「Microsoft Store」を開く
2. 「Windows Terminal」と検索
3. 「入手」をクリック
4. インストール完了

**方法2: wingetコマンド（Windows 11）**

PowerShellで:

```powershell
winget install Microsoft.WindowsTerminal
```

### Windows Terminalの設定

**1. Windows Terminalを開く**

- スタートメニューから「Terminal」を検索して起動

**2. デフォルトプロファイルの変更（オプション）**

WSLをデフォルトにしたい場合:

1. `Ctrl + ,`（設定を開く）
2. 「スタートアップ」タブ
3. 「既定のプロファイル」で「Ubuntu-22.04」を選択
4. 保存

**3. フォントの変更（オプション）**

見やすいフォントに変更:

1. 設定 → プロファイル → Ubuntu 22.04（または該当プロファイル）
2. 「外観」タブ
3. フォントを「Cascadia Code」や「Consolas」に変更
4. サイズを調整（推奨: 12）

---

## トラブルシューティング

### WSLインストールエラー

**エラー: 「この操作を実行するには、仮想マシン機能を有効にする必要があります」**

**解決方法**:
1. BIOSで仮想化（Virtualization、VT-x、AMD-V）を有効化
2. Windowsの機能で「仮想マシンプラットフォーム」を有効化

PowerShellで:
```powershell
dism.exe /online /enable-feature /featurename:VirtualMachinePlatform /all /norestart
```

再起動後、再度`wsl --install`を実行。

### npmインストールで権限エラー

**エラー: 「EACCES: permission denied」**

**解決方法（WSL）**:
npmのグローバルディレクトリを変更:

```bash
mkdir ~/.npm-global
npm config set prefix '~/.npm-global'
echo 'export PATH=~/.npm-global/bin:$PATH' >> ~/.bashrc
source ~/.bashrc
```

再度インストール:
```bash
npm install -g @anthropic-ai/claude-code
```

### claude: command not found

**原因**: パスが通っていない

**解決方法**:
```bash
echo 'export PATH="$PATH:$(npm config get prefix)/bin"' >> ~/.bashrc
source ~/.bashrc
```

### WSLの起動が遅い

**解決方法**: Windows Defenderの除外設定

1. Windows セキュリティを開く
2. ウイルスと脅威の防止 → 設定の管理
3. 除外 → 除外の追加
4. 「フォルダー」を選択
5. `%USERPROFILE%\AppData\Local\Packages\CanonicalGroupLimited...`を追加

---

## VS Code / Cursorとの統合

WSL環境でVS CodeやCursorを使う場合:

### VS Codeの設定

**1. Remote - WSL拡張機能のインストール**

1. VS Codeを開く
2. 拡張機能タブ（Ctrl+Shift+X）
3. 「Remote - WSL」と検索
4. インストール

**2. WSL環境を開く**

1. Ctrl+Shift+P
2. 「WSL: New Window」を選択
3. WSL内のフォルダが開ける

**3. VS CodeのターミナルでClaude Code使用**

1. Ctrl+`でターミナルを開く
2. `claude`と入力
3. WSL環境のClaude Codeが起動

---

## まとめ

Windowsでは2つのインストール方法があります:

**方法1: 直接インストール（2025年7月以降）**
- シンプルで簡単
- 初心者におすすめ
- PowerShellから直接使用

**方法2: WSL経由**
- より本格的な開発環境
- Linux環境で動作
- チーム開発に適している

どちらの方法でも、Node.js → Claude Codeの順でインストールし、初回起動時に認証すれば使い始められます。

次章では、認証とAPI設定について詳しく解説します。
