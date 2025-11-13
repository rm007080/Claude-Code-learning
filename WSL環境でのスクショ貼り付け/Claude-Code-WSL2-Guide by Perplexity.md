# Claude Code × WSL2 クリップボード統合・スクリーンショット貼り付けガイド

## 概要
このドキュメントはClaude Code/Cursorでスクリーンショットを貼り付ける方法、改行方法、およびWSL2環境でのクリップボード統合をまとめたチーム共有用ガイドです。

---

## 第1部：基本的な操作方法

### 1. スクリーンショット貼り付け方法

#### Windows環境
```bash
# キーボード操作
1. Win + Shift + S でスクリーンショット範囲を選択
2. 自動的にクリップボードへコピー
3. Claude Code画面で Ctrl + V で貼り付け
4. [Image #1] のような表示が出れば認識成功

# または
- 画像ファイルをドラッグ&ドロップで貼り付け
```

#### Mac環境
```bash
# キーボード操作
1. Command + Shift + 4（または Command + Shift + Control + 4）でスクリーンショット範囲を選択
2. クリップボードに画像が保存
3. Cmd + V（または Ctrl + V）で貼り付け
```

---

### 2. 改行方法

#### 一般的な設定
```bash
# 最も確実な方法
Ctrl + J  # Claude Code入力欄で改行（送信されない）

# 代替キー
Alt + Enter      # または
Shift + Enter    # （設定によって異なる）
```

#### VSCode/Cursorでの設定変更
```json
// settings.json に以下を追加
{
  "actions": [
    {"command": {"action": "sendInput", "input": "\n"}, "keys": "shift+enter"}
  ]
}
```

---

## 第2部：WSL2でのクリップボード統合セットアップ

### 1. 前提条件確認

```bash
# 環境確認コマンド
uname -a              # Linux環境か確認
wsl --version         # WSLバージョン確認（v2.1以上推奨）
```

**推奨環境**
- Windows 11 + Microsoft Store版WSL
- WSL 2.1以上
- Ubuntu 20.04 LTS以上

---

### 2. クリップボードツールのインストール

```bash
# パッケージリストの更新
sudo apt update

# xclipとwl-clipboardの両方をインストール
sudo apt install xclip wl-clipboard

# インストール確認
which xclip           # → /usr/bin/xclip と表示されればOK
which wl-copy         # → /usr/bin/wl-copy と表示されればOK
```

**重要**
- Cursorでも同じWSL2環境を使うため、別途インストール不要
- Cursorは「Remote - WSL」拡張機能でWSL2に接続
- WSL2環境は複数エディタで共有される

---

### 3. 環境変数確認と設定

```bash
# 現在の状態を確認
echo $DISPLAY           # X11接続状態（:0 が表示されればOK）
echo $WAYLAND_DISPLAY   # Wayland接続状態（wayland-0 が表示されればOK）

# WSLg（WSL GUI）が有効か確認
ps aux | grep -i wslg  # プロセス確認
```

**正常な環境の例**
```
DISPLAY=:0
WAYLAND_DISPLAY=wayland-0
```

---

### 4. Waylandソケットのシンボリックリンク設定

```bash
# Waylandソケットのシンボリックリンクを作成（問題発生時）
ln -s /mnt/wslg/runtime-dir/wayland-0* $XDG_RUNTIME_DIR/

# 動作確認
echo "テスト" | wl-copy
```

Windows側でCtrl+Vを押して「テスト」が貼り付けられればOK。

---

## 第3部：トラブルシューティング

### 問題①：xclip/wl-clipboardがインストール済みだが動作しない

#### Step 1: クリップボード接続確認
```bash
# DISPLAY確認
echo $DISPLAY

# 期待される出力
# :0 が表示されればOK
# （表示されない場合はX11接続失敗）

# Wayland確認
echo $WAYLAND_DISPLAY

# 期待される出力
# wayland-0 が表示されればOK
# （表示されない場合はWayland接続失敗）
```

#### Step 2: wl-clipboardで動作テスト
```bash
# テキストをコピー
echo "テスト" | wl-copy

# コンソール出力
# → エラーなし：OK
# → "Failed to connect to Wayland server" ：シンボリックリンク設定が必要
```

