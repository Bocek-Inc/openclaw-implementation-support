# OpenClaw導入・運用に関する詳細調査レポート

調査日: 2026-03-21

---

## 1. 常時稼働環境: VPS vs Mac mini

### VPSの場合

#### 推奨スペック

| 項目 | 最小要件 | 推奨スペック | ヘビーユース |
|------|---------|-------------|-------------|
| CPU | 1-2 vCPU | 2-4 vCPU | 6+ vCPU |
| メモリ | 2 GB RAM | 4 GB RAM | 8-12 GB RAM |
| ストレージ | 20 GB SSD | 40 GB NVMe | 100 GB NVMe |
| OS | Ubuntu 22.04 LTS | Ubuntu 22.04 LTS | Ubuntu 22.04 LTS |

**注意**: OpenClawはAPIベースでClaude等の外部LLMを呼び出すため、ローカルにGPUは不要。VPSに求められるのはゲートウェイの安定稼働とスキル実行環境のみ。

#### 推奨VPSサービスと月額コスト

| サービス | プラン例 | スペック | 月額 |
|---------|---------|---------|------|
| Hetzner | CX22 | 2 vCPU / 4GB RAM / 40GB NVMe | 約$5-7/月 |
| DigitalOcean | Basic Droplet | 2 vCPU / 4GB RAM / 80GB SSD | 約$12/月 |
| Contabo | VPS S | 4 vCPU / 8GB RAM / 75GB NVMe | 約$7/月 |
| Oracle Cloud | Always Free | 4 ARM CPU / 24GB RAM / 200GB | 無料 |
| Hostinger | KVM 2 | 2 vCPU / 8GB RAM / 100GB NVMe | 約$6/月 |

**コスト感**: 年間 $60〜$144（約9,000〜22,000円）程度。Hetznerが最もコスパが良い。Oracle Cloud Always Freeは無料だが、ARM環境のため一部互換性問題の可能性あり。

#### VPSのメリット
- 初期投資が不要（月額制）
- 固定IPが標準で付与される
- データセンター品質のネットワーク（高可用性）
- 世界中どこからでもSSHアクセス可能
- バックアップ・スナップショット機能が標準装備

#### VPSのデメリット
- 月額費用が継続的にかかる
- サーバ管理（SSH、ファイアウォール、OS更新）のスキルが必要
- セキュリティハードニングが必須（後述）

---

### Mac miniの場合

#### 推奨スペック

| 項目 | 推奨 | 備考 |
|------|------|------|
| モデル | Mac mini M4 | 2026年時点で最もコスパが良い |
| メモリ | 16 GB | OpenClaw単体なら十分 |
| ストレージ | 256 GB〜 | プロジェクト数による |
| 価格 | 約$499〜$599 | Apple Store価格 |

#### 電気代

- アイドル時消費電力: **3-4W**（M4チップ）
- 高負荷時: **40-45W**
- 年間電気代: **約$15-25**（約2,000〜4,000円）
- Mac miniの電気代はVPSの月額費用と比べて圧倒的に安い

#### ネットワーク公開設定

| 方法 | 特徴 | コスト | 推奨度 |
|------|------|--------|--------|
| Cloudflare Tunnel | ポート開放不要、無料、コミュニティ実績多 | 無料 | 最も推奨 |
| Tailscale | プライベートメッシュVPN、簡単設定 | 無料（個人） | 自分だけのアクセスなら最適 |
| ngrok | 簡単だが無料枠に制限あり | 無料〜$8/月 | テスト用途向き |

**最も推奨される構成**: Mac mini + Cloudflare Tunnel（無料）。ポートフォワーディング不要で、自宅ルーターの設定変更も不要。

#### デーモン化

```bash
openclaw --install-daemon
```
- macOS launchdデーモンとしてインストールされる
- Mac起動時に自動開始（ログイン不要）
- クラッシュ時も自動再起動
- 一度設定すれば以降は放置でOK

