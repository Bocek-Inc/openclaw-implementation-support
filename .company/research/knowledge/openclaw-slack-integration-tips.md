# OpenClaw Slack連携 Tips

## スレッド返信の徹底

OpenClawからSlackへメッセージを送信する際、**必ず `threadId` を指定する**こと。指定しないとチャンネルに直投稿されてしまい、会話が散らかる。

### ルール
1. 受信メッセージに `topic_id` があれば → それを `threadId` に使う（既存スレッド内への返信）
2. `topic_id` がなければ → `message_id` を `threadId` に使う（新規スレッド作成）
3. `threadId` なしでの送信は絶対NG

### よくあるミス
- `message` ツールで `threadId` パラメータを付け忘れる
- 複数回返信する際に最初のメッセージIDを使い回してしまう

## セッション管理

- `session_status` でトークン使用量・コストを確認できる
- モデルの切り替えも `session_status` から可能

## cronジョブによる定期実行

OpenClawのcronジョブ機能で、日報投稿やチェックタスクを自動化できる。

### 設定例（日報）
```
openclaw cron add --schedule "0 23 * * *" --task "日報を作成して #channel に投稿" --model anthropic/claude-sonnet-4-20250514
```

### オプション
- `--schedule`: cron式（分 時 日 月 曜日）
- `--task`: 実行するタスクの指示文
- `--model`: 使用モデル（省略時はデフォルト）
- `--channel`: 出力先チャンネル

### 注意点
- cronジョブはメインセッションとは独立して動く
- 正確なタイミングが必要な場合はcron、柔軟でいい場合はheartbeatを使い分ける
