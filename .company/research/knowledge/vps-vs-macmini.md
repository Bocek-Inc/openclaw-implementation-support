# OpenClaw動作環境比較: VPS vs Mac mini

## 結論

- **まず試したい** → VPS（初期投資ゼロ、合わなければ解約）
- **本格的に日常運用したい** → Mac mini（直感的に操作できる、長期コスト安い、ローカルファイルも使える）
- **どちらも専用機として隔離される**ため、セキュリティ面は大きな差はない（ただしMac miniは社内NW上にある点は留意）

---

## 比較表

| 比較項目 | Mac mini（M4 16GB） | VPS（Hostinger KVM2） |
|---------|---------------------|----------------------|
| **初期コスト** | 94,800円（税込） | 0円 |
| **月額コスト** | 電気代 約100〜300円 | 約1,050円（$6.99/月） |
| **損益分岐点** | 約9年で逆転（月額差850円） | 短期〜中期はVPSが有利 |
| **セットアップ** | ターミナルで直接操作 | WebUI + SSH |
| **WebUI** | なし（ターミナル直接） | 環境変数・チャネル接続程度 |
| **細かい設定** | ターミナルでそのまま可能 | SSH接続が必要 |
| **ローカルファイル操作** | ✅ 直接アクセス可能 | ❌ サーバー内のみ |
| **iMessage連携** | ✅ macOS限定機能 | ❌ 不可 |
| **ローカルLLM** | ✅ M4で40+トークン/秒 | ❌ GPU VPSが必要（$100+/月） |
| **スマートホーム制御** | ✅ ローカルNW通信可能 | ❌ 不可 |
| **AppleScript連携** | ✅ macOSアプリ自動操作 | ❌ 不可 |
| **稼働安定性** | macOSアップデート・停電リスク | DC運用で高安定 |
| **スケーラビリティ** | メモリ増設不可（はんだ付け） | ワンクリックでRAM増設可 |
| **セキュリティ隔離** | ✅ 専用機として隔離（社内NW上） | ✅ 完全に別ネットワーク |
| **トラブル時の対応** | その場で直接確認・修正できる | SSH接続して確認 |

## Mac miniが向いている人

- ローカルファイルを直接操作したい（Excel集計、コードリポジトリ直接操作等）
- macOS限定機能（iMessage、AppleScript、スマートホーム）を使いたい
- 長期（1年以上）運用する予定
- ターミナルで直接操作できる手軽さを重視
- ローカルLLMを試したい（API料金削減・プライバシー重視）

## VPSが向いている人

- まずは低コストでOpenClawを試したい
- 物理障害（停電、macOSアップデート）を気にしたくない
- マルチエージェントへのスケールアップを想定している
- ネットワークレベルでの隔離を重視する

## HostingerのWebUIでできること・できないこと

| できること | できないこと（SSH必要） |
|-----------|---------------------|
| 環境変数の設定・編集 | 設定ファイルの直接編集 |
| チャットチャネルの接続 | パーミッション変更 |
| ゲートウェイトークン認証 | カスタムスクリプトの実行 |
| QRコード表示（チャネル接続用） | ログの詳細確認 |
| Dockerコンテナ管理（起動/停止/更新） | ファイアウォール設定 |
| ログ閲覧（基本） | cronジョブの細かい設定 |

## コスト詳細

### Mac mini
- 本体: 94,800円（M4 16GB / 256GB、Apple公式）
- 電気代: アイドル5-7W、ワークロード8-15W → 月100〜300円
- LLM API: 別途（Anthropic pay-as-you-go）

### VPS（Hostinger）
- KVM1: $4.99/月（1 vCPU / 4GB RAM / 50GB SSD）
- KVM2: $6.99/月（2 vCPU / 8GB RAM / 100GB SSD）← OpenClaw推奨
- KVM4: 高額（ローカルLLM用、GPU必要な場合$100+/月）
- LLM API: 別途（同上）

## 出典
- Mac mini価格: [Apple公式](https://www.apple.com/jp/shop/buy-mac/mac-mini)
- Hostinger: [OpenClaw VPS Hosting](https://www.hostinger.com/vps/openclaw-hosting)
- 消費電力: [BoilerplateHub](https://boilerplatehub.com/blog/openclaw-mac-mini)
- VPS比較: [Analytics Vidhya](https://www.analyticsvidhya.com/blog/2026/02/openclaw-hosting-mac-mini-vs-cloud-vps/)
- Hostinger WebUI: [Hostinger Support](https://www.hostinger.com/support/how-to-install-openclaw-on-hostinger-vps/)

---
*最終更新: 2026-03-21（Mac mini記事執筆時の調査結果を統合）*
