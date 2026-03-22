---
topic: OpenClaw × Microsoft Teams連携調査
status: completed
requested_by: オーナー
date: 2026-03-19
---

# OpenClaw × Microsoft Teams 連携調査

## 結論

**OpenClawはTeamsで使えます。** ただし、プラグインの追加インストールとAzure Botの設定が必要です。

```bash
openclaw plugins install @openclaw/msteams
```

## セットアップに必要なもの

| 項目 | 内容 |
|------|------|
| Azureの準備 | Azure Bot の登録（App ID, Client Secret, Tenant ID） |
| Teams App Manifest | Botの構成とスコープ設定 |
| 公開URL | OpenClaw Gatewayを外部公開（HTTPS、デフォルトport 3978） |
| 設定 | `channels.msteams` に `appId`, `appPassword`, `tenantId` などを設定 |

## 制限事項

- チャンネル/グループの画像・ファイル内容の取得は不可
- SharePoint/OneDriveに保存された添付ファイルのダウンロードは不可
- ライブWebhookイベント以外のメッセージ履歴の読み取りは不可

## 補足: Teams以外の対応プラットフォーム

OpenClawは20以上のプラットフォームに対応している。

| プラットフォーム | 対応状況 | 備考 |
|------------------|----------|------|
| WhatsApp | コア対応 | Baileys使用、QRペアリング |
| Telegram | コア対応 | Bot API |
| Discord | コア対応 | サーバー/チャンネル/DM対応 |
| Slack | コア対応 | Bolt SDK |
| LINE | 対応 | - |
| Microsoft Teams | **プラグイン対応** | 別途インストール必要 |
| Google Chat | コア対応 | - |
| WebChat | 対応 | ブラウザベース（最も手軽） |

※ その他 Signal, iMessage, IRC, Feishu, Mattermost, Matrix 等も対応

## Teamsセットアップが困難な場合の代替

- **WebChat**: ブラウザ経由でアクセス（最も簡単）
- **Slack連携**: TeamsとSlackを橋渡し
- **Discord**: 他プラットフォームで運用

## ネクストアクション

- [ ] クライアントのTeams環境でのAzure Bot登録の可否確認
- [ ] セットアップ手順の詳細化（必要に応じて）

## 出典

- [OpenClaw 公式ドキュメント - Teams](https://docs.openclaw.ai/channels/msteams)
- [OpenClaw Chat Channels 一覧](https://docs.openclaw.ai/channels)
- [OpenClaw Microsoft Teams Plugin Setup](https://www.maxpetrusenko.com/blog/openclaw-microsoft-teams-plugin-setup-and-troubleshooting)
