# 環境構築（VPS / Mac mini / Docker / Windows）

## 動作環境の選択肢（3つ）

### 1. 自分のPC
- 無料だがPC OFFで停止
- セキュリティリスクあり
- [出典: 動画1]

### 2. 予備ハードウェア（Mac mini等）
- 常時稼働・隔離可能だが初期投資500ドル〜
- 「人間と同じ世界に住むAI」= ユーザーのデスクの隣で動く
- [出典: 動画1, Mac mini比較メモ]

### 3. VPS/サーバー（動画1では推奨）
- 月数ドル〜、24/7、問題がサーバー側で完結
- [出典: 動画1]

## Mac miniの詳細

### 本質的なメリット（VPSとの根本的な違い）
- **VPS**: インターネット上の隔離されたサーバー。「別室に閉じ込められたアシスタント」
- **Mac mini**: ユーザーのデスクの隣で動く。「隣の席に座るアシスタント」

### Mac miniだからこそできること（VPSでは不可）
1. **ローカルファイルに直接アクセス**: 「このフォルダのExcelを集計して」がそのまま通る
2. **iMessage連携**: macOS環境でしか利用できない
3. **ローカルコードリポジトリの直接操作**: cloneせずにそのまま操作
4. **ローカルネットワーク通信**: NAS、スマートホーム等と通信可能
5. **ローカルLLM実行**: API料金ゼロ、データが外部に出ない

### セットアップ所要時間
- 合計約70分（開封〜Telegram連携まで）
- 内訳: macOS初期設定(15-20分) → Docker Desktop(10分) → OpenClaw Docker pull & 起動(10-15分) → 環境変数・APIキー設定(5-10分) → Telegram Bot連携(10分) → 初回ブートストラップ(5-10分)

### 推奨スペック
- **最低**: M4 Mac mini 16GB（$629〜、約9.5万円〜）
- **推奨**: M4 Mac mini 32GB（ローカルLLMをフル活用する場合）
- 16GBモデルなら20BクラスのローカルLLMが動作
- Mac miniが品薄との情報あり
- [出典: 動画3, Mac mini比較メモ]

## VPSの詳細

### Hostingerでのデプロイ
- OpenClaw用テンプレートでワンクリックデプロイ
- プラン目安：KVM1（小規模）、KVM2（推奨）、KVM4（ローカルモデル向け）
- 自動バックアップ推奨（月3ドル程度）
- [出典: 動画1]

### VPSのコスト
- 月額: $5〜12（KVM 1〜KVM 2）
- 初期コスト: 0円
- 1ヶ月プランで試して合わなければすぐ解約可能

### VPSの制約
- サーバー内のワークスペースしか操作できない
- 日本リージョンなし（オランダ・リトアニア・イギリス・アメリカ・シンガポール・ブラジル）
- タイムゾーン: UTC。cronジョブで日本時間を指定するには追加設定が必要
- ローカルLLM実行には高額プラン（KVM 4以上）が必要
- [出典: Mac mini比較メモ, デモ知見]

## セットアップ後の重要ポイント
- ゲートウェイトークン＝マスターキー → パスワードマネージャーに保存
- LLM APIキー設定（Anthropic Claude推奨：40ドルチャージでTier 2）
- 支出ガードレール設定（月額上限、自動リロードOFF）
- [出典: 動画1]

## 初回ブートストラップ
- Bootstrap.mdが起動し、ボットの性格・役割・ユーザー情報を設定
- 短く済ませず、具体的に説明するのがコツ
- 日本語でやりとり可能。OpenClawが自分で名前を決めるキャラクター性
- [出典: 動画1, デモ知見]

## Telegram連携
- BotFatherでボット作成 → APIトークン取得 → OpenClawに接続
- ペアリングコードで自分のユーザーIDを許可リストに登録
- 再起動後に「LLM request rejected: thinking blocks」エラーが出た → `/new`でリセットして解決
- [出典: 動画1, デモ知見]

## Google Workspace連携（OAuth設定）
- Google Cloud Consoleでプロジェクト作成 → API有効化 → OAuth設定
- 所要10〜15分、一度やれば済む
- Gmail/カレンダー/Drive/Sheets/Docs/People APIに対応
- [出典: 動画1]

## Hostinger関連の発見
- サーバーロケーション選択は購入時のみ（購入後は変更不可かも）
- Tokyo選択可能かは要確認
- タイムゾーン設定がcronジョブに影響する
- [出典: デモ知見]

## 公式インストール方法（2026-03-16追記）