#### Step 3: Waylandソケット修復
```bash
# Waylandソケットのシンボリックリンク作成
ln -s /mnt/wslg/runtime-dir/wayland-0* $XDG_RUNTIME_DIR/

# 再度テスト
echo "テスト" | wl-copy
```

---

### 問題②：テキストコピーは成功するが、画像（スクリーンショット）が貼り付けられない

#### 原因
Claude CodeはクリップボードからのX11/Waylandレベルでの**画像バイナリー読み込みに対応していない**

#### 対策①：画像ファイルとしてパス指定（推奨）

```bash
# 1. Windows側でスクリーンショットを保存
# Win + Shift + S → 画像を保存（例：C:\Users\ユーザー\Pictures\screenshot.png）

# 2. Claude Codeで以下のように指示
"""
この画像を解析してください: C:\Users\littl\Pictures\screenshot.png
"""

# Claude側が自動パス変換ルール(CLAUDE.md)で以下のように変換
# C:\Users\littl\Pictures\screenshot.png
#     ↓ (自動変換)
# /mnt/c/Users/littl/Pictures/screenshot.png
```

#### 対策②：ドラッグ&ドロップで貼り付け

```bash
# 1. Windows エクスプローラーで画像ファイルを表示
# 2. Claude Code の入力欄に直接ドラッグ&ドロップ
# 3. 自動的に [Image #1] のような形式で挿入
```

#### 対策③：PowerShellスクリプトで自動ファイル化（上級向け）

```powershell
# C:\Users\littl\clipboard_to_file.ps1 として保存
Add-Type -AssemblyName System.Windows.Forms
$img = [System.Windows.Forms.Clipboard]::GetImage()
if ($img) {
    $timestamp = Get-Date -Format "yyyyMMdd_HHmmss"
    $path = "C:\Users\littl\Pictures\clipboard_$timestamp.png"
    $img.Save($path)
    Write-Output $path
}
```

WSL側でエイリアス作成：
```bash
# ~/.bashrc に以下を追加
alias clipimg='powershell.exe -ExecutionPolicy Bypass -File /mnt/c/Users/littl/clipboard_to_file.ps1'

# 使い方
clipimg
# → 画像が自動保存され、パスが表示される
```

#### 対策④：Paste Image拡張機能（簡単）

```bash
# 1. Cursor/VS Codeの拡張機能から「Paste Image」をインストール
# 2. スクリーンショット撮影後
# 3. Ctrl + Alt + V で自動貼り付け
```

---

## 第4部：Claude Code自動パス変換設定

### CLAUDE.md ファイルの作成・編集

```bash
# Claude Codeプロジェクト内で CLAUDE.md を作成または編集
# （既存の場合は既存ファイルに追記）

nano CLAUDE.md
```

### CLAUDE.md の内容（テンプレート）

```markdown
# Claude Code Configuration

## MUST - Critical Rules
- MUST WindowsのパスはUbuntuのマウントパスに変換すること
- 例: "C:\Users\user1\Pictures\test.jpg" → "/mnt/c/Users/user1/Pictures/test.jpg"
- 例: "C:\workspace\project" → "/mnt/c/workspace/project"

## Path Conversion Rule
- Windows Drive Letter: C: → /mnt/c/
- Windows Drive Letter: D: → /mnt/d/
- Backslash: \ → Forward Slash: /

## Important Notes
- Always use WSL-compatible paths when referencing files
- Do not mix Windows and WSL paths in the same command
- When providing file paths to the user, convert them to WSL format

## Example Commands
### Correct
wc -l /mnt/c/Users/user1/Pictures/data.txt

### Incorrect
wc -l C:\Users\user1\Pictures\data.txt
```

### 設定反映手順

```bash
# 1. CLAUDE.md を保存
# 2. Claude Code本体を完全に終了
# 3. Claude Codeを再起動
# 4. 新しいルールが有効化される

# 動作確認
"""
この画像を分析してください: C:\Users\littl\Pictures\screenshot.png
"""
# → Claude がパスを自動変換して /mnt/c/Users/littl/Pictures/screenshot.png と認識
```

