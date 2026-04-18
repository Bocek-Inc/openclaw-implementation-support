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

## HEARTBEAT.mdを使った外部状態の軽量監視パターン

HEARTBEAT.mdにGitHub Issue等の外部状態追跡タスクを書くことで、heartbeatのたびに自動チェックできる。

### ユースケース例: GitHub Issueのクローズ監視

```markdown
## 定期チェック
- [ ] openclaw/openclaw#12345 がクローズされたら、しゅーへいさんに報告してから設定変更を依頼する（自動変更NG）
```

エージェントはheartbeatのたびに `gh issue view openclaw/openclaw#12345 --json state` を実行し、OPEN→CLOSEDになったら通知する。

### なぜcronではなくHEARTBEATか
- 「クローズされたら1回だけ動く」という条件トリガー型のタスクはcronより向いている
- heartbeatは30分おき程度で自然に実行されるため、polling間隔として適切
- cronはタイミング固定・繰り返し向き、heartbeatは「状態変化を待つ」向き

### 落とし穴
- HEARTBEAT.mdに書きっぱなしにすると、条件達成後もチェックを続ける可能性がある
- アクション完了後はHEARTBEAT.mdの該当行を削除（または`[x]`完了マーク）する運用にする

### 2026-04-18 実例
`openclaw/openclaw#52536`（Slack streaming thread bug）をHEARTBEAT.mdに記録し、毎heartbeatでghコマンドでステータス確認。クローズ時の通知→設定変更依頼まで自動化する設計。

---

## 関連
- [OpenClaw CLI docs](https://docs.openclaw.ai/cli/cron)
- HEARTBEAT.mdとの使い分け: 正確なタイミング → cron、バッチ処理 → heartbeat