### システム要件
- **Node.js**: 22以上（Node 24推奨）
- **macOS**: 13 (Ventura) 以降（Intel / Apple Silicon対応）
- **Linux**: glibc 2.31+の任意のモダンディストリビューション
- **Windows**: WSL2経由を強く推奨
- **最小構成**: 1GB RAM, 500MB ディスク
- [出典: https://docs.openclaw.ai/install]

### インストーラスクリプト（推奨）
```bash
# macOS / Linux / WSL2
curl -fsSL https://openclaw.ai/install.sh | bash

# Windows (PowerShell)
iwr -useb https://openclaw.ai/install.ps1 | iex
```

### パッケージマネージャー
```bash
npm install -g openclaw@latest
openclaw setup --wizard --install-daemon
```

### ソースビルド
```bash
git clone https://github.com/openclaw/openclaw.git
cd openclaw && pnpm install && pnpm ui:build && pnpm build && pnpm link --global
openclaw setup --wizard --install-daemon
```

### その他の方法
- Docker, Podman, Nix, Ansible, Bun対応

### インストール後の検証
```bash
openclaw doctor     # 環境チェック
openclaw status     # 稼働状態確認
openclaw dashboard  # ダッシュボード表示
```

### 重要な変更点
- 2026年1月にAnthropicがOAuth廃止 → Anthropic APIキー（pay-as-you-go）が唯一の接続方法
- [出典: 公式ドキュメント]

## Mac mini 日本価格（2026-03-17追記）

| モデル | チップ | メモリ | ストレージ | Apple公式価格（税込） |
|--------|--------|--------|----------|-------------------|
| エントリー | M4 | 16GB | 256GB | 94,800円 |
| （セール価格参考） | M4 | 16GB | 256GB | 84,800円（Amazon等） |
| M4 Pro | M4 Pro | 24GB | 512GB | 要確認 |

- [出典: Apple公式 apple.com/jp, Impress Watch, ASCII.jp]

## macOSでの権限設定（2026-03-17追記）

### 必要な権限（TCC）
- Notifications（通知）
- Accessibility（アクセシビリティ）
- Screen Recording（画面収録）
- Microphone（マイク）
- Speech Recognition（音声認識）
- Automation/AppleScript（自動化）
- ※ フルディスクアクセスも推奨

### セキュリティ推奨設定
- `ALLOWED_DIRECTORIES=/Users/username/openclaw-workspace` でアクセス可能ディレクトリを制限
- `COMMAND_ALLOWLIST=ls,cat,mkdir,cp,node,npm` でコマンドホワイトリスト
- サンドボックス/隔離環境での実行を公式推奨
- [出典: 公式ドキュメント docs.openclaw.ai/platforms/macos, 競合記事各種]

## 常時稼働の詳細設定（2026-03-17追記）

### launchd（自動起動）
- **推奨**: `openclaw gateway install` コマンドで自動導入（`--install-daemon`フラグ）
- LaunchAgent名: `ai.openclaw.gateway`
- 制御コマンド:
  - 再起動: `launchctl kickstart -k gui/$UID/ai.openclaw.gateway`
  - 無効化: `launchctl bootout gui/$UID/ai.openclaw.gateway`
- watchdog plist（オプション）: 15秒ごとにゲートウェイ稼働を確認、落ちていたら再登録

### macOS側の設定
- スリープ無効化: システム設定 → ディスプレイ → スリープしない
- 停電後自動起動: システム設定 → 一般 → 起動とシャットダウン → 「停電後に自動的に起動」
- 自動ログイン: システム設定 → 一般 → 「再起動後に自動的にログイン」

### 注意事項
- 状態ディレクトリ（`~/.openclaw`）をiCloudやクラウド同期フォルダに置かない（ファイルロック競合）
- [出典: 公式ドキュメント, boilerplatehub.com, dirkpaessler.blog]

## 電気代の詳細（2026-03-17追記）

| 状態 | 消費電力 | 月額電気代（目安） |
|------|---------|-----------------|
| アイドル時 | 5-7W | 約100-150円 |
| OpenClawワークロード時 | 8-15W | 約150-300円 |
| ローカルLLM推論時 | 30-50W（推定） | 要追加調査 |

- Apple Silicon統合メモリでGPU/CPUがRAMを共有 → ローカルLLMに有利
- [出典: boilerplatehub.com, vpn07.com, 競合記事各種]

## 不足情報
- [ ] Windows/WSL手順の詳細
- [ ] Docker Compose設定の詳細解説
- [ ] よくあるエラーとトラブルシューティング
- [ ] M4 Proモデルの日本価格確認
- [ ] ローカルLLM実行時の電気代実測値

---
*最終更新: 2026-03-17（Mac mini日本価格、権限設定、常時稼働設定、電気代追記）*
