# 05. VS Code/Cursorとの連携

## この章で学ぶこと

Claude CodeとVS Code/Cursorを並行利用することで、開発効率を最大化できます。この章では、効率的な画面配置、VS Code拡張機能の活用、ショートカットキー、実践的なワークフローを学びます。

## なぜ並行利用するのか？

### Claude Code単体の制限

Claude Codeはターミナルベースのため:
- コードの可視性が限られる
- ファイル間のナビゲーションが難しい
- リアルタイムのプレビューができない
- Git の差分確認が見づらい

### VS Code/Cursorとの組み合わせメリット

```
Claude Code          VS Code/Cursor
(ターミナル)        (エディタ)
─────────────────   ─────────────────
指示を出す          コードを見る
実装を依頼          変更を確認
コマンドを実行      Git差分を確認
テストを実行        デバッグする
```

## 推奨画面レイアウト

### パターン1: 横並び（推奨）

```
┌────────────────────────┬────────────────────────┐
│                        │                        │
│    VS Code/Cursor      │      ターミナル        │
│      (エディタ)        │    (Claude Code)       │
│                        │                        │
│  ・ファイルツリー      │  ・Claude Codeセッション│
│  ・エディタ            │  ・指示入力            │
│  ・Git差分             │  ・実行結果確認        │
│                        │                        │
└────────────────────────┴────────────────────────┘
```

**適している作業**:
- コードレビュー
- 実装確認
- Git差分確認

### パターン2: 上下分割

```
┌──────────────────────────────────────────────────┐
│                                                  │
│            VS Code/Cursor                        │
│            (エディタ)                            │
│                                                  │
├──────────────────────────────────────────────────┤
│                                                  │
│            ターミナル (Claude Code)              │
│                                                  │
└──────────────────────────────────────────────────┘
```

**適している作業**:
- 長いファイルの編集
- ターミナル出力の確認
- ログの監視

### パターン3: VS Code統合ターミナル

```
┌──────────────────────────────────────────────────┐
│              VS Code/Cursor                      │
│  ┌────────────────┬────────────────┐            │
│  │ ファイルツリー │   エディタ     │            │
│  │                │                │            │
│  └────────────────┴────────────────┘            │
│  ┌──────────────────────────────────┐           │
│  │  統合ターミナル (Claude Code)    │           │
│  └──────────────────────────────────┘           │
└──────────────────────────────────────────────────┘
```

**適している作業**:
- シングルモニター環境
- 頻繁な切り替えが必要な作業

## Claude Code VS Code拡張機能

### インストール

```bash
# VS Code/Cursorの拡張機能から検索
Claude Code for VS Code
```

