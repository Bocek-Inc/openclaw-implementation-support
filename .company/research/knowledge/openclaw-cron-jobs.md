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

## 関連
- [OpenClaw CLI docs](https://docs.openclaw.ai/cli/cron)
- HEARTBEAT.mdとの使い分け: 正確なタイミング → cron、バッチ処理 → heartbeat
