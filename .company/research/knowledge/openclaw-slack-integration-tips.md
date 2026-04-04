# OpenClaw Slack連携の注意点・Tips

## 最重要: スレッド返信の徹底

### 問題
OpenClawの `message(action=send)` で Slack にメッセージを送る際、`threadId` を指定しないとチャンネルに直投稿になってしまう。

### 原因
- `threadId` パラメータの設定漏れ
- 特にエージェントが複数ステップの処理を行う際に忘れやすい

### 対策
メッセージ送信時に必ず以下のルールを適用:

1. 受信メッセージに `topic_id`（= 既存スレッドのts）があればそれを `threadId` に使う
2. `topic_id` がなければ `message_id`（= 受信メッセージ自体のts）を `threadId` に使う
3. **`threadId` なしで `message(action=send)` を呼ぶことは絶対禁止**

### 設定例

```json
{
  "action": "send",
  "channel": "slack",
  "target": "C0AMMKLQZ2B",
  "threadId": "1234567890.123456",
  "message": "返信内容"
}
```

## メッセージ読み取り

```json
{
  "action": "read",
  "channel": "slack",
  "target": "CHANNEL_ID",
  "around": "MESSAGE_TS",
  "limit": 10
}
```

- `around`: 指定tsの前後のメッセージを取得
- `threadId`: スレッド内のメッセージを取得

## リアクション

```json
{
  "action": "react",
  "channel": "slack",
  "messageId": "MESSAGE_TS",
  "emoji": "thumbsup"
}
```

## session_statusでのトークン確認
- `session_status` ツールで現在のセッションのtoken数（in/out）が確認可能
- ただし日次の合計使用量を集計する機能はOpenClawには現状ない
- 継続的にトラッキングしたい場合は、セッションごとにメモリファイルに記録する運用が必要

## 学んだ教訓
- Slackでのスレッド返信は**最も基本的なルール**だが、AIエージェントが最も忘れやすいポイントでもある
- MEMORY.mdやAGENTS.mdに強い言葉でルールを書いておくことで再発防止になる
- cronジョブの `--announce` オプションで投稿される場合もスレッド管理に注意

## ⚠️ 既知の落とし穴: Slackイベント重複によるGitHub APIの二重発行 (2026-04-04確認)

### 問題
Slackの「チャンネルメッセージ」と「スレッド通知」が同一メッセージに対して両方届いた場合、OpenClawが2つの別イベントとして処理してしまい、GitHub API（例: PR Approve）が2回呼ばれる。

GitHub APIの `POST /repos/{owner}/{repo}/pulls/{pull_number}/reviews` は冪等でないため、同じPRに2件のApproveが作成される。

### 影響
- PRに同一ユーザーのApproveが重複して表示される
- レビュー履歴が汚染される

### 根本原因
- OpenClaw側のメッセージdeduplication（重複排除）が未実装
- Slackのイベント配信がチャンネルメッセージ+スレッド通知の両方をトリガーする仕様

### 暫定対策（導入支援時の推奨）
1. **べき等チェックをプロンプトに組み込む**: 「同じPRにすでにApproveが付いていないか確認してから実行する」という指示をSOUL.mdやAGENTS.mdに追加
2. **GitHub API呼び出し前に確認ステップを挟む**: `gh pr view --json reviews` で既存レビューを確認してから実行
3. OpenClaw本体のdeduplication対応を開発チームにフィードバック（issue #52536 関連）
