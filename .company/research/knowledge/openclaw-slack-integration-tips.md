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

---

## CIポーリング → レビュー依頼の自動フロー (2026-04-09)

### パターン
SlackでPRレビュー依頼を受けた際、CIが完了していない場合に自動ポーリングしてからチームへのレビュー依頼を追加するフロー。

### フロー
1. `gh pr checks <PR番号>` でCIステータス確認
2. IN_PROGRESSならN分待機 → 再チェック（最大X回）
3. SUCCESS/SKIPPEDになったら `gh pr edit --add-reviewer Bocek-Inc/dev1` でレビュー依頼
4. Slackスレッドに完了通知

### ポイント
- subagentを使うことで10分超のポーリングもタイムアウトせずに処理可能
- CI完了後に初めてレビュー依頼を送ることでレビュアーの負担を減らせる
- 「CIが終わったら教えて」という依頼もOpenClawで完結できる

### 実績
PR #3355（taskhub-neo-frontend）で実証。5回・約10分間のポーリング後、自動でdev1チームへのレビュー依頼を追加。

---

## octocov + GitHub Artifact によるカバレッジ可視化 (2026-04-09)

### 背景
カバレッジの可視化にCodecov（外部SaaS）を使うべきか相談を受けた際の知見。

### 判断基準
| ニーズ | 推奨 |
|--------|------|
| PRコメントでの差分確認だけ欲しい | octocovで十分 |
| 長期的なトレンド・履歴が見たい | Codecov追加を検討 |
| OSS・チーム全員に公開したい | Codecov |
| コストを抑えたい | octocov + GitHub Artifact |

### octocov + Artifact パターン
```yaml
# test.ymlに追加するだけでHTMLレポートをCI上で閲覧可能
- name: Upload coverage HTML report
  uses: actions/upload-artifact@v4
  with:
    name: coverage-report
    path: coverage/lcov-report/
    retention-days: 7
```

### ポイント
- octocovは既にPRコメントでカバレッジ差分を表示できる（Codecovと重複）
- Codecovは「カバレッジ推移グラフ」「バッジ表示」「PR必須設定（branch protection）」が強み
- 小規模チームはoctocov + GitHub Artifactで十分なケースが多い
- AI（OpenClaw）がCI設定ファイルを読んで具体的なYAML修正案を提示するユースケースとして有効
