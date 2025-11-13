# 01. CLAUDE.mdの活用

## この章で学ぶこと

Claude Codeのメモリシステムの中核となるCLAUDE.mdファイルの役割、配置場所、効果的な書き方を学びます。CLAUDE.mdを適切に活用することで、Claude Codeとのコミュニケーションが格段に効率的になります。

## CLAUDE.mdとは？

CLAUDE.mdは、Claude Codeが自動的に読み込む「記憶ファイル」です。プロジェクトのルール、技術スタック、開発規約などを記載することで、毎回同じ指示を繰り返す必要がなくなります。

### CLAUDE.mdのメリット

1. **指示の効率化**: 毎回同じルールを説明する必要がない
2. **一貫性の確保**: チーム全体で同じルールを共有できる
3. **新メンバーのオンボーディング**: プロジェクトの理解が早くなる
4. **コンテキストの節約**: 重要な情報だけを事前に伝えられる

## CLAUDE.mdの配置場所と優先順位

CLAUDE.mdは3つの場所に配置でき、それぞれ異なる役割と優先順位があります。

### 1. プロジェクトルート: `./CLAUDE.md` (最優先)

**配置場所**:
```
my-project/
├── CLAUDE.md          ← ここ
├── package.json
└── src/
```

**用途**:
- チーム全体で共有したいプロジェクト固有のルール
- Gitでバージョン管理される
- READMEのような役割も担う

**使用例**:
- プロジェクトの概要と目的
- 技術スタック
- ディレクトリ構造の説明
- 開発フロー

### 2. .claudeディレクトリ: `./.claude/CLAUDE.md` (次優先)

**配置場所**:
```
my-project/
├── .claude/           ← 隠しフォルダ
│   └── CLAUDE.md      ← ここ
├── package.json
└── src/
```

**用途**:
- プロジェクト専用のメモリ
- 個人的なルールやメモ (.gitignoreに追加可能)
- より詳細な実装ガイドライン

**使用例**:
- 個人の作業メモ
- プロジェクト固有の細かい実装ルール
- チームに共有しない個人設定

### 3. ホームディレクトリ: `~/.claude/CLAUDE.md` (最低優先)

**配置場所**:
```bash
# Linux/macOS
/home/username/.claude/CLAUDE.md

# Windows (WSL)
/home/username/.claude/CLAUDE.md

# Windows (PowerShell)
C:\Users\username\.claude\CLAUDE.md
```

**用途**:
- 全プロジェクトで共通の個人ルール
- 個人の開発スタイルやコーディング規約
- プロジェクトに依存しない設定

**使用例**:
- 常に日本語で返答してほしい
- テスト駆動開発を優先
- Conventional Commits形式でコミット

## CLAUDE.mdの作成方法

### 方法1: /initコマンドで自動生成

最も簡単な方法は、Claude Codeの`/init`コマンドを使用することです。

```bash
> /init
```

Claude Codeがプロジェクト全体を分析し、以下の内容を自動生成します:
- 使用している技術スタック
- ディレクトリ構成
- 主要な機能の概要
- 検出されたコーディング規約

**実行タイミング**:
- プロジェクト開始時
- 大きな機能追加の後
- フォルダ構成を大きく変更した後

### 方法2: 手動で作成

エディタで直接編集する方法です。

```bash
# プロジェクトルートに作成
touch CLAUDE.md
code CLAUDE.md

# .claudeディレクトリに作成
mkdir -p .claude
touch .claude/CLAUDE.md
code .claude/CLAUDE.md

# ホームディレクトリに作成
mkdir -p ~/.claude
touch ~/.claude/CLAUDE.md
code ~/.claude/CLAUDE.md
```

## 効果的なCLAUDE.mdの書き方

### 基本テンプレート

```markdown
# プロジェクト名

## プロジェクト概要
このプロジェクトの目的と主要な機能を記載

## 技術スタック
- フロントエンド: React 18 + TypeScript
- バックエンド: Node.js + Express
- データベース: PostgreSQL
- スタイリング: Tailwind CSS

## ディレクトリ構造
```
src/
├── components/  # UIコンポーネント
├── pages/       # ページコンポーネント
├── api/         # API関連
├── utils/       # ユーティリティ関数
└── types/       # TypeScript型定義
```

## コーディング規約
- ES Modulesを使用（CommonJSは使わない）
- 関数は単一責任の原則に従う
- コンポーネントは100行以内に抑える
- TypeScript strict モードを有効にする

## 開発フロー
1. 機能追加前に必ずテストを書く
2. 実装後に型チェックを実行: `npm run typecheck`
3. コミット前にlintとformatを実行
4. コミットメッセージは Conventional Commits 形式

## よく使うコマンド
- `npm run dev`: 開発サーバー起動
- `npm run build`: 本番用ビルド
- `npm test`: テスト実行
- `npm run lint`: Lint実行
- `npm run typecheck`: 型チェック

## 注意事項
- .envファイルは絶対にコミットしない
- APIキーは環境変数で管理
- テストは個別に実行（全体実行は避ける）
```

### フロントエンド開発向けの例

```markdown
# プロジェクト: ECサイト

## 技術スタック
- Next.js 14 (App Router)
- TypeScript 5.0
- Tailwind CSS
- Prisma ORM
- PostgreSQL

## UIコンポーネント規約
- すべてのコンポーネントはServer Componentを優先
- Client Componentが必要な場合のみ'use client'を使用
- コンポーネントファイル名はPascalCase (例: Button.tsx)
- propsの型定義は必須

## スタイリング規約
- Tailwind CSSを優先的に使用
- カスタムCSSは最小限に
- レスポンシブデザイン必須: mobile-first
- ダークモード対応も考慮

## データフェッチング
- Server Componentでfetchを直接使用
- Client ComponentではuseQueryを使用
- ローディング状態とエラー状態の処理を忘れない

## 命名規則
- 変数: camelCase
- コンポーネント: PascalCase
- 定数: UPPER_SNAKE_CASE
- ファイル名: コンポーネントと同じ名前
```

