# OpenClaw × GitHub 開発チーム支援パターン

## 概要
開発チームがSlackを使ってOpenClawにGitHubの作業を依頼するパターン。
コードレビュー・PR作成・技術アドバイスなどをSlackメンションだけで完結させる。

---

## パターン1: SlackメンションからのPRレビュー・Approve

### ユースケース
開発者がSlackで `@クロ https://github.com/org/repo/pull/XXXX` のように
PR URLを貼るだけでレビューを依頼できる。

### フロー
1. Slackでメンション受信 → `github` スキル読み込み
2. `gh pr view` でPRの概要・変更内容を取得
3. `gh pr diff` でdiffを解析
4. レビューコメントを `/repos/{owner}/{repo}/pulls/{pull_number}/reviews` に投稿
5. ApproveまたはRequest Changesをスレッド内で報告

### 実例（2026-04-08）
- taskhub-neo-frontend PR#3334（クレジット管理ソート機能）を依頼 → diffを読みapprove
- fukiさんのapprove後のdiff（SortIconコンポーネントの共通化）を重点確認し「ロジック変更なし」と判断

### 導入Tips
- `github` スキルを利用（SKILL.md に `gh` CLI経由でのレビュー手順が記載）
- 大規模PRはdiffが大きくなるためトークンに注意（diffの先頭から読む戦略が有効）
- `GH_TOKEN` 環境変数で認証。`gh auth login` 不要

---

## パターン2: SlackからのコードFix・PR作成

### ユースケース
SlackでコードのURLや問題点を共有するだけで、修正→コミット→PR作成まで完結。

### 実例（2026-04-08）
- Yohei Yamaguchiさんが `OrganizationAvailableCreditsController` のコードURLをSlackに貼り付け
- Swagger `@ApiResponses` に200レスポンス定義が欠けていることを検出
- 他のControllerのパターンを参照してfix → コミット → PR#1038 作成まで自動実行

### フロー
1. Slackでコード/ファイルURLを受信
2. ファイルを直接読み込み（`gh` CLIまたは `read` tool）
3. 既存パターン調査 → 修正 → `git checkout -b fix/xxx` → コミット → `gh pr create --draft`
4. PR URLをスレッドに返信

### 導入Tips
- ブランチ命名規則・PRテンプレートはリポジトリのルールに従うよう TOOLS.md に記載しておく
- `--draft` フラグを付けてPRを作成するルールを設定しておくと安全

---

## パターン3: 開発デバッグアドバイス

### ユースケース
開発者がSlackで「〇〇のテスト方法がわからない」と相談 → OpenClawが具体的なデバッグ方法を提案

### 実例（2026-04-08）
- 「無限スクロールのテストに900件のseederが必要？」という相談
- OpenClawの回答: 「`limit=2〜3` にするだけで少ないデータで複数ページが発生する。900件seedは不要」
- 代替案も提示: MSWでのAPIモック、`rootMargin` を広げてスクロール検知閾値を調整

### 知見
- 開発者が「データを大量用意しなければ」と思い込んでいる場面で、**パラメータ調整で解決できることを指摘**できる
- ローカル環境への負荷軽減（大量seeder → CIで爆死するリスクも回避）
- このような「実は簡単な解決策」を素早く提示できるのがAIエージェントの強み

---

## Cronジョブの分離設計パターン

### 背景
usage-aggregate（深夜23:55実行）とdaily-report（翌朝8:00実行）を別々のcronジョブとして設計。

### なぜ分離するのか
| 項目 | 一体化 | 分離 |
|------|--------|------|
| タイムアウトリスク | 高い（処理が長くなる） | 低い（各ジョブが短い） |
| デバッグ | 難しい | 簡単（どのステップで失敗したか明確） |
| 再実行 | 全部やり直し | 失敗したステップだけ再実行可能 |
| 再利用 | 難しい | usage-aggregateを別の報告にも使える |

### 推奨構成
```
23:55 - usage-aggregate: セッションログ集計 → memory/usage/YYYY-MM-DD.json 保存
08:00 - daily-report: usage JSONを読み込み → 日報生成 → Slackに投稿
```

### Tips
- 集計処理（データ収集）と報告処理（アウトプット）を分離すると保守しやすい
- cronジョブは単一責任原則（SRP）で設計する