#### Mac miniのメリット
- 長期運用で圧倒的にコスパが良い（約2年でVPSの累計費用を逆転）
- 電気代がほぼ無視できるレベル
- macOS環境そのままなのでClaude Codeとの親和性が高い
- ローカルにデータが残るためプライバシー面で安心

#### Mac miniのデメリット
- 初期投資が$499〜$599かかる
- 自宅のインターネット回線の安定性に依存
- 停電対策（UPS）が必要な場合がある
- 物理的な故障リスク

---

### 結論: どちらが良いか

| 観点 | VPS | Mac mini |
|------|-----|---------|
| 初期コスト | 低い（月額制） | 高い（$499〜$599） |
| ランニングコスト | $60〜$144/年 | $15〜$25/年（電気代のみ） |
| 2年間総コスト | $120〜$288 | $514〜$624 |
| 3年間総コスト | $180〜$432 | $529〜$649 |
| ネットワーク安定性 | 高い | 自宅回線に依存 |
| セキュリティ管理 | 要SSH/FW知識 | 比較的楽（ローカル） |
| 運用・保守の楽さ | やや手間 | デーモン化で楽 |
| Claude Code親和性 | 問題なし | macOSネイティブで高い |

**しゅーへいへの推奨**: 開発初心者であることを考慮すると、**Mac mini M4がおすすめ**。理由:
1. VPSのSSH管理・セキュリティハードニングは初心者にはハードルが高い
2. Cloudflare Tunnelを使えばネットワーク設定も簡単
3. macOS上でClaude Codeをそのまま動かせるため環境構築がシンプル
4. 長期運用（2年以上）ではコスト的にも逆転する
5. ローカル環境なのでデータ流出リスクが低い

ただし、**既にVPS管理の経験がある場合や、自宅回線が不安定な場合はHetzner VPS($5/月)も良い選択肢**。

---

### セキュリティ面での違い

#### VPSの場合（要注意事項が多い）
- **ポート18789（OpenClawゲートウェイ）を0.0.0.0にバインドしない**: 最も危険な設定ミス。ファイアウォールで制限必須
- SSH鍵認証の有効化とパスワードログインの無効化
- UFWファイアウォール設定（SSH:22のみ許可、他は全拒否）
- Docker使用時はDOCKER-USERチェーンでのルール設定が必要
- 定期的なOSアップデート
- セキュリティ調査で42,665台の露出したOpenClawインスタンスが発見されており、93.4%が認証バイパス可能だった

#### Mac miniの場合（比較的安全）
- Cloudflare Tunnel/Tailscale使用でポート開放不要
- macOSのファイアウォールで十分
- ローカルネットワーク内なので外部攻撃面が小さい
- APIキーは環境変数で管理し、ファイルパーミッションを厳格に設定

---

## 2. セットアップにかかる時間・工数

### OpenClaw自体の初期セットアップ

#### 前提条件
- Node.js 22以上（最新安定版推奨）
- Anthropic APIキー（pay-as-you-go）
- Docker Desktop（推奨、サンドボックス用）

#### セットアップ手順と所要時間

| ステップ | 内容 | 初心者の目安 | 経験者の目安 |
|---------|------|------------|------------|
| 1. Node.jsインストール | nvm or 公式インストーラー | 15-30分 | 5分 |
| 2. OpenClawインストール | `curl -fsSL https://openclaw.ai/install.sh \| bash` | 5分 | 5分 |
| 3. オンボーディング | `openclaw onboard` ウィザード実行 | 15-20分 | 5-10分 |
| 4. APIキー設定 | Anthropic APIキーの取得・設定 | 10-15分 | 5分 |
| 5. チャンネル接続 | Slack/Telegram/Discord等 | 20-40分 | 10-15分 |
| 6. 動作確認 | 最初のメッセージ送受信テスト | 10-15分 | 5分 |
| **合計** | | **1.5〜2.5時間** | **30〜45分** |

