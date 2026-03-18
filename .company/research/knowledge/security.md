# セキュリティリスク・対策

## 総論
- 理解のない人は「使わない方がよい」と強い警告（安野高弘）
- OpenClawはターミナルコマンド・ファイル読み書き・Web閲覧が可能 → リスクあり
- [出典: 動画3]

## 主なリスク

### 情報・ファイルリスク
- 気づかないうちに情報外部送信、ファイル削除・変更
- [出典: 動画3]

### 脆弱性
- 複数発見済み。リモートから悪意ある行動を送り込み実行させられる脆弱性も
- [出典: 動画3]

### インターネット露出
- デフォルト設定のまま → 誰でもアクセス可能な状態になり得る
- 実際に多数発見されている
- [出典: 動画3]

### ClawHub（スキルマーケット）リスク
- 約4000ファイルスキャン → 283個（約7%）に脆弱性
- APIキー・パスワード等の認証情報漏洩リスク
- 人気スキルでも問題を含むケースあり
- 自動スキャン改善中だが「魔境」に近い状態
- 300以上の悪意あるスキル発見報告あり
- [出典: 動画1, 動画3]

### VPS・Mac mini共通のリスク
- Gatewayがデフォルトで外部からアクセス可能
- APIキー・認証情報が平文保存
- LLM経由でのデータ漏洩リスク
- 認証情報を狙うマルウェア（RedLine、Lumma）が確認済み
- 「Mac miniなら手元にあるから安全」は**誤り**。ネットワーク接続している限りリスクは同等
- [出典: Mac mini比較メモ]

## 対策

### 最小権限の原則
- メッセージ送信・ファイル削除・ネットワーク要求は事前承認制に
- [出典: 動画1]

### ガードレール設定
- タスク3回失敗で停止
- 実行時間上限10分
- [出典: 動画1]

### セキュリティドキュメント活用
- OpenClawのセキュリティドキュメントURLをボットに渡して自己設定させる
- [出典: 動画1]

### スキル導入時の注意
- VirusTotalレポート確認
- ボットにコードレビューさせることを推奨
- [出典: 動画1]

### 運用上の注意
- 大切なファイル・機密情報のあるマシンでは使わない
- 大事な認証情報が入った環境には入れない
- 本番環境での運用は避ける
- 小さく始める（Telegramだけ、スキル1〜2個から）
- [出典: 動画1, 動画3]

## 公式セキュリティ設定（2026-03-16追記）

### セキュリティモデル
- 「単一の信頼できるオペレータ境界ごとに1つのゲートウェイ」が基本設計
- **3原則**: Identity first → Scope next → Model last
- [出典: https://docs.openclaw.ai/gateway/security]

### 監査コマンド
```bash
openclaw security audit         # 基本監査
openclaw security audit --deep  # 深い監査
openclaw security audit --fix   # 自動修正
openclaw security audit --json  # JSON出力
```

### 60秒セキュリティ強化ベースライン設定
```json5
{
  gateway: {
    mode: "local",
    bind: "loopback",
    auth: { mode: "token", token: "replace-with-long-random-token" },
  },
  session: { dmScope: "per-channel-peer" },
  tools: {
    profile: "messaging",
    deny: ["group:automation", "group:runtime", "group:fs", "sessions_spawn", "sessions_send"],
    fs: { workspaceOnly: true },
    exec: { security: "deny", ask: "always" },
    elevated: { enabled: false },
  },
  channels: {
    whatsapp: { dmPolicy: "pairing", groups: { "*": { requireMention: true } } },
  },
}
```

### 認証方式
- **token**（推奨）/ password / trusted-proxy

### ネットワークバインド
- **loopback**（デフォルト・最安全）/ lan / tailnet / custom

### DMポリシー
- **pairing**（推奨、1時間有効コード）/ allowlist（最高）/ open（低）/ disabled

### 注意事項
- Dockerソケットをコンテナにマウントしない
- `~/.openclaw`（state + workspace）の夜間バックアップ推奨
- サードパーティスキルは信頼できないコードとして扱う
- [出典: 公式ドキュメント, nebius.com, hostinger.com]

## 不足情報
- [ ] 既知のCVE情報一覧
- [ ] 企業向けセキュリティベストプラクティス（公式情報以外）
- [ ] サンドボックス環境の詳細

---
*最終更新: 2026-03-16（公式セキュリティドキュメント調査追記）*
