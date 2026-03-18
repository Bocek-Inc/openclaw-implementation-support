---
created: "2026-03-17"
platform: "WordPress（ブログ）"
status: published
publish_date: ""
tags: [openclaw, seo, skills, priority-10]
target_kw: "openclaw skills"
total_volume: 150
担当: 吉田
---

# 記事構成案: OpenClawのSkills（スキル）とは？全53種を徹底解説

## ペルソナ分析

### 個人ユーザー
- OpenClawを導入済みまたは導入検討中で、「Skills」の仕組みを知りたい人
- 「Skillsって何ができるの？」「どれを入れればいいの？」と迷っている人
- AIエージェントをもっと便利に使いこなしたい人

### 企業ユーザー
- OpenClawの導入検討時に拡張機能のラインナップを把握したい人
- Skillsのセキュリティリスク（ClawHub経由の悪意あるスキル等）が気になる人
- 業務自動化に使えるスキルの具体例を知りたい人

---

## 導入文（SEOガイド: ステップ①）

### easyな共感
「OpenClawのSkillsって何ができるの？」「たくさんあるけど、どれを入れればいいの？」と気になっていませんか。

### coreな共感
OpenClawを導入したものの、Skillsの設定が複雑で「結局何を有効にすればいいのかわからない」「セキュリティが心配で手が出せない」という方も多いのではないでしょうか。

### 引き込み
この記事では、OpenClawのSkillsの仕組みから公式53種の全リスト、おすすめスキルの実践レビュー、セキュリティ対策まで、初心者にもわかりやすく解説します。

### 記事概要
OpenClawのSkillsを理解して、安全に使いこなすための完全ガイドです。

---

## 素材マッピング

| セクション | 素材ソース |
|-----------|-----------|
| Skillsとは | knowledge/skills-clawhub.md（スキルの定義、Tools vs Skills） |
| 53種全リスト | knowledge/skills-clawhub.md（バンドルスキル全53種テーブル） |
| おすすめスキル実践 | WenHao Yu記事の設定例、Apiyi.comのTop 10、🔧オーナー体験 |
| ClawHub | knowledge/skills-clawhub.md（ClawHub情報） |
| セキュリティ | knowledge/skills-clawhub.md、knowledge/security.md |

---

## 記事構成（見出し案）

### h2: OpenClawとは？
*→ 内部リンクで「とは記事」に誘導。概略のみ*
- OpenClawの概要（2〜3文）
- 内部リンク: 「OpenClawについて詳しくはこちら」

---

### h2: OpenClawのSkills（スキル）とは？
*→ KW「openclaw skills」をカバー*

#### h3: OpenClawのSkillsの仕組み — ToolsとSkillsの違い
- **Toolsは「臓器」**: 何ができるかを決定（read, write, exec, browserなど）
- **Skillsは「教科書」**: Toolsの組み合わせ方を教える（gog, obsidian, githubなど）
- Skillsを入れても権限は変わらない（権限はToolsで制御）
- 表: ToolsとSkillsの違い比較
- <!-- 【画像: skills-vs-tools.png】AI生成イメージ。alt: OpenClawのToolsとSkillsの違いを示す概念図 -->

#### h3: OpenClawのSkillsが動作する3つの条件
- ①設定（Configuration）: Toolsで機能を許可
- ②導入（Installation）: 必要なCLIツールがインストール済み
- ③認可（Authorization）: 外部サービスへの認証完了
- 具体例: 「Gmailを読む」場合の3条件
- <!-- 【画像: skills-3conditions.png】AI生成イメージ。alt: OpenClawのSkillsが動作する3つの条件の図解 -->

#### h3: OpenClawのSkillsの3つの種類
- Bundled（組み込み）: 53種がプリインストール
- Managed（ClawHub経由）: 13,700以上のコミュニティ製スキル
- Workspace（ローカル）: ユーザーが自作
- 優先度の順番（ワークスペース > 管理 > バンドル）
- 表: 3種類の比較（取得元、管理方法、セキュリティ）

---

### h2: OpenClawの公式スキル全53種一覧【カテゴリ別】
*→ 網羅性の柱。競合が日本語で提供していない情報*

#### h3: OpenClawのビジネス・業務効率化スキル（7種）
- gog（Google Workspace）、himalaya（メール）、things-mac、apple-reminders、trello、1password
- 表形式: スキル名 / 対応サービス / 主な機能 / リスクレベル

#### h3: OpenClawのノート・ドキュメントスキル（5種）
- obsidian、notion、apple-notes、bear-notes、nano-pdf
- 表形式

#### h3: OpenClawのチャット・SNSスキル（6種）
- slack、discord、wacli、imsg、bluebubbles、bird
- 表形式

#### h3: OpenClawの開発者向けスキル（4種）
- github、coding-agent、tmux、session-logs
- 表形式

#### h3: OpenClawのクリエイティブ・メディアスキル（9種）
- openai-image-gen、nano-banana-pro、video-frames、gifgrep、canvas、sag、openai-whisper、openai-whisper-api、sherpa-onnx-tts
- 表形式

#### h3: OpenClawのスマートホーム・IoTスキル（4種）
- openhue、eightctl、food-order、ordercli
- 表形式

#### h3: OpenClawのAI連携・システムスキル（12種）
- gemini、oracle、mcporter、clawhub、skill-creator、healthcheck、summarize、weather、peekaboo、model-usage、blogwatcher、camsnap 等
- 表形式

