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

## ストリーミング設定とスレッド返信の二重投稿問題

### 症状
`streaming: "partial"` + `nativeStreaming: true` の設定で、スレッドへの**最初の返信**がチャンネル直下にも投稿されてしまう（二重投稿）。2回目以降の返信は正常にスレッド内に配信される。

### 原因
初回のスレッド返信時はまだスレッドセッションが確立されておらず、ストリーミングプレビュー（Slack Agents API経由）がチャンネル直下に配信される。最終レスポンスは `thread_ts` 付きでスレッドに配信されるため、結果的にチャンネルとスレッドの両方にメッセージが出現する。

### 対策
```json
{
  "channels": {
    "slack": {
      "streaming": "off",
      "nativeStreaming": false
    }
  }
}
```
設定変更後は `openclaw gateway restart` で反映。

### 補足
- OpenClaw本体のバグとして報告済み: https://github.com/openclaw/openclaw/issues/52536
- ストリーミングをオフにするとリアルタイムプレビューは無くなるが、最終レスポンスは正常に配信される
- 将来のバージョンで修正されたら `streaming: "partial"` に戻してOK

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
