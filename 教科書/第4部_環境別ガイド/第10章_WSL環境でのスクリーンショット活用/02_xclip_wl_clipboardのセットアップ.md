# 02. xclipとwl-clipboardのセットアップ

## セットアップ前の準備

### 前提条件の確認

```bash
# WSL2がインストールされているか確認
wsl --version

# WSL2が実行中か確認
wsl --list --verbose

# 出力例
#   NAME      STATE       VERSION
# * Ubuntu    Running     2
```

### システム更新

```bash
# パッケージリストを最新化
sudo apt update

# 既存パッケージを更新（推奨）
sudo apt upgrade -y
```

## Windows 11環境でのセットアップ

### Step 1: パッケージのインストール

```bash
# wl-clipboardをインストール（メイン）
sudo apt install wl-clipboard -y

# xclipもインストール（互換性のため）
sudo apt install xclip -y
```

### Step 2: インストール確認

```bash
# wl-copyのパス確認
which wl-copy
# 期待される出力: /usr/bin/wl-copy

# wl-pasteのパス確認
which wl-paste
# 期待される出力: /usr/bin/wl-paste

# xclipのパス確認
which xclip
# 期待される出力: /usr/bin/xclip

# バージョン確認
wl-copy --version
xclip -version
```

### Step 3: 環境変数の確認

```bash
# DISPLAY環境変数
echo $DISPLAY
# 期待される出力: :0

# WAYLAND_DISPLAY環境変数
echo $WAYLAND_DISPLAY
# 期待される出力: wayland-0

# XDG_RUNTIME_DIR環境変数
echo $XDG_RUNTIME_DIR
# 期待される出力: /run/user/1000 など
```

### Step 4: 動作テスト

```bash
# テキストをクリップボードにコピー
echo "テスト成功！" | wl-copy

# Windows側で確認:
# メモ帳などを開いてCtrl+Vで貼り付け
# 「テスト成功！」が表示されればOK

# WSL側でクリップボードの内容を確認
wl-paste
# 期待される出力: テスト成功！
```

### Step 5: Waylandソケットの確認とシンボリックリンク

```bash
# Waylandソケットの存在確認
ls -la /mnt/wslg/runtime-dir/

# 期待される出力例
# wayland-0
# wayland-0.lock

# XDG_RUNTIME_DIR内のソケット確認
ls -la $XDG_RUNTIME_DIR/ | grep wayland

# シンボリックリンクがない場合は作成
ln -sf /mnt/wslg/runtime-dir/wayland-0 $XDG_RUNTIME_DIR/
ln -sf /mnt/wslg/runtime-dir/wayland-0.lock $XDG_RUNTIME_DIR/

# 再度テスト
echo "リンク設定後のテスト" | wl-copy
wl-paste
```

## Windows 10環境でのセットアップ

### Step 1: VcXsrvのインストール（Windows側）

```
1. VcXsrvをダウンロード
   URL: https://sourceforge.net/projects/vcxsrv/

2. インストーラーを実行

3. VcXsrvを起動
   - Multiple windows を選択
   - Start no client を選択
   - Clipboard にチェック ✓
   - Primary Selection にチェック ✓
   - Disable access control にチェック ✓

4. 設定を保存（自動起動用）
   - Save configuration ボタンをクリック
   - スタートアップフォルダに保存
```

### Step 2: WSL側のパッケージインストール

```bash
# xclipをインストール
sudo apt install xclip -y

# インストール確認
which xclip
# 期待される出力: /usr/bin/xclip

# バージョン確認
xclip -version
```

### Step 3: DISPLAY環境変数の設定

```bash
# .bashrcファイルを編集
nano ~/.bashrc

# 以下を末尾に追加
export LOCAL_IP=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2}')
export DISPLAY=$LOCAL_IP:0

# 保存して終了（Ctrl+O、Enter、Ctrl+X）

# 設定を即座に反映
source ~/.bashrc

# 確認
echo $DISPLAY
# 期待される出力例: 172.28.160.1:0
```

### Step 4: 動作テスト

