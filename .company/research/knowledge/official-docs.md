# 公式ドキュメント・GitHub要約

## GitHub統計
- **スター数**: 250,829+（2026年3月3日時点）
- **コントリビューター**: 1,075人以上（毎週1,000人以上がコード貢献）
- **Forks**: 58,000+
- **ライセンス**: MIT License
- **記録**: 公開から約60日でReactを超え、GitHub史上最多スターのソフトウェアプロジェクト
- **創設者**: Peter Steinberger（後にOpenAI参加、プロジェクトをオープンソース財団に移管）
- **リポジトリURL**: https://github.com/openclaw/openclaw
- [出典: Web検索 2026-03-16]

## 公式ドキュメント構成

**メインサイト**: https://docs.openclaw.ai/

| セクション | 主要ページ | URL |
|----------|-----------|-----|
| スタート | Getting Started, Wizard, Quick start, Hubs | /start/ |
| Web UI | Control UI | /web/control-ui |
| ゲートウェイ | Configuration, Security, Remote access, Tailscale, Troubleshooting | /gateway/ |
| チャネル | Telegram, Slack, LINE等（30+） | /channels/ |
| コンセプト | Features, Multi-agent routing, Agent Runtime, Models CLI, Session Tools | /concepts/ |
| ツール | Skills, Skills Config, Creating Skills, ClawHub, Web Tools, Slash Commands, Plugins | /tools/ |

**その他の公式リソース**:
- https://openclaw.ai/ （メインサイト）
- https://www.getopenclaw.ai/en/docs （代替ドキュメント）
- https://docs.openclaw.ai/llms.txt （ドキュメント完全インデックス）
- [出典: 公式サイト調査 2026-03-16]

## アーキテクチャ
- **ハブ&スポーク型**: 単一のGatewayプロセスがメッセージングアプリとAIエージェントを接続
- **Agent Runtime**: pi-mono由来の組み込みエージェントランタイム
- **設定ファイル**: `~/.openclaw/openclaw.json`（JSON5形式）
- **デフォルトポート**: 18789
- **ホットリロード**: hybrid/hot/restart/off の4モード
- [出典: 公式ドキュメント]

## 設定リファレンス（openclaw.json主要セクション）
- **agents**: workspace、model、heartbeat、sandbox設定
- **channels**: チャネル別のenabled, dmPolicy, allowFrom, groupPolicy, healthMonitor
- **session**: dmScope（main/per-peer/per-channel-peer/per-account-channel-peer）、reset設定
- **gateway**: port, reload, push(APNs), channelHealthCheckMinutes
- **tools, hooks, cron**: Webhook、定期ジョブ、外部ツール統計
- **env**: 環境変数（`${VAR_NAME}`形式で参照可能）
- [出典: 公式ドキュメント]

## コスト情報
- **モデルティア**: Tier 1（Opus等: 1プロンプト2-6ドル）、Tier 2（Sonnet等: 中程度）、Tier 3（Haiku等: Opusの1/25）、無料（NVIDIA等）
- **3大コスト落とし穴**: 全処理をTier 1で実行、コンテキスト肥大化、過剰な自律実行
- **GPT-5.4対応**、マルチモデル（Claude、GPT、Ollama経由ローカルモデル）
- **重要**: 2026年1月にAnthropicがOAuth廃止 → Anthropic APIキー（pay-as-you-go）が唯一の接続方法
- [出典: 公式ドキュメント, Web検索]

---
*最終更新: 2026-03-16（公式ドキュメント・GitHub調査実施）*
