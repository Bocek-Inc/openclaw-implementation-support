---
created: "2026-03-17"
article: "openclaw とは"
status: published
published_url: ""
last_wp_sync: ""
---

# openclaw-toha リライト管理

## 未対応の修正TODO

- [ ] skills記事への内部リンクを追加 | 優先度: 通常
  - セキュリティセクション：「スキル選びでリスクが変わる」旨を追記し、skills記事へリンク
  - 「④小さく始める」の後あたりに追加
  - **リンクテキスト案**: 「OpenClawのスキルについて詳しく知りたい方は「OpenClaw Skills完全ガイド」をご覧ください。」
- [ ] ClawHub言及修正 | 優先度: 通常
  - ClawHubに言及している箇所 → skills記事のClawHubセクションへリンク
  - **方針**: skills記事ClawHubセクションへリンク +「ClawHubの詳細は別記事で解説予定です」表記
  - ClawHub単独記事が公開されたらそちらへリンク先を差し替え
- [x] ~~Telegram連携の手順セクションを追加~~ | 取消: 2026-03-19
  - → 別記事「openclaw telegram」として将来執筆する方針に変更
  - 以下の構成案は別記事用に保持
  - **挿入位置**: Step⑥（ブートストラップ）の直後に「Step⑦」として追加
  - **情報源**: OpenClaw公式ドキュメント（docs.openclaw.ai/channels/telegram）
  - **設定方法**: Hostinger Configuration画面（方法A）で統一
  - **構成案（確定）**:
    ```
    h3: Step⑦：Telegramと連携する（スマホからOpenClawを操作）
      h4: なぜTelegramなのか？
        - 20以上の対応チャットアプリの中で最も手軽（無料、ボット作成が簡単）
        - スマホ＋PCどちらからも操作可能
        - 「対策④：小さく始める」で推奨されている最初の連携先
      h4: ①Telegramでボットを作成する（BotFather）
        - Telegramアプリで @BotFather を検索して会話開始
        - /newbot → 表示名入力 → ユーザー名入力（末尾bot必須）
        - APIトークンが発行される → パスワードマネージャーに保存
        - <!-- 【画像: telegram-botfather-newbot.png】BotFatherでボットを作成した画面 -->
      h4: ②OpenClawにトークンを設定する
        - Step②のHostinger Configuration画面を再度開く
        - 「Telegram bot token」欄にBotFatherのトークンを貼り付けて保存
        - <!-- 【画像: openclaw-telegram-token-setting.png】Configuration画面のTelegram bot token欄（redacted状態） -->
      h4: ③ペアリングを承認する（初回のみ）
        - Telegramでボットにメッセージを送信
        - ボットからペアリングコードが返される
        - サーバー側でコマンドを実行して承認（Hostinger VPS用コマンド記載）
        - ペアリングコードは1時間で期限切れ → 再送信でOK
      h4: ④動作確認：Telegramから話しかけてみる
        - テストメッセージを送信 → OpenClawが応答すれば成功
        - <!-- 【画像: telegram-openclaw-first-chat.png】Telegramでの初回会話画面 -->
        - skills記事への導線リンク
    ```
  - **スクショ一覧**:
    | No | 撮影元 | 内容 | 状態 |
    |----|--------|------|------|
    | 1 | スマホ | BotFather /newbot → トークン発行画面（トークン隠す） | あり |
    | 2 | PC | Hostinger Configuration画面 Telegram bot token欄（redacted状態） | 撮影可能 |
    | 3 | スマホ | OpenClawボットとの初回会話画面 | あり |
  - **ボリューム**: 約800〜1,000字（h4×4、各150〜250字。分離不要）
  - **次のステップ**: しゅーへいさんレビューOK → スクショ用意 → HTML執筆

- [ ] Mac mini vs VPS比較セクションのリライト | 優先度: 通常
  - **現状の問題**: 「Mac miniが理想」と強めに書きすぎ
  - **結論: 明確な正解はない。どっちでもできる。**
  - VPSでできないことはほぼない（開発用途、Claude Code連携含めて）
  - Mac miniの優位性は「慣れ・とっつきやすさ」程度。コスト差も決定打にならない
  - **修正方針**: 「どちらでもいいが、Mac miniならではの利点はこう」というトーンに調整。深掘りしすぎない
  - Mac mini記事への内部リンク追加
  - **対応タイミング**: Mac mini記事公開後
  - 参照: secretary/notes/2026-03-21-macmini-vs-vps-summary.md

