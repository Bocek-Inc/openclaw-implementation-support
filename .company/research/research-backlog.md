# リサーチバックログ

## 基盤調査（全記事に影響）
- [x] OpenClaw公式ドキュメント通読 → knowledge/official-docs.md（2026-03-16）
- [x] GitHubリポジトリREADME・主要Issues確認 → knowledge/official-docs.md（2026-03-16）
- [x] ClawHub公式サイトの調査 → knowledge/skills-clawhub.md（2026-03-16）

## KW別の不足情報

### openclaw とは（優先度1）
- [x] 動画素材3本の調査
- [x] 競合記事分析（上位5記事）
- [x] 公式サイトからの最新GitHub stats確認（2026-03-16: 250,829+ stars）
- [ ] FAQ: 「Windows対応？」「日本語OK？」「無料？」「企業利用は？」の根拠調査

### openclaw インストール（優先度2）
- [x] 公式インストール手順の最新版確認（2026-03-16）
- [ ] よくあるエラーとトラブルシューティング一覧
- [ ] Docker Compose設定の詳細

### openclaw セキュリティ（優先度2）
- [x] 公式セキュリティドキュメントの詳細（2026-03-16）
- [ ] 既知のCVE情報
- [ ] 企業向けセキュリティベストプラクティス
- [ ] 動作環境ごとのセキュリティ比較（Mac mini vs VPS）
  - Mac mini: データが社外に出ない（✅）、社内NW上にあるため侵害時の踏み台リスク（△）
  - VPS: 完全にネットワーク隔離（✅）、データが海外DCに置かれる（△）
  - 会社のセキュリティポリシーに応じた選び方を解説
  - 素材: research/knowledge/vps-vs-macmini.md
- [ ] サンドボックス環境の情報
- [ ] `allowBundled`でスキルを外してもexecで実行可能な問題の詳細解説
  - 対策: `tools.deny`、APIキー未設定、`exec approvals`（承認フロー）
  - skills記事では概要のみ触れている → セキュリティ記事で具体的な設定方法を解説
  - スクショ素材あり: `openclaw.json`の`allowBundled`設定画面（しゅーへいさん撮影済み）
- [ ] テスト環境（サンドボックス）での動作確認手順
  - skills記事の「4つのポイント」から分離。セキュリティ記事で解説

### openclaw github（優先度3）
- [x] リポジトリ構造の概要（2026-03-16）
- [ ] 4レイヤー構造の詳細
- [ ] 最新リリース情報

### openclaw できること（優先度3）
- [ ] ユースケースの網羅的収集（海外事例含む）
- [ ] 「できないこと」の明確化

### openclaw 法人（優先度4）
- [ ] 法人導入事例の調査
- [ ] ガバナンス・コンプライアンス観点の情報

### openclaw 使い方（優先度5）
- [ ] 初心者向けステップバイステップガイド
- [ ] 便利な使い方Tips集

### openclaw docker（優先度6）
- [ ] Docker Compose設定の完全解説
- [ ] .env設定ファイルの全項目解説
- [ ] Ollama連携の手順

### openclaw mac mini（優先度7）
- [x] 常時稼働設定の詳細手順（2026-03-21）
- [x] よくある問題と解決策（2026-03-21）
- [x] VPS vs Mac mini比較（2026-03-21）→ knowledge/vps-vs-macmini.md

### openclaw windows（優先度7）
- [ ] WSL方式の詳細手順
- [ ] Cloudflare/Railway方式の調査
- [ ] Windows固有のトラブルシューティング

### openclaw 料金（優先度9）
- [ ] 最新の各APIプロバイダ料金表
- [ ] 月額コストシミュレーション
- [ ] 無料で始める方法

### openclaw skills（優先度10）
- [ ] Skills一覧（13,729+種類）の主要カテゴリ調査
- [ ] 主要スキルの個別レビュー
- [ ] スキル開発方法

### openclaw slack（優先度11）
- [ ] Slack連携の詳細セットアップ手順
- [ ] Slack活用のベストプラクティス

---
*最終更新: 2026-03-16（基盤調査3項目+KW別複数項目を完了チェック）*