---

## 第5部：動作確認チェックリスト

### セットアップ完了後の確認項目

```bash
# ✓ チェックリスト

□ WSL2がインストール済みか確認
  $ wsl --version

□ xclip がインストール済みか確認
  $ which xclip
  # → /usr/bin/xclip と表示されればOK

□ wl-clipboard がインストール済みか確認
  $ which wl-copy
  # → /usr/bin/wl-copy と表示されればOK

□ DISPLAY 環境変数が設定されているか
  $ echo $DISPLAY
  # → :0 と表示されればOK

□ WAYLAND_DISPLAY が設定されているか
  $ echo $WAYLAND_DISPLAY
  # → wayland-0 と表示されればOK

□ テキストコピーが動作するか
  $ echo "テスト" | wl-copy
  # Windows側でCtrl+V → 「テスト」が貼り付けられればOK

□ CLAUDE.md にパス変換ルールが追記されているか
  $ cat CLAUDE.md | grep -i "windows"
  # → パス変換ルール関連の行が表示されればOK

□ Claude Code を再起動したか
  # → CLAUDE.md の変更を反映させるため必須
```

### 全チェック完了時の状態

```
環境構築完了
├── WSL2クリップボード統合: ✅ 動作確認済み
├── xclip/wl-clipboard: ✅ インストール確認済み
├── テキストコピー: ✅ 動作確認済み
├── パス自動変換: ✅ CLAUDE.md 設定完了
└── Claude Code: ✅ 再起動完了
```

---

## 第6部：環境別の推奨セットアップ

### Windows 11 + Microsoft Store版WSL（推奨）

```bash
# 1. パッケージ更新とツールインストール
sudo apt update
sudo apt install xclip wl-clipboard

# 2. 環境変数確認
echo $DISPLAY
echo $WAYLAND_DISPLAY

# 3. 動作テスト
echo "テスト" | wl-copy

# 4. CLAUDE.md にパス変換ルール追加

# 5. Claude Code再起動
```

### Windows 10環境（VcXsrv必要）

```bash
# 1. VcXsrv をダウンロード・インストール
# https://sourceforge.net/projects/vcxsrv/

# 2. VcXsrvの設定
# - Multiple windows: 選択
# - Start no client: 選択
# - Clipboard: ✓ チェック
# - Disable access control: ✓ チェック
# - 設定を保存（自動起動用）

# 3. WSL側に環境変数を設定（~/.bashrc に追加）
LOCAL_IP=$(cat /etc/resolv.conf | grep nameserver | awk '{print $2}')
export DISPLAY=$LOCAL_IP:0

# 4. 設定を反映
source ~/.bashrc

# 5. 動作テスト
echo "テスト" | xclip -selection clipboard
```

---

## 第7部：Cursor IDE での設定

### Cursor + WSL2 接続確認

```bash
# Cursor を起動後
# 1. 左下の "><" アイコンをクリック
# 2. "Connect to WSL" を選択
# 3. WSL2ディストリビューション選択

# 接続成功確認
# → Cursor左下に "WSL: Ubuntu" などと表示されればOK

# Cursor 統合ターミナルから確認
echo $DISPLAY        # :0 が表示されればOK
which wl-copy        # /usr/bin/wl-copy が表示されればOK
```

### Cursor でのパス指定方法

```bash
# CLAUDE.md 設定後の使用例

# 画像ファイルを参照する場合
"""
このスクリーンショットのエラーメッセージを解析してください:
C:\Users\littl\Pictures\error_screenshot.png
"""

# Cursor が自動的に以下のように解釈
# → /mnt/c/Users/littl/Pictures/error_screenshot.png

# ファイル操作も可能
"""
以下のテキストファイルを読み込んで、JSONに変換してください:
C:\Users\littl\Desktop\data.txt
"""
```

---

## 第8部：よくある質問（FAQ）

### Q1: xclip と wl-clipboard は両方必要ですか？

