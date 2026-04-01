# OpenClaw × GitHub PR自動化の運用知見

## 概要

OpenClawをSlack連携と組み合わせることで、自然言語の指示からGitHub PR作成・レビュー対応まで自動完結できる。実際の開発チームでの運用事例をベースに知見をまとめる。

---

## パターン1: Slack指示 → PR作成の自動完結

### ユースケース
Slackで「〇〇の設定を変更してPR出して」と依頼 → OpenClawがリポジトリを確認・変更・PR作成まで実行

### 実例（2026-04-01）
- **依頼**: `#030_openclaw_request` チャンネルで「agepoyo と taskhub-monorepo に Dependabot cooldown設定を追加して」
- **OpenClawの動作**:
  1. 両リポジトリの `.github/dependabot.yml` を確認
  2. cooldown設定を追記
  3. 各リポジトリにPR作成（agepoyo#691, taskhub-monorepo#972）
  4. Slack スレッドに PR URLを返信

### 設定のポイント
- `#030_openclaw_request` のような「依頼専用チャンネル」を設けると整理しやすい
- 複数リポジトリへの同時適用も1回の指示で対応可能
- Assignee設定は `gh pr create --assignee <user>` で行うが、GitHubユーザー名が必要

---

## パターン2: PRレビューコメントへの自動対応

### ユースケース
レビュアーがPRコメントで修正指摘 → OpenClawがコメントを読んで修正対応

### 実例（2026-04-01）
- **状況**: PR#977（Free plan seeder追加）に対してレビュアー（OhVIton）が「Keycloakの方も登録いる」とコメント
- **OpenClawの動作**:
  1. PRのレビューコメントを確認 (`gh pr view --comments`)
  2. 該当ファイル (`mysql/seeder/keycloak/1-user.sql`) を確認
  3. Keycloakユーザー登録を追加して同PRに push
- **効果**: レビュー指摘を見落とさず、人間が気づかなくても自動で対応

### 落とし穴・注意点
- 複数のDBを持つシステム（例: appDB + Keycloakなど）では、1か所の変更が他DBにも影響することがある
- PRレビューコメントを取得するには `gh pr view <number> --comments --repo <owner/repo>` を使う
- コメント対応後は必ずスレッドに「対応しました」と報告する（サイレント修正はNG）

---

## パターン3: ベストプラクティスの能動的提案

### ユースケース
単に指示通り実装するだけでなく、一般的なベストプラクティスを調べて提案する

### 実例（2026-04-01）
- **状況**: Dependabot cooldownの値として `patch: 7日, minor: 30日, major: 90日` と設定したが、一般値より長かった
- **OpenClawの動作**: Renovate公式推奨値（patch=3日, minor=7日, major=30日）を調べて「これに変更しますか？」と提案
- **効果**: ユーザーが気づかないベストプラクティスのズレを指摘できる

### Dependabot cooldown設定の参考値
```yaml
# 一般的な推奨値（Renovate公式 minimumReleaseAge 基準）
cooldown:
  patch:   3  # yanked版を踏まない最低限の待機期間
  minor:   7  # 軽微な破壊的変更への対応猶予
  major:   30 # コミュニティ評価が出るまでの待機
  default: 3
```

---

## 導入Tipsまとめ

| Tips | 内容 |
|------|------|
| 依頼チャンネルの設計 | `#request` 系チャンネルを分離すると会話履歴が整理しやすい |
| Assignee設定 | GitHubユーザー名をあらかじめ `TOOLS.md` に記載しておく |
| PRテンプレート | `## 概要` `## 変更内容` の構成で自動生成される |
| レビュー対応 | `gh-issues` スキルや直接 `gh pr view` で定期確認も可能 |
| 複数リポジトリ対応 | 1つのSlack指示で複数リポジトリに同時適用可能 |

---

## 関連ファイル
- `usecases.md` — B-7. GitHub操作の自動化
- `openclaw-slack-integration-tips.md` — Slack連携の基本Tips
