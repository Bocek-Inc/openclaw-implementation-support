# OpenClaw から Claude Code を操作する連携 - 調査レポート

調査日: 2026-03-21

---

## 1. OpenClaw から Claude Code を呼び出す仕組み

### 1-1. 概要

OpenClaw から Claude Code を呼び出す仕組みは **既に存在し、実用段階にある**。主に以下の3つの方法がある。

### 1-2. 方法A: Claude Code Skill（MCP経由）

最も整備された方法。`openclaw-claude-code-skill` というコミュニティ製スキルが公開されている。

- **リポジトリ**: https://github.com/Enderfga/openclaw-claude-code-skill
- **仕組み**: MCP（Model Context Protocol）を通じて、OpenClawのエージェントがClaude Code CLIをプログラム的に操作する
- **主な機能**:
  - パーシステントセッション（コンテキストを保持したまま複数回やり取り）
  - エフォートコントロール（low/medium/high/max + ultrathink）
  - プランモード（実行前に計画を立てさせる）
  - コンテキスト管理（セッションのコンパクト化、トークン使用量の確認）
  - エージェントチーム（複数の専門エージェントをデプロイ）
  - ツールコントロール（使用可能なツールの制御）
  - 予算制限（API使用量の上限設定）
  - マルチモデルサポート

**セットアップ手順**:
```bash
git clone https://github.com/Enderfga/openclaw-claude-code-skill
cd openclaw-claude-code-skill
npm install
npm run build
npm link  # CLIをグローバルに使えるようにする（任意）
```

### 1-3. 方法B: exec ツールによるCLI直接実行

OpenClawの `exec` ツールを使い、Claude Code CLIをサブプロセスとして直接実行する方法。

- **公式ドキュメント**: https://docs.openclaw.ai/tools/exec
- **仕組み**: OpenClawのexecツールがシェルコマンドを実行し、Claude Code CLIを `--print` フラグ付きで非対話モードで起動
- **CLI引数例**: `claude -p "タスク内容" --output-format json --permission-mode bypassPermissions`
- **注意**: `skills.entries.*.env` で設定した環境変数が exec ツールのサブプロセスに渡されないバグが報告されている（Issue #31583）

### 1-4. 方法C: ACP（Agent Communication Protocol）経由

OpenClawの公式プロトコルであるACPを使い、Claude Codeと通信する方法。

- **ツール**: `acpx`（ヘッドレスCLIクライアント）
- **リポジトリ**: https://github.com/openclaw/acpx
- **コマンド例**: `acpx claude` でClaude Codeとの対話セッションを開始
- OpenClaw v26 で ACP プラグインを有効化すると、フル統合が利用可能

### 1-5. 方法D: Claude Code Gateway（プロキシ方式）

Claude Code CLIをDockerコンテナ内でサブプロセスとして実行するFastAPIサーバー。OpenAI互換のHTTPリクエストをClaude Code CLI呼び出しに変換する。

- **リポジトリ**: https://github.com/enescingoz/claude-code-gateway
- **用途**: OpenClaw以外のツール（Cline、Aider等）からもClaude Codeを利用可能にする

---

## 2. 複数リポジトリでClaude Codeを動かす構成

### 2-1. マルチエージェント構成

OpenClawは **複数の独立したエージェント** をサポートしており、各エージェントに異なるリポジトリ（ワークスペース）を割り当てられる。

**アーキテクチャ**:
```
OpenClaw Gateway
├── Agent A（管理リポジトリ担当）
│   ├── workspace: /path/to/management-repo
│   ├── agentDir: ~/.openclaw/agents/agent-a/
│   ├── sessions: 独立したチャット履歴
│   └── AGENTS.md / SOUL.md: エージェント固有の設定
│
├── Agent B（開発リポジトリ担当）
│   ├── workspace: /path/to/dev-repo
│   ├── agentDir: ~/.openclaw/agents/agent-b/
│   ├── sessions: 独立したチャット履歴
│   └── AGENTS.md / SOUL.md: エージェント固有の設定
│
└── Agent Bindings（ルーティング設定）
    ├── Slack #management → Agent A
    └── Slack #development → Agent B
```

### 2-2. セッション管理とコンテキスト分離

- **完全分離**: 各エージェントは独自のワークスペース、セッション、認証プロファイルを持つ
- **クロストーク防止**: 明示的に有効化しない限り、エージェント間の情報漏洩は発生しない
- **セッションストア**: `~/.openclaw/agents/<agentId>/sessions` に個別保存
- **個別の人格設定**: AGENTS.md、SOUL.md、USER.md をエージェントごとに設定可能

### 2-3. Agent Binding（ルーティング）

チャネル（Slack等）からのメッセージを適切なエージェントにルーティングする仕組み:

```yaml
# 設定例
bindings:
  - channel: slack-management
    agent: agent-management
  - channel: slack-development
    agent: agent-development
```