または:
- [VS Code Marketplace](https://marketplace.visualstudio.com/items?itemName=anthropic.claude-code)

### 主要機能

#### 1. 自動コンテキスト追加

**機能**: 現在開いているファイルを自動的にClaude Codeのコンテキストに追加

**メリット**:
- `@ファイルパス`の入力不要
- 自動的に最新の内容を参照

**使い方**:
```bash
# VS Codeでファイルを開く
src/components/UserProfile.tsx

# Claude Codeで指示（ファイル指定不要）
> このコンポーネントにプロフィール編集機能を追加してください
```

#### 2. 選択範囲のコンテキスト化

**機能**: VS Codeで選択したテキストをClaude Codeのコンテキストに追加

**使い方**:
1. VS Codeでコードを選択
2. Claude Codeで指示
```bash
> 選択した部分をリファクタリングしてください
```

#### 3. ショートカットキー

**Command + Option + K** (macOS) / **Ctrl + Alt + K** (Windows)
- 現在のファイルをコンテキストに明示的に追加

### 設定のカスタマイズ

VS Codeの設定（settings.json）:

```json
{
  "claude-code.autoContext": true,  // 自動コンテキスト追加
  "claude-code.selectionContext": true,  // 選択範囲をコンテキスト化
  "claude-code.terminalIntegration": true  // ターミナル統合
}
```

## 効率的なショートカットキー

### VS Code/Cursor側

| ショートカット | 機能 | 活用シーン |
|-------------|------|-----------|
| `Cmd+P` | ファイル検索 | ファイルを素早く開く |
| `Cmd+Shift+P` | コマンドパレット | Git操作など |
| `Cmd+B` | サイドバー表示切替 | 作業スペース確保 |
| `Cmd+J` | ターミナル表示切替 | Claude Codeを表示 |
| `Cmd+\` | エディタ分割 | 複数ファイル同時表示 |
| `Cmd+Option+K` | コンテキスト追加 | Claude Codeに送信 |

### ターミナル側（Claude Code）

| 操作 | 機能 |
|-----|------|
| `Esc` | 実行停止 |
| `Esc × 2` | 前のメッセージを編集 |
| `Tab` | Think モード |
| `Shift+Tab × 2` | Plan モード切替 |
| `/clear` | コンテキストリセット |
| `/compact` | コンテキスト圧縮 |

## 実践的なワークフロー

### ワークフロー1: 新機能の実装

**ステップ1: ファイル確認**
```bash
# VS Codeで関連ファイルを開く
- src/components/UserProfile.tsx
- src/types/user.ts
- src/hooks/useUser.ts
```

**ステップ2: Claude Codeで指示**
```bash
# 拡張機能により、開いているファイルが自動的にコンテキストに
> これらのファイルを確認して、プロフィール編集機能の
  実装方針を提案してください think
```

**ステップ3: VS Codeで変更確認**
- リアルタイムでファイルの変更を確認
- Git差分を確認

**ステップ4: フィードバック**
```bash
# VS Codeで問題箇所を選択
> 選択した部分のエラーハンドリングを改善してください
```

### ワークフロー2: バグ修正

**ステップ1: 問題箇所の特定**
```bash
# VS Codeでファイルを開き、問題箇所を選択
src/utils/calculation.ts の calculateTotal 関数
```

**ステップ2: Claude Codeで調査依頼**
```bash
> 選択した関数にバグがあります。
  入力が空配列の場合にNaNを返します。
  原因を調査して修正してください。
```

**ステップ3: VS Codeでテスト**
```bash
# VS Codeでテストを実行
npm test calculation.test.ts
```

**ステップ4: 差分確認**
```bash
# VS CodeのSource Controlビューで差分確認
# 問題なければClaude Codeでコミット
> 修正をコミットしてください
  コミットメッセージ: fix(utils): handle empty array in calculateTotal
```

### ワークフロー3: コードレビュー

**ステップ1: Pull Request確認**
```bash
# VS CodeでPull Requestのファイルを開く
# GitHub Pull Requests拡張機能を使用
```

**ステップ2: Claude Codeでレビュー依頼**
```bash
> Pull Request #123 のコードをレビューしてください。

  確認項目:
  - セキュリティ
  - パフォーマンス
  - コーディング規約
  - テストカバレッジ
```

**ステップ3: VS Codeでコメント確認**
```bash
# レビューコメントをVS Codeで確認
# 必要に応じて修正依頼
```

### ワークフロー4: リファクタリング

**ステップ1: リファクタリング対象を選択**
```bash
# VS Codeで大きなコンポーネントを開く
src/components/Dashboard.tsx (300行)
```

**ステップ2: Plan Modeで計画**
```bash
# Claude Codeで計画立案
> このコンポーネントを以下のように分割したいです:
  - Dashboard (メインコンポーネント)
  - DashboardHeader
  - DashboardStats
  - DashboardChart
  - DashboardRecentActivity

  実装計画を立ててください think
```

**ステップ3: 段階的に実装**
```bash
> まずDashboardHeaderを分離してください
```

**ステップ4: VS Codeで確認**
```bash
# VS Codeで新しいファイルを確認
# Git差分で変更内容を確認
```

**ステップ5: テストして次へ**
```bash
> npm run devで動作確認してください
> 問題なければ、次はDashboardStatsを分離してください
```

## Git連携のベストプラクティス

### VS CodeのSource Controlビューを活用

```bash
# VS Code側でGit操作を視覚的に確認
- 変更ファイルの一覧
- 差分の確認
- ステージング

# Claude Code側でコミット
> 以下のファイルをコミットしてください:
  - src/components/Dashboard.tsx
  - src/components/DashboardHeader.tsx

  コミットメッセージ: refactor(dashboard): extract header component
```

### Git差分の確認パターン

**パターン1: VS Codeで差分確認後、Claude Codeで判断**

```bash
# VS Codeで差分を目視確認
# Claude Codeに判断を依頼

> git diffの結果を確認して、
  コミットして問題ないか教えてください
```

**パターン2: Claude Codeで差分取得、VS Codeで詳細確認**

```bash
# Claude Codeで差分取得
> git diff --stat

# VS CodeのSource Controlで詳細確認
# 問題なければコミット
> コミットしてください
```

## デバッグとの連携

### VS Codeデバッガーの活用

**ステップ1: ブレークポイント設定**
```bash
# VS Codeでブレークポイントを設定
src/utils/calculation.ts:42
```

**ステップ2: デバッグ実行**
```bash
# VS CodeのRun and Debugで実行
F5
```

**ステップ3: Claude Codeに状況説明**
```bash
> src/utils/calculation.ts の42行目でブレークしています。

  変数の状態:
  - items: []
  - total: NaN

  なぜtotalがNaNになるのか調査してください。
```

**ステップ4: 修正確認**
```bash
# VS Codeで修正内容を確認
# デバッガーで再度確認
```

## 拡張機能の推奨

### Claude Code連携に有用な拡張機能

1. **Claude Code for VS Code**
   - 公式拡張機能
   - 自動コンテキスト追加

2. **GitLens**
   - Git履歴の確認
   - ブレームビュー

3. **Error Lens**
   - エラーのインライン表示
   - Claude Codeへのエラー報告が容易

4. **GitHub Pull Requests**
   - PR の確認
   - レビューコメントの管理

5. **Live Share**
   - ペアプログラミング
   - チームでのClaude Code活用

## トラブルシューティング

### 問題1: Claude Code拡張機能が動作しない

**原因**: 拡張機能が正しくインストールされていない

**解決策**:
```bash
# 拡張機能を再インストール
1. VS Codeで拡張機能をアンインストール
2. VS Codeを再起動
3. 拡張機能を再インストール
4. Claude Codeを再起動
```

### 問題2: コンテキストが追加されない

**原因**: 設定が無効になっている

**解決策**:
```json
// settings.json
{
  "claude-code.autoContext": true
}
```

### 問題3: ショートカットキーが動作しない

**原因**: キーバインドの競合

**解決策**:
```bash
# VS Codeのキーバインド設定で確認
Cmd+K Cmd+S
# "claude-code"で検索して確認
```

## まとめ

### 並行利用の価値

1. **視覚的な確認**
   - コードの変更をリアルタイム確認
   - Git差分を見やすく表示

2. **効率的なナビゲーション**
   - ファイル間の移動が容易
   - プロジェクト全体を俯瞰

3. **デバッグの容易さ**
   - ブレークポイント設定
   - 変数の確認

4. **Git連携**
   - 視覚的な差分確認
   - ステージング管理

### 推奨レイアウト

- **デュアルモニター**: 横並び配置
- **シングルモニター**: VS Code統合ターミナル
- **大画面**: 3分割（ファイルツリー、エディタ、ターミナル）

### ベストプラクティス

1. **Claude Code拡張機能を導入**
2. **ショートカットキーを習得**
3. **Git操作はVS Codeで視覚確認**
4. **コミットはClaude Codeで実行**
5. **デバッグはVS Codeで実施**

### 次のステップ

次の章では、効果的なプロンプトの書き方を学びます。明確な指示の出し方、think/ultrathinkの活用、型定義を使った精度向上など、Claude Codeとのコミュニケーション技術を習得します。
