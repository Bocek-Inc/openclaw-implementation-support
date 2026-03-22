---
topic: OpenClaw × Claude Code連携調査
status: completed
requested_by: オーナー
date: 2026-03-21
---

# OpenClaw × Claude Code 連携調査

## 結論

**OpenClawからClaude Codeを呼び出す仕組みは存在し、複数リポジトリの操作も可能。** 技術的には確立済みで、実践事例もある。

---

## 1. OpenClawからClaude Codeを呼び出す方法（4つ）

| 方式 | 概要 | 成熟度 |
|------|------|--------|
| **Claude Code Skill（MCP経由）** | コミュニティスキル。パーシステントセッション、エージェントチーム機能あり | ◎ 最も整備 |
| **execツール** | シェル実行で `claude --print` を非対話実行 | △ 環境変数バグあり |
| **ACP（Agent Communication Protocol）** | OpenClaw v26のACPプラグインでフル統合 | ○ 新しい |
| **Claude Code Gateway** | DockerコンテナでCLIをラップするプロキシ方式 | ○ |

### Claude Code Skillの詳細
- GitHub: [openclaw-claude-code-skill](https://github.com/Enderfga/openclaw-claude-code-skill)
- パーシステントセッション対応
- エフォートコントロール（処理の深さ調整）
- エージェントチーム機能

---

## 2. 複数リポジトリ構成

**可能。マルチエージェント + Agent Bindingで実現。**

- 各エージェントに異なるワークスペース（リポジトリ）を割り当て
- Agent BindingでSlackチャンネルからのルーティングを設定
- セッション・認証・人格設定は完全に分離

```
例:
Slack #management → OpenClaw Agent A → Claude Code → management-repo/
Slack #development → OpenClaw Agent B → Claude Code → development-repo/
```

---

## 3. セキュリティ上の注意

- Docker分離、ネットワーク制限、APIキーのシークレットマネージャー管理が必須
- CVE-2026-25253等の脆弱性が過去に報告されている
- `bypassPermissions`モードの使用には注意が必要
- Microsoftのブログ: 「信頼されないコード実行として扱うべき」

---

## 4. VPS vs Mac mini

| 比較軸 | VPS | Mac mini |
|--------|-----|----------|
| 初期コスト | $5/月〜 | 約10万円〜 |
| 常時稼働 | デフォルトで可 | 設定が必要 |
| セキュリティ分離 | Docker+ネットワーク分離が容易 | 手動設定 |
| 長期コスト（1年） | $60〜$240+ | 電気代のみ（月数百円） |
| macOS専用ツール | ✕ | ◎ |

**判断基準**: 試行段階はVPS、本格運用で長期的にはMac miniが有力

---

## 5. Slack → OpenClaw → Claude Code → リポジトリ操作

**現実的。実践事例あり。**

- バグ報告 → 自動PR作成まで、15〜30分で完了する事例あり（手動だと2〜4時間）
- Slackの Socket Mode で簡単に接続可能

### 代替案: Claude Code in Slack（Anthropic公式）
- セットアップがシンプル
- ただしカスタマイズ性はOpenClaw経由より低い

---

## ネクストアクション

- [ ] Claude Code Skillの詳細仕様確認
- [ ] 実際のセットアップ手順の整理（VPS or Mac mini）
- [ ] セキュリティ設計の検討（Docker分離、APIキー管理）
- [ ] クライアント提案用の構成図作成

## 出典

- [openclaw-claude-code-skill (GitHub)](https://github.com/Enderfga/openclaw-claude-code-skill)
- OpenClaw公式ドキュメント - ACP Plugin
- Microsoft Security Blog - AI Agent Security