**QuickStart利用時**: `openclaw onboard`のウィザードが90%の設定を自動化してくれるため、最短10〜15分でチャットできる状態になる。ただし、Slack連携やセキュリティ設定を含めると上記の時間が現実的。

### Claude Code Skillの追加設定

| ステップ | 内容 | 初心者の目安 | 経験者の目安 |
|---------|------|------------|------------|
| 1. Skill理解 | スキルの仕組みを理解 | 30-60分 | - |
| 2. Claude Code Skill導入 | `openclaw skill install` | 10-15分 | 5分 |
| 3. ワークスペース設定 | AGENTS.md, SOUL.md等の設定 | 30-60分 | 15-20分 |
| 4. Claude Codeとの連携確認 | テスト実行 | 15-30分 | 10分 |
| **合計** | | **1.5〜2.5時間** | **30〜35分** |

**ワークスペースファイルの構成**:
OpenClawのエージェントには以下のマークダウンファイルが必要:
- `SOUL.md` - エージェントの性格・行動規範
- `IDENTITY.md` - 名前・役割の定義
- `USER.md` - ユーザー情報
- `AGENTS.md` - エージェント設定
- `TOOLS.md` - 利用可能なツール定義
- `SECURITY.md` - セキュリティポリシー

### 複数リポジトリのAgent Binding設定

| ステップ | 内容 | 初心者の目安 | 経験者の目安 |
|---------|------|------------|------------|
| 1. 設計 | どのリポジトリにどのエージェントを紐づけるか設計 | 30-60分 | 15分 |
| 2. エージェント定義 | `openclaw.json`にエージェントを追加 | 20-30分 | 10分 |
| 3. Binding設定 | チャンネル・ピア・ギルドのルーティング設定 | 30-60分 | 15-20分 |
| 4. ワークスペース作成 | 各エージェントのディレクトリ・MDファイル | 30-60分 | 15-20分 |
| 5. テスト・調整 | ルーティングが正しく動くか確認 | 30-60分 | 15-20分 |
| **合計** | | **2.5〜4.5時間** | **1〜1.5時間** |

### 全体の導入工数まとめ

| フェーズ | 初心者 | 経験者 |
|---------|--------|--------|
| OpenClaw基本セットアップ | 1.5〜2.5時間 | 30〜45分 |
| Claude Code Skill設定 | 1.5〜2.5時間 | 30〜35分 |
| 複数リポジトリBinding | 2.5〜4.5時間 | 1〜1.5時間 |
| **総計** | **5.5〜9.5時間（1〜2日）** | **2〜3時間（半日）** |

---

## 3. 複数リポジトリにまたがる調査・修正

### クロスリポジトリ操作は可能か

**結論: 可能だが、構成方法によってアプローチが異なる。**

#### アプローチ1: 1つのエージェントで複数リポジトリにアクセス

- エージェントのワークスペースにGitHub MCPツールを設定すれば、1つのエージェントが複数リポジトリのissueを読み、コードを修正し、PRを作成できる
- GitHub統合により、特定のコミット取得、issue説明の読み取り、コードdiffへのインラインコメント投稿が可能
- **推奨される構成**: 「管理リポのissueを見て、開発リポのコードを修正する」というユースケースに最も適している

#### アプローチ2: 複数エージェントによる分担（Multi-Agent Routing）

- 各リポジトリに専用エージェントを配置
- エージェント間通信はACP（Agent Communication Protocol）で実現
- 2026.2.17リリースで「決定論的サブエージェントスポーン」と「構造化されたエージェント間通信」が導入
- Bindingの設定で`(channel, accountId, peer)`の組み合わせでルーティングを制御

#### アプローチ3: Claude Code Agent Teams