### 2-4. Mission Control（オーケストレーション）

大規模運用向けに「OpenClaw Mission Control」というダッシュボードがある:
- 複数エージェントの一元管理
- タスクの割り当てとコーディネーション
- 承認ベースのガバナンス
- API駆動の自動化

---

## 3. 実現可能性と制約

### 3-1. 技術的実現可能性: 可能

OpenClaw + Claude Code の連携は **既に技術的に確立されている**。コミュニティでの採用も広がっている。

### 3-2. 制限事項

| 項目 | 制限内容 |
|------|----------|
| 環境変数の伝搬 | exec ツールでの環境変数バグ（Issue #31583）|
| 認証 | CLAUDE_CODE_OAUTH_TOKEN の安全な管理が必要 |
| セッション長 | Claude Code のコンテキストウィンドウに依存（長時間タスクでは圧縮が必要）|
| コスト | Claude API の従量課金（予算制限機能で対応可能）|
| レイテンシ | OpenClaw → Claude Code → リポジトリ操作の多段構成による遅延 |
| 権限 | `bypassPermissions` モードはセキュリティリスクあり |

### 3-3. セキュリティ上の懸念

**APIキー管理**:
- 環境変数またはシークレットマネージャー（Vault, AWS Secrets Manager等）に保管
- 設定ファイルにコミットしない
- API キーに使用量上限を設定
- 定期的なキーローテーション

**権限制御**:
- Docker コンテナでの実行（プロセス分離、ファイルシステム制限、ネットワーク制御）
- `docker run -d --read-only --cap-drop=ALL --security-opt=no-new-privileges openclaw`
- ゲートウェイポートを公開しない（localhost バインドのみ）
- リモートアクセスは Tailscale、SSH トンネル、VPN 経由

**既知のセキュリティインシデント**:
- CVE-2026-25253: クロスサイトWebSocketハイジャッキング（2026年1月）
- CVE-2026-32000, CVE-2026-32032: その他の脆弱性
- 21,000以上のインスタンスがインターネットに公開されていた事例あり

**Microsoft のセキュリティガイドライン**:
> OpenClaw は「信頼されないコード実行（persistent credentialsあり）」として扱うべきであり、通常の個人・企業ワークステーションでの実行は適切ではない

### 3-4. 実践事例

- **24/7 自動バグ修正ワークフロー**: OpenClaw がエラーを検知 → Claude Code がコードを分析・修正 → PR を自動作成
  - 参考: https://eastondev.com/blog/en/posts/ai/20260227-openclaw-claude-code-workflow/
- **ClaudeClaw プロジェクト**: OpenClaw スタイルの自律エージェントシステムを Claude Code 上に構築
  - 参考: https://medium.com/@mcraddock/building-claudeclaw-an-openclaw-style-autonomous-agent-system-on-claude-code-fe0d7814ac2e
- **マルチエージェント開発パイプライン**: 複数の専門エージェントが協調して開発タスクを実行
  - 参考: https://dev.to/ggondim/how-i-built-a-deterministic-multi-agent-dev-pipeline-inside-openclaw-and-contributed-a-missing-4ool

---

## 4. VPS vs Mac mini での運用差異

### 4-1. 比較表

| 項目 | VPS（クラウド） | Mac mini |
|------|-----------------|----------|
| **初期コスト** | $5〜20/月 | M4 Mac mini: 約10万円〜 |
| **常時稼働** | デフォルトで24/7 | セットアップ必要（自動起動設定等） |
| **消費電力** | 考慮不要（ホスティング側） | アイドル時約5W（非常に省電力） |
| **セキュリティ分離** | コンテナ+ネットワーク分離が容易 | 物理的に手元にあるが分離は手動設定 |
| **スケーラビリティ** | 簡単にスケールアップ | ハードウェア制約あり |
| **アクセス性** | どこからでもアクセス可能 | Tailscale/VPN等の設定が必要 |
| **パフォーマンス** | プランに依存（$5だと制限的） | Apple Siliconで高性能 |
| **ランニングコスト** | 月額固定 + API使用料 | 電気代のみ + API使用料 |
| **セットアップ容易さ** | ワンクリックデプロイあり（is*hosting等） | 手動セットアップが多い |
| **データプライバシー** | クラウド事業者に依存 | 完全ローカル |

### 4-2. VPS のメリット・考慮点

**メリット**:
- $5/月からスタート可能（十分に安い）
- 2〜3分でデプロイ完了（ワンクリックイメージ利用時）
- ネットワーク分離が容易
- セキュリティ上、自律ワークフロー向きとされる

**考慮点**:
- ストレージ容量に制限がある場合あり
- 低価格プランだとCPU・メモリが不足する可能性
- Claude Code のセッション管理でディスクI/Oがボトルネックになりうる

