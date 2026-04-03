# OpenClaw GitHub PR操作の知見・落とし穴

## 概要

OpenClawがSlackのリクエストに応じてコードを修正しPRを作成する際の実運用知見をまとめる。

---

## 落とし穴1: ブランチ起点ミス

### 問題
`git checkout -b <new-branch>` を実行する前に現在のブランチを確認しないと、誤ったブランチ（例: 他メンバーのfeatureブランチ）を起点にしてしまう。

### 症状
- PRに不要なコミットが大量に含まれる
- レビュアーが本来の変更点を特定できない

### 対策
```bash
# 必ず main (or develop) から切る
git fetch origin
git checkout main
git pull origin main
git checkout -b fix/your-fix-name
```

### リカバリ手順
1. 誤ったPRをクローズ
2. `main` から新しいブランチを作成
3. `git cherry-pick <commit-hash>` で正しいコミットだけ持ち込む
4. 新PRを作成

```bash
git checkout main && git pull
git checkout -b fix/correct-branch
git cherry-pick <commit-hash>
git push origin fix/correct-branch
```

---

## 落とし穴2: GitHub Assignees の設定制限

### 問題
`gh pr create --assignee <username>` は、そのユーザーがリポジトリへのWrite権限を持っていない場合にエラーとなりAssignee設定が失敗する。

### 対応
- PRを作成後に手動でAssigneeを設定してもらうよう依頼する
- または `gh api` でorganizationメンバーシップを確認してから設定する

---

## Tips: 例外ログレベルの適切な設定

### ユースケース
OpenClawがLaravelのエラーログ設定変更を自動化する際の知見。

### 考え方
- `404 Not Found` 系・アクセス制限系の業務例外 → `WARNING` が適切
- インフラ障害・DB接続エラー → `ERROR` または `CRITICAL`
- `ERROR` で設定すると監視アラートが頻発してノイズになる

### 実装例 (Laravel)
```php
// ExceptionHandler.php
public static function handle(Exceptions $exceptions): void
{
    // 業務例外はWARNINGに下げてアラートノイズを減らす
    $exceptions->level(ResourceNotFoundException::class, LogLevel::WARNING);
    $exceptions->level(AccessDeniedException::class, LogLevel::WARNING);
}
```

---

## Tips: OpenClawによるPR自動作成フロー

### 典型的なフロー
1. Slackで依頼受信（チャンネル: `#030_openclaw_request` など）
2. 対象ファイルを特定・修正
3. `main` から新ブランチを切る
4. コミット・プッシュ
5. `gh pr create` でPR作成
6. SlackスレッドにPR URLを返信

### 注意点
- PR作成後に必ずdiffを確認する（予期しない差分が含まれていないか）
- Assigneeは事前にGitHubユーザー名を確認しておく
- ブランチ名は `fix/` `feat/` `chore/` などプレフィックスを付ける

---

*最終更新: 2026-04-03*