```
A: 不要です。以下の通り1つだけで大丈夫です：

- Windows 11（WSLg有効）: wl-clipboard のみで十分
- Windows 10（VcXsrv使用）: xclip のみで十分

ただし、両方インストールしても問題ありません。
```

### Q2: Ctrl + V でスクリーンショットを直接貼り付けたいのですが

```
A: Claude Code の仕様上、X11/Waylandレベルでの画像バイナリー読み込み非対応です。

以下のいずれかの方法をお勧めします：
1. 画像ファイルとしてパス指定
2. ドラッグ&ドロップで貼り付け
3. Paste Image拡張機能を使用
```

### Q3: CLAUDE.md を編集しましたが反映されません

```
A: 以下を確認してください：

1. CLAUDE.md のファイル名が正確か確認（大文字小文字も含む）
2. Claude Code を完全に終了→再起動
3. ファイル内容に誤字がないか確認
4. ~/.bashrc も反映が必要な場合は source ~/.bashrc を実行
```

### Q4: WSL2を再起動しても設定が消える場合は

```
A: 環境変数を永続化してください：

# ~/.bashrc に以下を追加
export DISPLAY=:0
export WAYLAND_DISPLAY=wayland-0

# 設定を反映
source ~/.bashrc
```

---

## 第9部：トラブルシューティング フローチャート

```
スクリーンショットが貼り付けられない
│
├─ テキストコピー(echo "test" | wl-copy)は成功？
│  │
│  ├─ Yes → 「問題②：画像が貼り付けられない」へ
│  │
│  └─ No → 下記へ
│     │
│     ├─ echo $WAYLAND_DISPLAY で wayland-0 が表示？
│     │  │
│     │  ├─ No → Waylandソケット作成を実行
│     │  │      ln -s /mnt/wslg/runtime-dir/wayland-0* $XDG_RUNTIME_DIR/
│     │  │
│     │  └─ Yes → WSL再起動
│     │
│     └─ VcXsrv が起動しているか確認（Windows 10の場合）
│
└─ 画像（スクリーンショット）が貼り付けられない
   │
   ├─ CLAUDE.md にパス変換ルールを追記
   │
   ├─ Claude Code を再起動
   │
   └─ ドラッグ&ドロップで画像を直接貼り付け試行
```

---

## 第10部：参考コマンド集

```bash
# 環境確認系
wsl --version                              # WSLバージョン確認
wsl -l -v                                  # インストール済みディストリ一覧
uname -a                                   # Linux情報確認
echo $SHELL                                # デフォルトシェル確認

# クリップボード操作系
echo "文字列" | wl-copy                    # Wayland: コピー
wl-paste                                   # Wayland: ペースト確認
echo "文字列" | xclip -selection clipboard # X11: コピー
xclip -selection clipboard -o              # X11: ペースト確認

# ファイルパス確認系
which xclip                                # xclip の場所確認
which wl-copy                              # wl-copy の場所確認
ls -la /mnt/c/Users/                       # Windowsフォルダ確認

# WSL再起動系
wsl --terminate Ubuntu                     # 特定ディストリを終了
wsl --shutdown                             # WSL全体を終了
wsl                                        # WSL起動

# 設定ファイル関連
cat CLAUDE.md                              # CLAUDE.md 内容確認
nano ~/.bashrc                             # ~/.bashrc 編集
source ~/.bashrc                           # 設定反映
```

---

## まとめ

このガイドで以下が実現できます：

```
✅ Claude Code/Cursorでスクリーンショットを効率的に使用
✅ WSL2とWindowsのクリップボード統合
✅ WindowsパスからWSLパスへの自動変換
✅ 改行などの基本的な操作
✅ トラブル発生時の原因特定と対策実施
```

---

## 版履歴

| バージョン | 日付 | 内容 |
|-----------|------|------|
| 1.0 | 2025-10-28 | 初版作成（全10部構成） |

---

## 参考リンク・資料

- WSL公式ドキュメント: https://learn.microsoft.com/ja-jp/windows/wsl/
- Claude Code公式: https://github.com/anthropics/claude-code
- Cursor IDE: https://www.cursor.com/