- Claude Codeの「Agent Teams」機能を活用
- 各エージェントが独立したプロセスとして真に並列で動作
- エージェント間で通信・タスクリスト共有・自動的な作業引き受けが可能
- 役割分担（例: issue調査担当とコード修正担当）が自然にできる

### 制約と注意点

1. **認証の分離**: エージェントごとに認証プロファイルが独立。メインエージェントの認証情報は他のエージェントに自動共有されない
2. **agentDirの重複禁止**: 同じagentDirを複数エージェントで使い回すと認証/セッションの衝突が起きる
3. **ファイルロック**: 共有ワークスペースで2つのエージェントが同じファイルに書き込もうとすると「resource busy」シグナルが返される（サイレントな上書きは発生しない）
4. **Binding設定の優先順位**: 複数のBindingがマッチする場合「最も詳細なもの（most-specific）」が勝ち、同じティアでは設定ファイルの記載順が優先

### 具体的な構成例: 管理リポissue → 開発リポ修正

```
エージェント構成:
  agent-dev:
    workspace: ./agents/dev/
    tools:
      - github-mcp (管理リポ: read権限, 開発リポ: read/write権限)
      - claude-code-skill
    binding:
      channel: slack
      peer: "#dev-requests"
```

このように1つのエージェントにGitHub MCPで複数リポジトリへのアクセスを付与するのが最もシンプルな構成。

---

## 4. 実際の運用イメージ

### ケース1: Slackから「このバグ直して」と依頼した場合

```
1. ユーザー（Slack）: 「#dev-requests に "ログインページで500エラーが出る。修正して" と投稿」
   ↓ (Socket Mode経由)
2. OpenClaw Gateway: メッセージを受信、Bindingルールに基づきエージェント選択
   ↓
3. エージェント: システムプロンプト（SOUL.md, AGENTS.md等）をロード
   ↓
4. Claude Code Skill起動:
   - リポジトリをクローン/プル
   - エラーの原因調査（ログ、コード読み取り）
   - 修正コードの実装
   - テスト実行
   ↓
5. エージェント → Slack: 「調査結果: auth/login.tsの42行目でnull参照エラー。修正PRを作成しました: https://github.com/...」
   ↓
6. ユーザー: PRをレビュー → マージ
```

**所要時間の目安**:
- 最初の応答（受信確認）: 2-3秒
- 調査+修正+PR作成: 数分〜十数分（バグの複雑さによる）
- P50レスポンスタイム: 2.3秒、P99: 11秒（ツール呼び出し回数による）

### ケース2: 仕様確認の場合

```
1. ユーザー（Slack）: 「ユーザー登録APIのバリデーションルールを教えて」
   ↓
2. OpenClaw Gateway → エージェント選択
   ↓
3. エージェント:
   - リポジトリのコードを検索
   - バリデーション関連のファイルを読み取り
   - 仕様をまとめる
   ↓
4. エージェント → Slack:
   「ユーザー登録APIのバリデーション:
    - email: 必須, RFC 5322形式
    - password: 必須, 8文字以上, 大小英数字含む
    - name: 必須, 2-50文字
    参照: src/validators/user.ts」
```

**所要時間の目安**: 10-30秒

### ケース3: 複数リポにまたがる場合

```
1. ユーザー（Slack）: 「管理リポのissue #42を確認して、開発リポで対応して」
   ↓
2. OpenClaw Gateway → エージェント選択
   ↓
3. エージェント:
   a. GitHub MCP → 管理リポのissue #42を取得・内容を理解
   b. issue内容を分析し、修正方針を策定
   c. 開発リポのコードを調査
   d. 修正を実装
   e. テスト実行
   f. PRを作成
   ↓
4. エージェント → Slack:
   「管理リポ issue #42 "商品検索でカテゴリフィルターが動かない" を確認しました。
    原因: search/filter.tsのカテゴリIDマッピングが古い形式のまま。
    修正PR: https://github.com/org/dev-repo/pull/123
    issueにも修正PRへのリンクをコメントしました。」
```

