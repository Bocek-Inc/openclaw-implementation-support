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

## 実用パターン: 2ステップCron設計（単一責任の原則）

複数のステップが必要なタスクは **Cronを分割する** と保守しやすい。

```
# ステップ1: データ集計（軽量・高頻度）
openclaw cron add --name "usage-aggregate" --cron "55 23 * * *" ...
  --message "aggregate-usage.pyを実行してください"

# ステップ2: 日報生成・投稿（集計済みデータを使う）
openclaw cron add --name "daily-report" --cron "0 8 * * *" ...
  --message "前日のusage/*.jsonを読んで日報を作成してください"
```

**メリット:**
- 集計だけ失敗した場合に日報ジョブへの影響がない
- 各ジョブが単純になりデバッグしやすい
- 集計データを他のジョブでも再利用できる

## HEARTBEAT.mdでのPR監視パターン（2026-04-12確認）

重要なPRの状態監視を HEARTBEAT.md に記載しておくと、heartbeat のたびに状態を確認できる。

```markdown
# HEARTBEAT.md
## 監視中のPR
- OpenClaw PR: https://github.com/.../pull/XX → マージされたら通知
```

heartbeat実行時にPRのopen/closed状態を確認し、変化があればSlackに通知するパターン。cronより軽量で、「クローズされたら終了」系のタスクに向いている。

## 関連
- [OpenClaw CLI docs](https://docs.openclaw.ai/cli/cron)
- HEARTBEAT.mdとの使い分け: 正確なタイミング → cron、バッチ処理 → heartbeat
