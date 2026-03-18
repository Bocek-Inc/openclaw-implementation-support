# 対応プラットフォーム

## メッセージプラットフォーム（30+チャネル）

### 確認済み対応チャネル一覧
BlueBubbles, Discord, Feishu, Google Chat, iMessage, IRC, LINE, Matrix, Mattermost, Microsoft Teams, Nextcloud Talk, Nostr, Signal, Synology Chat, Slack, Telegram, Tlon, Twitch, WhatsApp, Zalo, Zalo Personal

- **マルチチャネル対応**: 複数チャネルの同時運用が可能
- **公式ドキュメント**: https://docs.openclaw.ai/channels/
- [出典: 公式ドキュメント, 動画1]

## 実際の連携事例

### Telegram
- BotFatherでボット作成 → APIトークン取得 → OpenClawに接続
- ペアリングコードで自分のユーザーIDを許可リストに登録
- 手順自体はスムーズ
- [出典: 動画1, デモ知見]

### Slack
- 動画2の作者がSlackを採用（仕事で使い慣れているため）
- OpenClaw専用Slackワークスペースに開発用・マーケ用チャンネルを作成
- 各チャンネルに専用エージェントを配置可能
- [出典: 動画2]

### Raspberry Pi + Slack
- Raspberry PiにOpenClawをインストールしSlack連携した実験例
- Slackで議事録を送ると、スケジュールを自動調整してくれた
- [出典: 動画3]

## 音声入出力
- STT: OpenAI Whisper利用
- TTS: Edge TTS（無料、74言語、300+音声）
- macOS/iOS/Android音声入出力対応
- [出典: 動画1, 公式ドキュメント]

## Google Workspace連携
- Gmail/カレンダー/Drive/Sheets/Docs/People APIに対応
- Google Cloud ConsoleでOAuth設定、所要10〜15分
- GOGスキル（Google Workspace CLI）で統合的に操作
- [出典: 動画1, 公式ドキュメント]

## その他の機能
- Canvas機能（ライブCanvasのレンダリングと操作）
- マルチエージェントルーティング（複数エージェントの使い分け）
- [出典: 公式ドキュメント]

## 不足情報
- [ ] 各プラットフォームの連携手順詳細
- [ ] iMessage連携の詳細（macOS限定）
- [ ] LINE連携の詳細手順

---
*最終更新: 2026-03-16（公式ドキュメントから30+チャネル一覧追記）*
