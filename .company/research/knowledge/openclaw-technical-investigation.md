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

### 実際のやりとり例（2026-04-10〜11の事例）
- **問題:** octocovでlcovフォーマット使用時、BranchesとFunctionsカバレッジが表示されない
- **調査:** `https://raw.githubusercontent.com/k1LoW/octocov/main/coverage/lcov.go` を直接fetch
- **特定:** lcovパーサーは `DA`（行カバレッジ）のみ処理、`BRDA`/`FN`/`FNDA` は `default: // not implemented` でスキップ
- **結論:** lcovでは `lines` のカバレッジしか取れない。`Branches/Functions` を表示したいなら `cobertura` or `clover` に切り替えが必要

### ポイント
- 「ドキュメントに書いていない」→「ソースを読む」という発想の転換
- OpenClawはRawコンテンツURLから直接コードを読めるため、GitHub上のソースコードが調査対象になる
- 「未実装なのか、設定が必要なのか」をコードで判断できるため、無駄な試行錯誤を省ける

---

## ユースケース3: ツール選定より設定の見直しを先に（2026-04-11の事例）

### 課題
- 「octocovをcodecovに切り替えるべきか」「lcovをcoberturaにすべきか」と聞かれる
- 実は設定ファイルの問題で、ツール自体は変えなくて済むケースが多い

### OpenClawの活用方法
1. 現在の `.octocov.yml` 設定を確認してもらう
2. 「PRコメントに全ファイルを表示させたい」→ `report.datastores` と `diff.datastores` の設定確認
3. ツール変更よりも設定変更で解決できるかを先に診断

### 実際のやりとり例（2026-04-11の事例）
- **問い:** octocovでPRコメントに「比率だけ」表示される → codecovに切り替えるべきか？
- **診断:** `.octocov.yml` の `report` / `diff` セクションを確認するよう案内
- **結論:** フォーマットを変えても `.octocov.yml` 設定が変わらなければ表示内容は変わらない

### .octocov.yml 設定例（全ファイル表示）
```yaml
comment:
  hideFooterLink: false
report:
  datastores:
    - artifact://${GITHUB_REPOSITORY}
diff:
  datastores:
    - artifact://${GITHUB_REPOSITORY}
```

### ポイント（導入支援Tips）
- 「ツールを変えたい」という相談には、まず「何が問題か」を深掘りする
- 設定で解決できる問題にツール変更を適用すると導入コストが増える
- ライブラリの制約（lcovのBranches未対応等）は事前に把握して提示できると信頼感が高まる

---

## 導入ヒント

### 効果的な頼み方
```
「このファイルを読んで、〇〇の原因を特定してほしい。仮説があれば教えて」
「ドキュメントより先にソースを確認して」
「設定ファイルを見て、PRコメントに全ファイルが出ない理由を調べて」
```

### 注意点
- ファイルパスはTOOLS.mdやリポジトリのパスを事前に伝えておくと速い
- 外部ライブラリは `web_fetch` でRaw URLを取得可能（Brave API不要）
- コードで確認した事実と推測を区別して回答させるとハルシネーションが減る

## 更新履歴
- 2026-04-10: ユースケース1・2を新規作成（CI/Local差異・外部ライブラリ直読）
- 2026-04-11: ユースケース3を追加（ツール選定前に設定見直しを提案するパターン）
