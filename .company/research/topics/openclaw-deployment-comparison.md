---
topic: OpenClaw常時稼働環境比較 & 複数リポジトリ運用
status: completed
requested_by: オーナー
date: 2026-03-21
---

# OpenClaw常時稼働環境比較 & 複数リポジトリ運用

## 結論

- **しゅーへいさんにはMac miniを推奨**（VPSより運用がシンプル、長期コスパ◎）
- **複数リポジトリにまたがる操作は可能**（GitHub MCP方式が最もシンプル）
- **セットアップは初心者で1〜2日**

---

## 1. VPS vs Mac mini

### 推奨: Mac mini M4（16GB）

| 比較軸 | VPS | Mac mini |
|--------|-----|----------|
| 初期コスト | $0 | 約10万円〜 |
| 月額コスト | $5〜12/月 | 電気代 月200〜400円程度 |
| 年間コスト | $60〜144 | 約3,000〜5,000円 |
| 常時稼働 | デフォルトで可 | `openclaw --install-daemon` で自動起動・自動再起動 |
| ネットワーク公開 | 標準でパブリックIP | Cloudflare Tunnel（無料）でポート開放不要 |
| セキュリティ管理 | SSH, ファイアウォール設定が必須（初心者にはハードル高） | Cloudflare Tunnel経由なら比較的安全 |
| Claude Code | Linux環境で動く | macOSネイティブで動く（環境構築シンプル） |

### Mac miniを推奨する理由
- VPSはSSH管理やファイアウォール設定が初心者にはハードル高い
- Cloudflare Tunnelを使えばポート開放不要で安全
- daemonモードで自動起動・クラッシュ自動再起動
- 長期的にコスパが良い
- Claude Codeがネイティブ動作

### VPSが良いケース
- 自宅回線が不安定な場合
- 複数拠点から管理したい場合
- → Hetzner VPS（$5/月）が良い選択肢

---

## 2. セットアップ工数

| フェーズ | 初心者（しゅーへい） | 経験者 |
|---------|-----|-----|
| OpenClaw基本セットアップ | 1.5〜2.5時間 | 30〜45分 |
| Claude Code Skill設定 | 1.5〜2.5時間 | 30〜35分 |
| 複数リポジトリBinding | 2.5〜4.5時間 | 1〜1.5時間 |
| **合計** | **5.5〜9.5時間（1〜2日）** | **2〜3時間** |

- `openclaw onboard` ウィザードで基本セットアップは最短10〜15分
- Slack連携やセキュリティ設定を含めるともう少しかかる
- ワークスペースファイル（SOUL.md, AGENTS.md等）6〜7ファイル作成が一番手間

---

## 3. 複数リポジトリにまたがる操作

### 可能。3つのアプローチ

| 方式 | 概要 | 適したケース |
|------|------|-------------|
| **1エージェント + GitHub MCP** | 1つのエージェントにGitHub MCPで複数リポアクセス権付与 | ◎ 最もシンプル。「issueを見てコード修正」に最適 |
| **マルチエージェント + ACP通信** | 各リポに専用エージェント、ACP Protocolで連携 | 大規模・チーム運用向け |
| **Claude Code Agent Teams** | 並列エージェントチームで役割分担 | 複雑なタスク向け |

### 注意点
- エージェント間で認証プロファイルは共有されない
- 同じagentDirの使い回しは厳禁

---

## 4. 実際の運用フロー

### バグ修正の場合
```
1. Slack: 「○○のバグ直して」（2-3秒でOpenClawが受信）
2. エージェント: リポジトリを調査
3. 原因特定 → 修正コード実装 → テスト → PR作成（数分〜十数分）
4. Slack: 結果報告（PRリンク付き）
```

### 仕様確認の場合
```
1. Slack: 「○○の仕様どうなってる？」
2. エージェント: コード・ドキュメント参照
3. Slack: 回答（該当コードの説明）
```

### 高度な運用例
- Sentry連携で本番エラー自動検知 → 自動修正PR作成（Nat Eliason氏チームの事例）

---

## 出典

- [OpenClaw Getting Started](https://docs.openclaw.ai/start/getting-started)
- [OpenClaw VPS Guide](https://docs.openclaw.ai/vps)
- [OpenClaw Multi-Agent Routing](https://docs.openclaw.ai/concepts/multi-agent)
- [OpenClaw on Mac Mini: Best Always-On Setup](https://boilerplatehub.com/blog/openclaw-mac-mini)
- [Claude Code Skill for OpenClaw](https://github.com/Enderfga/openclaw-claude-code-skill)
