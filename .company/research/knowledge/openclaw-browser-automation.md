---
topic: OpenClawのブラウザ操作・デバッグ機能
type: knowledge
created: 2026-03-22
sources:
  - OpenClaw公式ドキュメント, clawtank.dev, Medium, GitHub 等
---

# OpenClawのブラウザ操作・デバッグ機能

## 結論

**ブラウザ操作は本格的に対応。デバッグ用途にも使える。**

---

## ブラウザ操作の仕組み

Chrome DevTools Protocol (CDP) ベースで、内部的にPlaywrightを使用。スクショの画像認識ではなく、**ブラウザと直接通信**するため高速かつ正確。

### できる操作
- クリック、テキスト入力、スクロール、ドラッグ&ドロップ
- フォーム送信、ファイルアップロード
- スクリーンショット撮影（refラベルのオーバーレイ付きも可能）
- PDF出力、動画録画

### 3つの接続モード

| モード | 概要 | 用途 |
|--------|------|------|
| Extension Relay | 普段使いのChromeをそのまま制御 | ログイン状態を保持したまま操作 |
| OpenClaw Managed | 隔離されたChromiumインスタンス | 安全な自動化 |
| Remote CDP | クラウド/リモートのブラウザに接続 | VPSからの操作等 |

---

## デバッグ用途

### Chrome DevTools MCP統合で可能なこと

| 機能 | 詳細 |
|------|------|
| コンソールログ取得 | Runtime.consoleログにアクセス |
| ネットワーク監視 | リクエスト/レスポンスの傍受・分析 |
| パフォーマンス計測 | Core Web Vitals（LCP, CLS, INP） |
| DOM操作 | 要素の確認・操作 |

### E2Eテスト
- 自然言語でテストシナリオを定義可能
- セルフヒーリング機能付き（UIが変わっても自動適応）

### デバッグワークフロー例
```
Slack: 「ログイン画面でエラーが出てるか確認して」
OpenClaw: ブラウザ起動 → ログイン画面アクセス → スクショ撮影 → コンソールログ確認 → Slackに報告
```

### Cron + ブラウザ + Slack連携
- 定期的にWebアプリにアクセス → エラーがあればスクショ付きでSlackに報告
- 死活監視的な使い方も可能

---

## VPS vs Mac miniの違い

| 項目 | VPS | Mac mini |
|------|-----|----------|
| GUI付きブラウザ | ✕ ヘッドレスのみ | ◎ GUI付きも使える |
| Extension Relay | △ リレー用Nodeホストが必要 | ◎ そのまま使える |
| ヘッドレス操作 | ◎ 問題なし | ◎ 問題なし |
| スクショ撮影 | ◎ | ◎ |

→ ヘッドレスで十分なデバッグ用途なら**どちらでも同じ**

---

## 関連スキル（ClawHub）

| スキル名 | 概要 |
|----------|------|
| agent-browser | Rustベースの高速ヘッドレスブラウザ自動化 |
| browser-use / browser-js | 基本的なブラウザ操作 |
| webapp-testing | Webアプリテスト用 |

---

## 出典
- [Browser - OpenClaw公式ドキュメント](https://docs.openclaw.ai/tools/browser)
- [How OpenClaw Controls Your Browser](https://blog.laozhang.ai/en/posts/openclaw-browser-control)
- [E2E Test Automation with OpenClaw](https://jangwook.net/en/blog/en/openclaw-e2e-test-automation-guide/)
- [OpenClaw + Chrome DevTools MCP](https://medium.com/@tentenco/openclaw-chrome-devtools-mcp)