#### h3: OpenClawのその他のスキル（6種）
- goplaces、local-places、voice-call、spotify-player、sonoscli、blucli、songsee
- 表形式

---

### h2: OpenClawのおすすめスキル6選【目的別】
*→ オリジナルコンテンツ。表形式で簡潔に紹介*
- gog、summarize、weather、github、nano-banana-pro、healthcheck
- 表: スキル名 / おすすめポイント / 活用例
- 組み合わせ例（デイリーブリーフ、CI/CD、RSS収集）

---

### h2: OpenClawのSkillsを実際に使ってみよう【weatherスキルで動作確認】
*→ ハンズオン体験パート。読者に「実際に動く」イメージを持ってもらう*

#### h3: Telegramから天気を聞いてみる
- weatherはバンドルスキルなのでデフォルトで有効。設定不要ですぐ試せる
- Telegramで「東京の天気を教えて」と送信→OpenClawが天気予報を返す
- <!-- 【画像: weather-telegram.png】スクショ。alt: Telegramで天気を質問し、OpenClawが返答した画面 -->

---

### h2: ClawHub（クローハブ）でOpenClawのスキルを拡張する方法
*→ サードパーティスキルの世界を紹介*

#### h3: ClawHubとは？OpenClawの公式スキルストア
- 13,700以上のコミュニティ製スキル
- 人気1位: Capability Evolver（35,000DL）— AIが自動的に自身の機能を進化
- 2026年2月からVirusTotal自動スキャン統合

#### h3: ClawHubからOpenClawにスキルをインストールする手順
- clawhub search → clawhub install → clawhub update
- コード例付き
- ※スクショは「おすすめスキル③clawhub」セクションを参照

#### h3: ClawHubのOpenClawスキルのセキュリティリスクと対策
- 約7%に脆弱性が見つかった実績
- 悪意あるスキルの事例（インフォスティーラーの仕込み等）
- clawhub inspect でインストール前チェック
- サンドボックス環境での実行推奨

---

### h2: OpenClawのSkills利用時のセキュリティ対策
*→ 企業ユーザーの不安に応える*

#### h3: OpenClawのSkillsのリスクレベル一覧
- 表: リスクレベル別スキル分類（Low / Medium / High / Very High）
- 公式バンドルスキルはOpenClaw開発元が管理 → 基本的に安全
- リスクが高いのは外部サービスへのフルアクセス権を持つスキル（wacli, imsg, bird, 1password等）

#### h3: OpenClawのSkillsを安全に使うための4つのポイント
1. `skills.allowBundled` でホワイトリスト管理（使うものだけ有効化）
   - ※allowBundledはスキル（教科書）の制御。ツール（臓器）は残るため、直接指示での実行可能性は残る
   - SkillsタブUI（Agent → Skills）で設定反映を確認可能
   - <!-- 【画像: skills-ui-tab.png】スクショ。alt: OpenClawのWeb UIでスキルの有効/無効状態を確認する画面 -->
2. リスクレベルが「High」以上のスキルは本当に必要か検討
3. SKILL.mdの内容をコードレビューと同様に確認
4. テスト環境で先に動作確認する
- 設定例: openclaw.jsonのallowBundled設定（コード例）
- 4つの原則④で「exec approvals」（承認フロー）に言及。詳細はセキュリティ記事へ

---

### h2: まとめ：OpenClawのSkillsを理解して、AIエージェントを使いこなそう
- 記事の振り返り
- Skillsは「教科書」— 権限を与えるのではなく、使い方を教える仕組み
- 公式53種は安全。まずはgog・summarize・weatherあたりから始めるのがおすすめ
- ClawHubのサードパーティスキルはセキュリティに注意
- 「今回OpenClawのSkillsについて解説してきましたが、いかがだったでしょうか。」

---

## 内部リンク計画

| リンク先記事 | リンク元セクション |
|------------|-----------------|
| openclaw（とは記事） | 冒頭「OpenClawとは？」 |
| openclaw インストール | ClawHubインストール手順 |
| openclaw セキュリティ | セキュリティ対策セクション |
| openclaw できること | おすすめスキルセクション |
| openclaw 使い方 | おすすめスキルセクション |
| openclaw slack | チャット・SNSスキル（slack） |

## 外部リンク計画（一次情報のみ）

| リンク先 | 用途 |
|---------|------|
| https://docs.openclaw.ai/tools/skills | 公式Skills文書 |
| https://clawhub.ai | ClawHub公式 |
| https://github.com/openclaw/openclaw | GitHubリポジトリ |
| https://docs.openclaw.ai/tools/clawhub | ClawHub公式ガイド |

## オーナー作業（🔧マーク）

### スクショ撮影（WordPress入稿時にアップロード）
- Skills UIタブ（Agent → Skills画面。`blocked by allowlist`等のステータスが見える状態）
- weatherスキル動作確認（3枚: 設定画面、Telegram送信、返答）

### AI生成イメージ（任意）
- Tools vs Skillsの概念図
- Skillsが動作する3条件の図解
- セキュリティ4原則の図解

---

## 将来の関連記事候補
- 「OpenClawのスキルを自作する方法【SKILL.md作成ガイド】」（本記事から分離）

---

## ステータス
- [x] 構成案
- [x] オーナー確認（2026-03-17: スキル自作を別記事化、おすすめ3選に絞って深掘り）
- [x] 下書き
- [x] レビュー（2026-03-17: allowBundled修正、動作確認セクション追加、3つのポイントに整理）
- [x] 公開（2026-03-18: WordPress入稿・画像挿入・内部リンク設定完了）