### バックエンド開発向けの例

```markdown
# プロジェクト: REST API

## 技術スタック
- Node.js 20
- Express.js
- TypeScript
- Prisma ORM
- PostgreSQL
- Jest (テスト)

## API設計規約
- RESTful APIの原則に従う
- HTTPステータスコードを適切に使用
- エラーレスポンスは統一フォーマット
- APIバージョニングを行う (例: /api/v1/users)

## エラーハンドリング
```typescript
{
  "error": {
    "code": "VALIDATION_ERROR",
    "message": "User-friendly error message",
    "details": []
  }
}
```

## セキュリティ
- すべてのエンドポイントで認証を確認
- JWTトークンの有効期限: 1時間
- 環境変数でシークレットを管理
- SQL Injectionに注意（Prismaを使用）

## テスト規約
- すべてのエンドポイントにテストを書く
- テストカバレッジ80%以上を目標
- モックを適切に使用
- テストは高速に実行できるように
```

## CLAUDE.mdの更新戦略

### 更新のタイミング

1. **機能追加時**
   - 新しい技術スタックを導入したとき
   - 新しいディレクトリを追加したとき
   - 重要な設計決定をしたとき

2. **問題発見時**
   - Claude Codeが誤った実装をしたとき
   - よくあるミスを発見したとき
   - 新しいベストプラクティスを見つけたとき

3. **定期的なメンテナンス**
   - 週に1回程度見直す
   - 不要になった情報を削除
   - 冗長な説明を簡略化

### 更新方法

**方法1: /initコマンドで更新**
```bash
> /init
```
プロジェクト全体を再分析し、CLAUDE.mdを更新します。

**方法2: Claude Codeに依頼**
```bash
> [発見した内容]について、CLAUDE.mdに追記してください
```

**方法3: 手動編集**
エディタで直接編集し、必要な情報を追加・削除します。

**方法4: #記法で即座に追記**
```bash
> # console.logは削除しないでください
```
`#`で始まる指示は、その内容をCLAUDE.mdに自動追記します。

## サイズの最適化

CLAUDE.mdが大きくなりすぎると、毎回のトークン消費が増えてしまいます。

### 適切なサイズ

- **推奨**: 1,000〜3,000文字程度
- **最大**: 5,000文字以内
- **目安**: Markdownで100〜150行程度

### 大きくなりすぎた場合の対処法

1. **情報の分割**
```
my-project/
├── CLAUDE.md                # 全体概要（簡潔に）
└── .claude/
    ├── architecture.md      # アーキテクチャ詳細
    ├── api-guide.md         # API仕様
    └── ui-patterns.md       # UIパターン集
```

2. **古い情報の削除**
完了したタスクや不要になった情報を定期的に削除します。

3. **冗長な説明の簡略化**
詳細すぎる説明は要点だけに絞ります。

```markdown
# 悪い例（冗長）
このプロジェクトはReactを使用しています。ReactはFacebookが開発した
ライブラリで、コンポーネントベースのUI構築が可能です...

# 良い例（簡潔）
技術スタック: React 18, TypeScript, Tailwind CSS
```

## トラブルシューティング

### CLAUDE.mdが反映されない

**原因1: ファイルの場所が間違っている**
```bash
# 正しい場所にあるか確認
ls -la CLAUDE.md
ls -la .claude/CLAUDE.md
ls -la ~/.claude/CLAUDE.md
```

**原因2: セッションを再起動していない**
```bash
> /exit
# 再度Claude Codeを起動
claude
```

### 設定が競合している

複数のCLAUDE.mdに同じ項目が記載されていると、優先順位の高い方が適用されます。

**確認方法**:
```bash
> 現在読み込んでいるCLAUDE.mdの内容を教えてください
```

## 実践例: 新規プロジェクトのセットアップ

### ステップ1: プロジェクト開始

```bash
# プロジェクトディレクトリに移動
cd my-new-project

# Claude Codeを起動
claude

# 自動生成
> /init
```

### ステップ2: 内容を確認・編集

```bash
# 生成されたCLAUDE.mdを確認
> CLAUDE.mdを表示してください

# 必要に応じて追記を依頼
> 以下のルールをCLAUDE.mdに追加してください:
- すべての関数にはJSDocコメントを書く
- エラーハンドリングは必須
- console.logは本番コードに含めない
```

### ステップ3: 開発中の更新

```bash
# 実装中に気付いたルールを追加
> # useEffectの依存配列は必ず適切に設定すること

# 大きな変更後に再生成
> /init
```

## まとめ

### 重要ポイント

1. **3つの配置場所**を使い分ける
   - プロジェクトルート: チーム共有
   - .claude/: 個人・プロジェクト固有
   - ~/.claude/: 全プロジェクト共通

2. **`/init`コマンド**で簡単スタート
   - 定期的に実行して最新状態を維持

3. **簡潔に保つ**
   - 1,000〜3,000文字程度が理想
   - 詳細は別ファイルに分割

4. **生きたドキュメント**として管理
   - 定期的に見直し
   - 不要な情報は削除

### 次のステップ

次の章では、より細かい制御が可能な`settings.json`について学びます。CLAUDE.mdと組み合わせることで、より強力なメモリシステムを構築できます。