### 4-3. Mac mini のメリット・考慮点

**メリット**:
- M4チップの高パフォーマンス
- 超低消費電力（月の電気代は数百円程度）
- 物理的なデータ保持（プライバシー重視）
- 長期的にはVPSより安くなる（1年以上の運用で）

**考慮点**:
- 初期投資が大きい
- ネットワーク設定（ポート開放やVPN）が必要
- 停電・障害時の対応は自己責任
- 自宅ネットワークのセキュリティ設計が必要

### 4-4. 推奨構成

| ユースケース | 推奨 |
|-------------|------|
| 試しに使ってみたい | VPS（$5プラン） |
| 本格的な開発ワークフロー | Mac mini または VPS（$15〜20プラン） |
| チーム運用・企業利用 | VPS + Docker + Mission Control |
| プライバシー重視 | Mac mini（自宅設置） |

---

## 5. Slack → OpenClaw → Claude Code → リポジトリ操作のフロー

### 5-1. 結論: 現実的であり、既に実践例がある

このフローは **技術的に確立されており、現実的** である。

### 5-2. フロー詳細

```
1. ユーザーが Slack で @OpenClaw にメンション
   └─ 例: 「@OpenClaw このバグを調査して修正して」+ スタックトレース

2. Slack アダプター がメッセージを受信
   └─ Socket Mode または HTTP Events API
   └─ Agent Binding に基づいて適切なエージェントにルーティング

3. OpenClaw エージェント がメッセージを処理
   └─ LLM がタスクを理解・計画
   └─ Claude Code Skill（MCP）を呼び出し

4. Claude Code が起動
   └─ 指定されたリポジトリのワークスペースで作業
   └─ コード解析 → 原因特定 → 修正実装 → テスト実行

5. 結果をリポジトリに反映
   └─ 修正ブランチを作成（auto-fix/xxx 形式）
   └─ GitHub CLI（gh）でPRを自動作成

6. Slack に結果を通知
   └─ #auto-fix チャネルに通知
   └─ テンプレート: 修正成功 / レビュー必要 / 修正失敗
   └─ インタラクティブボタン: Approve / Reject / View PR
```

### 5-3. ユースケース別の実用性

**バグ修正**:
- 実用性: **高い**
- Slack でバグ報告 → OpenClaw が自動調査 → Claude Code が修正 → PR 作成
- 手動で2〜4時間かかるプロセスが15〜30分に短縮される事例あり

**仕様確認**:
- 実用性: **中〜高**
- Slack で「この機能の仕様を教えて」→ OpenClaw がコードベースを調査 → 要約を返信
- コードリーディング + ドキュメント生成に適している

**コードレビュー**:
- 実用性: **中**
- PR の変更内容を分析し、Slack でフィードバックを返す
- ただし最終判断は人間が行うべき

### 5-4. 代替構成

#### 代替案1: Slack → Claude Code 直接（Claude Code in Slack）

- Anthropic 公式の「Claude Code in Slack」機能
- 公式ドキュメント: https://code.claude.com/docs/en/slack
- Slack で @Claude にメンションすると、Claude Code セッションを自動作成
- **メリット**: セットアップが簡単、OpenClaw不要
- **デメリット**: OpenClawほどのカスタマイズ性がない、マルチリポジトリ管理が弱い

#### 代替案2: GitHub Actions + Claude Code

- GitHub Issue/PR をトリガーにClaude Codeを起動
- CI/CDパイプラインに組み込む
- **メリット**: GitHubエコシステムとの親和性が高い
- **デメリット**: Slack からのリアルタイム操作には不向き

#### 代替案3: カスタムSlack Bot → Claude Code CLI

- 自前のSlack Botを構築し、Claude Code CLIを直接呼び出す
- **メリット**: 中間レイヤーが少ない、シンプル
- **デメリット**: OpenClawの豊富な機能（メモリ、スキル、マルチチャネル等）を再実装する必要あり

### 5-5. フロー実装のための必要コンポーネント

| コンポーネント | 役割 | 必須/任意 |
|---------------|------|-----------|
| OpenClaw Gateway | メッセージルーティング、エージェント管理 | 必須 |
| Slack App | Socket Mode or HTTP Events API | 必須 |
| Claude Code CLI | コーディングタスクの実行 | 必須 |
| Claude Code Skill (MCP) | OpenClaw → Claude Code の橋渡し | 推奨 |
| GitHub CLI (gh) | PR作成・ブランチ操作 | 推奨 |
| Docker | セキュリティ分離 | 推奨 |
| Tailscale/VPN | リモートアクセス | VPS運用時は推奨 |

---

## 6. まとめと推奨事項

### 6-1. 総合評価