## 信頼性チェック結果（3/17 Phase 7準拠）

### 1. 引用元の網羅チェック
- 引用元あり: 9箇所（GitHub, Wikipedia, OpenClaw docs×3, The New Stack×2, Anthropic docs, YouTube）
- 引用元なし→修正済み: Mac mini価格（Apple公式リンク追加）、Hostinger拠点（Help Centerリンク追加）
- 引用元なし→許容: Hostinger KVMスペック（Step①にHostingerリンクあり）、Rate Limit表（Tier説明にAnthropicリンクあり）
- セットアップ最速70分: 出典不明だが、実体験ベースの記述として許容

### 2. 引用元の偏りチェック
- セキュリティ関連がThe New Stack 1ソースに集中（2箇所）
- ただしThe New Stackは技術メディアとして信頼性が高く、現状許容範囲
- 全体として公式ドキュメント中心の引用構成で適切

### 3. 事実の鮮度チェック → 修正済み
- 🔴 Mac mini USD価格 $629: Apple公式は$599。→ USD表記を削除、日本円のみに修正
- 🔴 Hostingerロケーション「シンガポール」: VPSではシンガポール利用不可 → マレーシアに修正
- 🔴 Hostingerデータセンター「6拠点」: 実際は9拠点以上 → 具体的な拠点列挙を削除、Help Centerリンクに差し替え
- ✅ GitHubスター数31万+: GitHub実ページで318k確認
- ✅ Anthropic Tier 1/2 Rate Limit: 公式docs全数値一致

### 4. クロスチェック
- GitHubスター数: GitHub(318k) × star-history.com(250k+ 3/3) → ✅ 整合
- Mac mini ¥94,800: Apple JP公式 × Gigazine → ✅ 一致
- Tier 1 ITPM 3万 / Tier 2 ITPM 45万 / 15倍: 記事 × Anthropic公式docs → ✅ 全一致
- ClawHub脆弱性7%(283/4000): The New Stackのみ（単一ソース）→ ⚠️ 許容だが補強推奨

## 完了済み

### 事実修正（3/16 セルフチェックで発覚）→ 3/17完了
- [x] Motebot → Moltbot に修正（事実誤認）
- [x] ClaudeBot → Clawdbot に修正（追加で発覚した事実誤認。Wikipedia等で確認）
- [x] チャネル数「15以上」→「20以上」に修正（3箇所）
- [x] GitHubスター数「27万超え」→「31万超え」に修正（GitHub実ページで318kを確認）
- [x] 対応プラットフォーム一覧にTeams/Signal/Matrix/iMessage追加
- [x] Windows対応にWSL2推奨の補足追加（公式ドキュメントへのリンク付き）
- [x] Mac mini価格を「約9.5万円〜」に統一（Apple公式価格94,800円に準拠）

### 上司FB（3/17）→ 3/17完了
- [x] 一次情報を引用・参考元として明記する
  - GitHubスター数 → GitHubリポジトリURL
  - 改名の歴史 → Wikipedia
  - 対応チャネル → 公式ドキュメントURL
  - セキュリティリスク（マルウェア・ClawHub脆弱性）→ The New Stack
  - Anthropic Tier制度 → Anthropic公式ドキュメント
  - Windows WSL2 → 公式プラットフォームドキュメント

### 共通ルール適用（feedback-log.md準拠）→ 3/17完了
- [x] 句読点の抜け漏れチェック
  - 「リスクがあります」末尾の句点追加
  - リスト項目末尾の文末表現統一
  - 口語的な括弧書き表現を書き言葉に修正
  - 不要な読点の削除

## 修正履歴
- 2026-03-17: 初版作成。3/16 TODO + 上司FBを統合
- 2026-03-17: 全TODO完了。事実修正（6項目）+ 一次情報引用（6箇所）+ 句読点修正（4箇所）。追加でClaudeBot→Clawdbotの事実誤認も修正
- 2026-03-17: 信頼性チェック（Phase 7）実施。3件の事実誤認を追加修正（Mac mini USD価格削除、Hostingerロケーション シンガポール→マレーシア、データセンター拠点情報修正）
- 2026-03-18: Telegram連携セクション構成案を確定。公式ドキュメント調査完了。設定方法はHostinger Configuration画面（方法A）で統一。スクショ3点（BotFather・Configuration画面・初回会話）で確定
