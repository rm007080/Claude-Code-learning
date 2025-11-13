# Claude GitHub Appのインストール

## はじめに

この章では、Claude GitHub Appのインストール手順を詳しく解説します。画面の説明とともに、迷わずインストールできるようにガイドします。

## Claude GitHub Appとは

### 概要

**Claude GitHub App**は、GitHubリポジトリとClaude AIを連携させる公式アプリケーションです。

**主な機能:**
- GitHubイベント（Issue作成、PRコメントなど）の監視
- @claudeメンションの検出
- Claude Codeの起動と実行
- 結果の自動コメント

## インストール手順

### ステップ1: インストールページにアクセス

ブラウザで以下のURLを開きます。

```
https://github.com/apps/claude
```

### ステップ2: インストールボタンをクリック

ページ上部にボタンが表示されます。

**初回インストールの場合:**
- **「Install」**ボタンが表示されます

**すでにインストール済みの場合:**
- **「Configure」**ボタンが表示されます
- 新しいリポジトリを追加する場合も、このボタンをクリック

### ステップ3: インストール先の選択

**個人アカウントまたは組織を選択:**

```
Install Claude

Where do you want to install this app?

❯ your-username (Personal account)
  your-organization (Organization)
```

**推奨:**
- 個人プロジェクト → 個人アカウント
- チームプロジェクト → 組織アカウント

### ステップ4: リポジトリの選択

**重要:** セキュリティの観点から、必要なリポジトリのみを選択します。

```
Repository access

○ All repositories
   This applies to all current and future repositories.

● Only select repositories
   Select at least one repository.

   ▼ Select repositories
     ☑ your-username/repo-name-1
     ☑ your-username/repo-name-2
```

**推奨設定:**
- ✅ **「Only select repositories」を選択**
- ✅ 必要なリポジトリのみにチェック

**理由:**
- 最小権限の原則（セキュリティのベストプラクティス）
- 不要なリポジトリへのアクセスを防ぐ
- 後から追加も可能

### ステップ5: 権限の確認

Claude GitHub Appが要求する権限を確認します。

**要求される権限:**

| 権限 | アクセスレベル | 用途 |
|------|--------------|------|
| **Contents** | Read & Write | コードの読み取りと変更 |
| **Issues** | Read & Write | Issueの閲覧とコメント |
| **Pull requests** | Read & Write | PRの閲覧とレビュー |

**これらの権限は適切です:**
- ✅ コードレビューに必要
- ✅ Issueへの回答に必要
- ✅ PRへのコメントに必要

### ステップ6: インストールの完了

**「Install」**ボタンをクリックして、インストールを完了します。

```
[Install] ボタン
```

**成功メッセージ:**
```
✓ Claude has been installed on your-username/repo-name
```

## インストール後の確認

### 方法1: GitHub上で確認

1. GitHubのトップページに戻る
2. 右上のアイコン → **Settings**
3. 左サイドバー → **Integrations** → **Applications**
4. **Installed GitHub Apps** タブ
5. **「Claude」**が表示されていれば成功

### 方法2: 直接URLで確認

```
https://github.com/settings/installations
```

このURLで、インストール済みアプリの一覧が表示されます。

**表示例:**
```
Installed GitHub Apps

Claude
  Installed: Just now
  Repositories: 2 selected
  [Configure] [Uninstall]
```

## インストール設定の変更

後から設定を変更することも可能です。

### リポジトリの追加

1. https://github.com/settings/installations にアクセス
2. **Claude** の **「Configure」**をクリック
3. **Repository access** でリポジトリを追加/削除
4. **「Save」**をクリック

### 具体的な手順

```
1. Configure をクリック
    ↓
2. Repository access
   ● Only select repositories
     ▼ Select repositories
       ☑ existing-repo-1
       ☑ existing-repo-2
       ☐ new-repo-3  ← 追加したいリポジトリにチェック
    ↓
3. [Save] ボタン
```

## セキュリティ設定の確認

### 権限の再確認

定期的に権限を確認することをお勧めします。

**確認項目:**
- [ ] 不要になったリポジトリは削除されているか
- [ ] アクセス権限は適切か
- [ ] 使用していないアプリはアンインストールしているか

### アクセスログの確認

GitHub Actionsのログで、いつClaude GitHub Appが実行されたかを確認できます。

```
https://github.com/your-username/your-repo/actions
```

## 複数リポジトリへのインストール

### 方法1: 初回インストール時に複数選択

インストール時に、複数のリポジトリにチェックを入れます。

```
● Only select repositories
  ▼ Select repositories
    ☑ repo-1
    ☑ repo-2
    ☑ repo-3
```

### 方法2: 後から追加

前述の「リポジトリの追加」手順で、後から追加できます。

### 方法3: All repositories（非推奨）

**全リポジトリにインストール（非推奨）:**
```
○ All repositories
  This applies to all current and future repositories.
```

**非推奨の理由:**
- セキュリティリスク
- 不要なリポジトリへのアクセス
- 将来作成するリポジトリにも自動適用される

**推奨:** 必要なリポジトリのみを個別に選択

## 組織アカウントでのインストール

### 組織の管理者権限が必要

組織のリポジトリにインストールする場合、組織の管理者権限が必要です。

### 組織での承認フロー

一部の組織では、アプリのインストールに承認が必要な場合があります。

**承認フローの例:**
```
1. あなたがインストールをリクエスト
    ↓
2. 組織の管理者に通知が送信される
    ↓
3. 管理者が承認
    ↓
4. インストール完了
```

### 組織設定の確認

組織の設定で、GitHub Appsのインストールポリシーを確認できます。

```
https://github.com/organizations/your-org/settings/oauth_application_policy
```

## トラブルシューティング

### 問題: インストールボタンが表示されない

**原因:**
- GitHubにログインしていない
- ブラウザのキャッシュ問題

**解決策:**
1. GitHubにログインしているか確認
2. ブラウザのキャッシュをクリア
3. シークレットモードで試す

### 問題: リポジトリが表示されない

**原因:**
- リポジトリの管理者権限がない
- プライベートリポジトリへのアクセス権限がない

**解決策:**
- リポジトリの管理者に権限付与を依頼

### 問題: 組織でインストールできない

**原因:**
- 組織の管理者権限がない
- 組織でGitHub Appsが制限されている

**解決策:**
- 組織の管理者に相談
- 承認をリクエスト

## アンインストール

必要に応じて、Claude GitHub Appをアンインストールできます。

### アンインストール手順

1. https://github.com/settings/installations にアクセス
2. **Claude** の **「Configure」**をクリック
3. ページ下部の **「Uninstall」**をクリック
4. 確認ダイアログで **「OK」**

**注意:** アンインストールすると、すべてのリポジトリでClaude GitHub Actionが動作しなくなります。

## まとめ

### 完了したこと

- [x] Claude GitHub Appのインストール
- [x] リポジトリの選択（最小権限の原則）
- [x] 権限の確認
- [x] インストールの確認

### 重要なポイント

1. **最小権限**: 必要なリポジトリのみを選択
2. **確認**: インストール後は必ず確認
3. **柔軟性**: 後から設定変更可能
4. **セキュリティ**: 定期的な見直し

### 次章の内容

次の章では、OAuth Tokenの生成とGitHub Secretsへの追加を行います。これにより、Claude AIとGitHubの連携が完成します。