**所要時間の目安**: 5-15分（issue内容とコードの複雑さによる）

### ケース4: Sentry連携による自動バグ修正（高度な運用例）

Nat Eliason氏のチームが実践している運用:

```
1. Sentry: 本番環境でエラーを検知
   ↓
2. OpenClaw: Sentryアラートを受信
   ↓
3. エージェント:
   - エラーログを分析
   - 原因を特定
   - 修正コードを生成
   - PRを自動作成
   ↓
4. Slack: 「エラー検知→修正PR作成完了」の通知
   ↓
5. 開発者: PRをレビュー → マージ
```

人間が気づく前にバグが修正されている、というワークフローが実現可能。

### レスポンス時間まとめ

| 操作 | P50 | P99 | 備考 |
|------|-----|-----|------|
| 簡単な質問応答 | 2-3秒 | 5-8秒 | ツール呼び出し少 |
| コード調査・説明 | 5-15秒 | 30秒 | コード読み取りあり |
| バグ修正+PR作成 | 2-5分 | 15分 | 複雑さによる |
| クロスリポ操作 | 5-15分 | 30分 | 複数リポアクセス |

**注意**: Slack側のレート制限（2026年3月3日に新制限適用）により、非Marketplaceアプリへのスロットリングが発生する場合がある。

---

## 参考資料・出典

### 公式ドキュメント
- [OpenClaw Getting Started](https://docs.openclaw.ai/start/getting-started)
- [OpenClaw VPS Guide](https://docs.openclaw.ai/vps)
- [OpenClaw Slack Channel](https://docs.openclaw.ai/channels/slack)
- [OpenClaw Multi-Agent Routing](https://docs.openclaw.ai/concepts/multi-agent)
- [OpenClaw Agent Workspace](https://docs.openclaw.ai/concepts/agent-workspace)
- [OpenClaw Skills](https://docs.openclaw.ai/tools/skills)
- [OpenClaw Security](https://docs.openclaw.ai/gateway/security)

### GitHub
- [openclaw/openclaw](https://github.com/openclaw/openclaw)
- [openclaw/openclaw AGENTS.md](https://github.com/openclaw/openclaw/blob/main/AGENTS.md)
- [Claude Code Skill for OpenClaw](https://github.com/Enderfga/openclaw-claude-code-skill)
- [OpenClaw Workspace Skill](https://github.com/win4r/openclaw-workspace)
- [Awesome OpenClaw Skills](https://github.com/VoltAgent/awesome-openclaw-skills)

### コミュニティ記事・ブログ
- [OpenClaw on Mac Mini: Best Always-On Setup](https://boilerplatehub.com/blog/openclaw-mac-mini)
- [Mac Mini vs VPS for OpenClaw](https://www.pcbuildadvisor.com/mac-mini-vs-vps-for-openclaw-formerly-clawdbot-moltbot-which-one-should-you-choose/)
- [OpenClaw Setup: Zero to First Chat in 10 Minutes](https://medium.com/@contact_68491/openclaw-setup-from-zero-to-first-chat-in-10-minutes-2026-edition-ba9c384f811c)
- [OpenClaw Security Hardening Guide](https://alirezarezvani.medium.com/openclaw-security-my-complete-hardening-guide-for-vps-and-docker-deployments-14d754edfc1e)
- [Reduce OpenClaw Latency: 5 Optimizations](https://markaicode.com/reduce-openclaw-latency-5-optimization-tips/)
- [OpenClaw Multi-Agent Deployment](https://medium.com/h7w/openclaw-multi-agent-deployment-from-single-agent-to-team-architecture-the-complete-path-353906414fca)
- [Sentry→OpenClaw→PR Workflow (Nat Eliason)](https://x.com/nateliason/status/2017270908986016044)
- [Every Way to Deploy OpenClaw](https://flowzap.xyz/blog/every-way-to-deploy-openclaw)