```bash
# VcXsrvが起動していることを確認してから実行

# テキストをクリップボードにコピー
echo "Windows 10でのテスト成功！" | xclip -selection clipboard

# Windows側で確認:
# メモ帳などを開いてCtrl+Vで貼り付け

# WSL側でクリップボードの内容を確認
xclip -selection clipboard -o
# 期待される出力: Windows 10でのテスト成功！
```

## トラブルシューティング（セットアップ時）

### エラー: "cannot open display"

**原因:**
DISPLAY環境変数が正しく設定されていない

**解決方法:**
```bash
# DISPLAY変数を確認
echo $DISPLAY

# 空の場合は設定
export DISPLAY=:0

# またはWindows 10の場合
export DISPLAY=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2}'):0

# 恒久的に設定
echo 'export DISPLAY=:0' >> ~/.bashrc
source ~/.bashrc
```

### エラー: "Failed to connect to Wayland server"

**原因:**
Waylandソケットへの接続に失敗

**解決方法:**
```bash
# Waylandソケットの存在確認
ls -la /mnt/wslg/runtime-dir/wayland-0

# シンボリックリンクを作成
ln -sf /mnt/wslg/runtime-dir/wayland-0 $XDG_RUNTIME_DIR/wayland-0

# 環境変数を確認
echo $WAYLAND_DISPLAY
# 空の場合は設定
export WAYLAND_DISPLAY=wayland-0
```

### wl-copy/wl-pasteが見つからない

**原因:**
パッケージがインストールされていない

**解決方法:**
```bash
# パッケージリストを更新
sudo apt update

# 再度インストール
sudo apt install wl-clipboard -y

# インストール確認
dpkg -l | grep wl-clipboard
```

### VcXsrvが動作しない（Windows 10）

**原因:**
- VcXsrvが起動していない
- ファイアウォールがブロックしている

**解決方法:**
```bash
# 1. VcXsrvが起動しているか確認
# タスクマネージャーでvcxsrv.exeを探す

# 2. ファイアウォール設定を確認
# Windows Defender ファイアウォールで
# VcXsrvを許可リストに追加

# 3. WSL側から接続テスト
xeyes
# 目のアイコンが表示されればX11接続成功
```

## 自動起動の設定

### Windows起動時にVcXsrvを自動起動（Windows 10）

```powershell
# 1. VcXsrvの設定を保存
# VcXsrv起動時に「Save configuration」で保存

# 2. 保存したconfig.xlaunchをスタートアップフォルダに配置
# スタートアップフォルダを開く
shell:startup

# 3. config.xlaunchをコピー
```

### WSL起動時に環境変数を自動設定

```bash
# ~/.bashrcまたは~/.zshrcに追記
cat >> ~/.bashrc << 'EOF'

# WSL2クリップボード設定
if [ -n "$WSL_DISTRO_NAME" ]; then
    # Windows 11（WSLg）の場合
    if [ -f /mnt/wslg/versions.txt ]; then
        export DISPLAY=:0
        export WAYLAND_DISPLAY=wayland-0
    # Windows 10の場合
    else
        export LOCAL_IP=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2}')
        export DISPLAY=$LOCAL_IP:0
    fi
fi
EOF

# 設定を反映
source ~/.bashrc
```

## 確認チェックリスト

完全なセットアップ確認のためのチェックリスト:

```bash
# ✓ パッケージがインストールされているか
which wl-copy && which xclip

# ✓ 環境変数が設定されているか
echo $DISPLAY && echo $WAYLAND_DISPLAY

# ✓ Waylandソケットが存在するか（Windows 11）
ls -la $XDG_RUNTIME_DIR/wayland-0

# ✓ テキストコピーが動作するか
echo "最終確認テスト" | wl-copy && wl-paste

# ✓ Windows側で貼り付けができるか
# メモ帳でCtrl+Vを試す
```

## まとめ

- Windows 11ではwl-clipboard、Windows 10ではxclipが主要ツール
- 環境変数（DISPLAY、WAYLAND_DISPLAY）の設定が重要
- Waylandソケットのシンボリックリンク設定が必要な場合がある
- VcXsrv（Windows 10）の起動とファイアウォール設定に注意
- 自動起動設定で毎回の手動設定を省略可能

次章では、画像ファイルの扱い方とパス変換について詳しく解説します。
