# OpenClaw 技術調査アシスタントとしての活用

## 概要

OpenClawをSlack経由でコードベースの技術調査に使う活用パターン。
「コードを読んで事実ベースで仮説検証を行う」スタイルは、ドキュメントに載っていない挙動や設定差異の調査に特に有効。

---

## ユースケース1: CI/Local環境差異の原因特定

### 課題
- PlaywrightのE2EテストでlocalとCIで挙動が異なる
- 「違いはwebServerの設定だけのはず」→ 原因が特定できない

### OpenClawの活用方法
1. SlackでOpenClawにファイルパスを伝えて `playwright.config.ts` `vite.config.ts` を読ませる
2. 「事実を確認してから回答して」と指示
3. OpenClawが各設定項目の差異（webServer.command / retries / workers / reuseExistingServer等）を表形式で整理
4. 各仮説（Service Worker / BrowserContextライフサイクル / React StrictMode）を順番にコードで検証

### 実際のやりとり例（2026-04-10の事例）
- **問題:** `taskhub-neo-frontend` でPlaywrightのfetch挙動がlocal(dev)とCI(build+preview)で異なる
- **調査:** `playwright.config.ts` `vite-plugin-pwa.ts` `globalSetup.ts` `main.tsx` `neoFetch.ts` を順番に読み込み
- **特定:** React StrictMode（development buildのみ）による `useEffect` 二重実行 → `neoFetch.ts` のpendingRequests管理でレースコンディション発生

### ポイント
- 「仮説を立てて → コードで確認 → 次の仮説へ」を繰り返すことで根本原因を絞り込める
- OpenClawは複数ファイルをまたいで情報を統合できるため、設定ファイル間の依存関係も把握しやすい

---

## ユースケース2: 外部ライブラリのソースコード直接調査

### 課題
- ドキュメントに記載のない挙動（「lcovだとBranches/Functionsが表示されない」等）
- GitHubのIssueやREADMEでは答えが見つからない

### OpenClawの活用方法
1. ライブラリのGitHubリポジトリURLをOpenClawに渡す
2. OpenClawが `web_fetch` でソースコードを直接取得・解析
3. 実装の事実（対応フォーマット、スキップされているフィールド等）を特定

### 実際のやりとり例（2026-04-10の事例）
- **問題:** octocovでlcovフォーマット使用時、BranchesとFunctionsカバレッジが表示されない
- **調査:** `https://raw.githubusercontent.com/k1LoW/octocov/main/coverage/lcov.go` を直接fetch
- **特定:** lcovパーサーは `DA`（行カバレッジ）のみ処理、`BRDA`/`FN`/`FNDA` は `default: // not implemented` でスキップ
- **提案:** Coberturaフォーマットへの切り替え（Vitest設定例を提示）

### ポイント
- 「ドキュメントに書いていない」→「ソースを読む」という発想の転換
- OpenClawはRawコンテンツURLから直接コードを読めるため、GitHub上のソースコードが調査対象になる
- 「未実装なのか、設定が必要なのか」をコードで判断できるため、無駄な試行錯誤を省ける

---

## 導入ヒント

### 効果的な頼み方
```
# コードを先に見せてから質問する
「playwright.config.ts を読んで、CI/localの設定差異を表形式でまとめて」

# 仮説を明示して検証を依頼する
「Service Workerのキャッシュが原因という仮説を、コードの事実で検証して」

# ソース調査を依頼する
「octocovのlcov実装を https://github.com/k1LoW/octocov で確認して、
BranchesとFunctionsが表示されない理由を教えて」
```

### 落とし穴
- **ドキュメント優先ではなくコード優先:** ドキュメントが古い・不正確なケースで特に有効。OpenClawはソースコードの事実を優先して回答するよう促すとよい
- **仮説の絞り込みは段階的に:** 一度に全仮説を検証しようとせず、最も可能性が高いものから順番に確認していく方が会話が整理しやすい
- **大規模コードベース:** 複数のリポジトリにまたがる場合は、事前にリポジトリのパスをTOOLS.mdに記載しておくとOpenClawがすぐに参照できる

---

## 関連ユースケース
- [開発依頼・PR作成](./usecases.md) → コード実装を依頼する場合
- [Slack連携Tips](./openclaw-slack-integration-tips.md) → Slackでの質問フロー設計
