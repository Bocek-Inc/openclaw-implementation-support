# OpenClaw 開発ワークフロー統合の知見

開発チームへの導入で得られた実践的なパターンと落とし穴。

---

## 技術選定の経緯調査フロー

### ユースケース
「なぜこのライブラリを選んだの？」「誰がいつ入れたの？」という技術選定の経緯をOpenClawが横断的に調査するパターン。

### 実例（2026-04-16）
- Slackで「wouter（フロントエンドルーティング）を選んだ経緯を調べて」と依頼
- OpenClawが Slack・Notion・git log を横断調査
- → git log から `avaice（平林さん）が2024年12月31日に説明なし・PRなしでmainに直接コミット` と特定
- → Notionの技術選定ドキュメントには記載なしも確認

### 有効なケース
- ドキュメントがなく「言語化されていない設計判断」の発掘
- 「誰に聞いたらわかる？」の特定
- 技術負債の根本原因調査

### ポイント
- `git log --grep`, `git log --author`, Slack検索, Notion全文検索を組み合わせる
- 「文書がない = 意図的な選択ではないかもしれない」という仮説を立てやすい

---

## Notionアジェンダ自動追記パターン

### ユースケース
Slackの会話から直接NotionのMTGアジェンダDBに議題を追加する。

### 実例（2026-04-16）
- wouter vs react-router-dom の調査結果をSlackで共有した後
- 「dev2週次のアジェンダに追加して」と依頼
- → OpenClawがNotionのアジェンダDBにページを作成（背景・選択肢・TODO付き）
- → 週次MTGの当日に自動で議題一覧に表示される

### 効果
- 「後でNotionに書こう」という行動コストをゼロに
- Slackの会話が散逸せずMTGに繋がる

### ポイント
- NotionのデータベースIDをTOOLS.mdに記録しておくと再利用しやすい
- 「考慮すべきこと」DBと「議論トピック」DBの二段構えが使いやすい

---

## CI/CDセキュリティツール統合のTips

### ユースケース
Aikido safe-chain のようなCIセキュリティツールとDependabotを組み合わせる際の落とし穴と回避策。

### 問題（2026-04-16）
- Dependabot が `dompurify 3.4.0` のPRを作成
- CIでAikido safe-chainの `minimum package age` チェックがブロック
- → `dompurify@3.4.0 - blocked by safe-chain direct download minimum package age`

### 原因
safe-chainのminimum package ageチェックは「リリース直後の未実績パッケージ」を弾く機能。DependabotはリリースされたばかりのパッチバージョンのPRを即作成するため、この条件に引っかかりやすい。

### 回避策

**① Dependabot PRのジョブだけスキップ（推奨）**
```yaml
- name: Run Aikido safe-chain
  run: |
    EXTRA_FLAGS=""
    if [ "${{ github.actor }}" = "dependabot[bot]" ]; then
      EXTRA_FLAGS="--safe-chain-skip-minimum-package-age"
    fi
    npx @aikidosec/safe-chain $EXTRA_FLAGS
```

**② 全体でスキップ（非推奨・セキュリティリスクあり）**
```yaml
run: npx @aikidosec/safe-chain --safe-chain-skip-minimum-package-age
```

### ポイント
- Dependabotは「確認済み・広く使われているパッケージのバージョンアップ」なので、age checkの趣旨とは相容れない
- エラーメッセージにスキップフラグが書いてある（`--safe-chain-skip-minimum-package-age`）ので、ログをよく読めば自己解決できる

---

## protobuf oneof デフォルト値の処理パターン（Kotlin/PHP混在環境）

### 問題
gRPC通信でKotlin（protobuf）→ PHP に値を渡す際、protobufの `oneof` 未設定時のデフォルト値（例: `AGENTTOOL_NOT_SET`）がPHP側のenumに存在せずエラーになる。

### 誤ったアプローチ
PHP側の `AgentToolType` enumに `AGENTTOOL_NOT_SET = 0` を追加する。

### 正しいアプローチ（2026-04-16 PR #1079）
Kotlin側のprotobuf処理層（itamae）で `AGENTTOOL_NOT_SET` をスキップするロジックを追加。

### 理由
- `AGENTTOOL_NOT_SET` はprotobufの実装詳細（oneof未設定時のデフォルト）
- PHPのビジネスロジック層のenumに追加すると、「未設定」が有効な値として誤って処理される危険性
- 処理責任は protobuf の型を扱うKotlin側に持たせるべき

### ポイント
- 「PHPのenumに追加すべきか、Kotlin側でスキップすべきか」という判断は、**その値の意味的な位置づけ**で決まる
- protobuf固有の概念はprotobufを扱う層（gRPCアダプター）で処理させるのが正しいアーキテクチャ

---

*初版: 2026-04-16*
