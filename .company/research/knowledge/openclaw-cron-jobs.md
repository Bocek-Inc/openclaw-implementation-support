# OpenClaw Cronジョブ設定

## 概要
OpenClawにはcronスケジューラが内蔵されており、定期的なタスクを自動実行できる。Gateway経由で管理。

## 基本コマンド

```bash
# ジョブ一覧
openclaw cron list

# ジョブ追加
openclaw cron add --name "job-name" --cron "0 8 * * *" --tz "Asia/Tokyo" ...

# ジョブ編集
openclaw cron edit <job-id> --message "新しいメッセージ"

# ジョブ削除
openclaw cron rm <job-id>

# 手動実行（デバッグ用）
openclaw cron run <job-id>
```

## 主要オプション

| オプション | 説明 |
|-----------|------|
| `--cron <expr>` | 5フィールドcron式 (分 時 日 月 曜日) |
| `--tz <iana>` | タイムゾーン (例: Asia/Tokyo) |
| `--exact` | staggerを0にして正確な時刻で実行 |
| `--channel <ch>` | 配信先チャンネル種別 (slack, telegram等) |
| `--to <dest>` | 配信先ID (Slackチャンネル等) |
| `--announce` | 結果をチャットに投稿 |
| `--session <target>` | `main` or `isolated` |
| `--message <text>` | エージェントへの指示 |
| `--timeout-seconds <n>` | タイムアウト |

## 実用例: Slack日報の定期投稿

```bash
openclaw cron add \
  --name "daily-report" \
  --cron "0 8 * * *" \
  --tz "Asia/Tokyo" \
  --exact \
  --channel slack \
  --to "C0AMMKLQZ2B" \
  --announce \
  --session isolated \
  --timeout-seconds 60 \
  --message "日報を作成してください..."
```

## 注意点
- `--message` 内でシェル変数（`$(date ...)`）を使うと**追加時に展開されてハードコードされる**。動的な日付が必要な場合はメッセージ内でエージェントに計算させる指示にする
- `--session isolated` にするとメインセッションのコンテキストと分離される
- `--exact` をつけないとデフォルトでstagger（ランダム遅延）が入る

## 実用例: 使用量集計を独立cronで分離する設計（2026-03-28 追記）

日報cronとは別に、軽量な使用量集計cronを独立させることで責務を分離できる。

```bash
openclaw cron add \
  --name "usage-aggregate" \
  --cron "55 23 * * *" \
  --tz "Asia/Tokyo" \
  --exact \
  --channel slack \
  --to "CHANNEL_ID" \
  --session isolated \
  --timeout-seconds 30 \
  --message "python3 /path/to/scripts/aggregate-usage.py を実行してください。結果をログに出力するだけでOK。Slackへの投稿は不要。"
```

### なぜ分離するか
- 日報cronはセッションログ解析・Slack投稿・PR作成など重い処理を含む（タイムアウトリスクあり）
- 使用量集計は純粋にスクリプト実行のみで完結する → 軽量・高信頼
- 集計データを事前に生成しておくことで、翌朝の日報cronがファイルを読むだけで済む（冪等性の確保）

### 設計パターン
1. 23:55 に `usage-aggregate` cronが当日分を `.json` に保存
2. 翌8:00 に `daily-report` cronが `.json` を読んで日報に含める
3. ファイルがなければ日報cron内でスクリプトを実行するフォールバックも持たせる

## ハートビートでGitHub Issue監視（2026-03-28 追記）

ハートビート処理内で `gh issue view <number>` を実行することで、特定Issueの状態を定期チェックできる。

```markdown
# HEARTBEAT.md 記載例
- Issue #52536 が Open のままか確認 (`gh issue view 52536 -R owner/repo`)
  → Close されていたら #alert-channel に通知
```

### ユースケース
- 重要バグIssueの解消を監視してSlack通知
- 外部ベンダーへの問い合わせIssueの返答待ち監視
- リリースブロッカーの状態監視

### 注意
- heartbeatは主セッションのコンテキストが引き継がれるため、監視結果をmemoryに書いてセッション間で状態を保持できる
- ただし`gh` CLIへのアクセスが必要なため、実行環境に `GH_TOKEN` が設定されていること

## 関連
- [OpenClaw CLI docs](https://docs.openclaw.ai/cli/cron)
- HEARTBEAT.mdとの使い分け: 正確なタイミング → cron、バッチ処理 → heartbeat
