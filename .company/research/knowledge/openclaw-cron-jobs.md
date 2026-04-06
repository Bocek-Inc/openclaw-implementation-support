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

## 実践知見: cronプロンプトの段階的改善アプローチ（2026-04-06）

日報cronのような定期タスクは、**最小構成で始めてフィードバックを受けながら改善する**のが定着しやすい。

### 実例: Bocek社での日報cron改善履歴
1. **初期**: 基本的な使用量サマリーのみ投稿
2. **改善1**: セッション別コスト詳細をスレッド返信で追加
3. **改善2**: 「やったこと＋人間換算工数＋コスト」形式の詳細まとめに更新

`openclaw cron edit <job-id> --message "新しいプロンプト"` でいつでも変更できるため、
「まず動かす → ユーザーの反応を見る → 改善する」のサイクルを回しやすい。

### ポイント
- 初期は出力が少なくて物足りなく感じるくらいで十分。慣れてから情報を追加する
- プロンプトに「手順X, Y, Z」と番号を振って構造化すると、エージェントが迷わず動く
- cronの出力フォーマット（Slack投稿先・スレッド有無）も `--message` 内に含めて指示する

## 関連
- [OpenClaw CLI docs](https://docs.openclaw.ai/cli/cron)
- HEARTBEAT.mdとの使い分け: 正確なタイミング → cron、バッチ処理 → heartbeat