| 調査ポイント | 結論 |
|-------------|------|
| OpenClaw → Claude Code の呼び出し | 可能。MCP Skill、exec、ACP の3方式あり |
| 複数リポジトリ構成 | 可能。マルチエージェント + Agent Binding で実現 |
| 技術的制約 | 環境変数バグ、セキュリティ設計が重要 |
| VPS vs Mac mini | 用途による。試行はVPS、本格運用はMac miniも有力 |
| Slack → OpenClaw → Claude Code フロー | 現実的。既に実践事例あり |

### 6-2. 推奨アプローチ

1. **まずVPS（$5〜10プラン）で小さく始める**
   - OpenClaw + Claude Code Skill をセットアップ
   - 1つのリポジトリで動作検証

2. **Slack連携を追加**
   - Socket Mode で接続（ファイアウォール内でも動作可能）
   - 簡単なバグ修正フローから試す

3. **安定したらマルチリポジトリに拡張**
   - Agent Binding で管理リポジトリ・開発リポジトリを分離
   - Mission Control の導入を検討

4. **本格運用時にMac miniへ移行**を検討
   - 長期コスト削減
   - データプライバシーの向上

### 6-3. 注意事項

- **セキュリティを最優先**: Docker分離、ネットワーク制限、キーローテーションは必須
- **コスト監視**: Claude API の使用量に予算制限を設定すること
- **段階的に拡張**: いきなり全自動化せず、人間のレビューを含むフローから始める

---

## Sources

### 公式ドキュメント
- [OpenClaw 公式サイト](https://openclaw.ai/)
- [OpenClaw Exec Tool](https://docs.openclaw.ai/tools/exec)
- [OpenClaw Multi-Agent Routing](https://docs.openclaw.ai/concepts/multi-agent)
- [OpenClaw Agent Workspace](https://docs.openclaw.ai/concepts/agent-workspace)
- [OpenClaw Slack Channel](https://docs.openclaw.ai/channels/slack)
- [OpenClaw Security](https://docs.openclaw.ai/gateway/security)
- [OpenClaw CLI Backends](https://docs.openclaw.ai/gateway/cli-backends)
- [OpenClaw VPS Setup](https://docs.openclaw.ai/vps)
- [Claude Code in Slack](https://code.claude.com/docs/en/slack)

### GitHub リポジトリ
- [openclaw/openclaw](https://github.com/openclaw/openclaw)
- [openclaw-claude-code-skill](https://github.com/Enderfga/openclaw-claude-code-skill)
- [openclaw/acpx](https://github.com/openclaw/acpx)
- [claude-code-gateway](https://github.com/enescingoz/claude-code-gateway)
- [openclaw-mission-control](https://github.com/abhi1693/openclaw-mission-control)

### 技術ブログ・記事
- [OpenClaw + Claude Code 24/7 Auto Bug Fix](https://eastondev.com/blog/en/posts/ai/20260227-openclaw-claude-code-workflow/)
- [Managing OpenClaw with Claude Code](https://trilogyai.substack.com/p/managing-openclaw-with-claude-code)
- [Building ClaudeClaw](https://medium.com/@mcraddock/building-claudeclaw-an-openclaw-style-autonomous-agent-system-on-claude-code-fe0d7814ac2e)
- [OpenClaw Multi-Agent Orchestration Guide](https://zenvanriel.com/ai-engineer-blog/openclaw-multi-agent-orchestration-guide/)
- [OpenClaw Hosting: Mac Mini vs Cloud VPS](https://www.analyticsvidhya.com/blog/2026/02/openclaw-hosting-mac-mini-vs-cloud-vps/)
- [OpenClaw vs Claude Code (DataCamp)](https://www.datacamp.com/blog/openclaw-vs-claude-code)
- [Running OpenClaw safely (Microsoft Security Blog)](https://www.microsoft.com/en-us/security/blog/2026/02/19/running-openclaw-safely-identity-isolation-runtime-risk/)
- [OpenClaw Slack Integration Guide (C# Corner)](https://www.c-sharpcorner.com/article/the-complete-guide-to-integrating-slack-with-openclaw-2026-the-steps-most-ai/)
- [Multi-Agent Dev Pipeline (DEV Community)](https://dev.to/ggondim/how-i-built-a-deterministic-multi-agent-dev-pipeline-inside-openclaw-and-contributed-a-missing-4ool)
- [SkyPilot Blog - Don't Run OpenClaw on Your Main Machine](https://blog.skypilot.co/openclaw-on-skypilot/)

### セキュリティ関連
- [OpenClaw Security Guide (Contabo)](https://contabo.com/blog/openclaw-security-guide-2026/)
- [JFrog - Giving OpenClaw The Keys](https://jfrog.com/blog/giving-openclaw-the-keys-to-your-kingdom-read-this-first/)
- [CVE-2026-32000](https://www.redpacketsecurity.com/cve-alert-cve-2026-32000-openclaw-openclaw/)
