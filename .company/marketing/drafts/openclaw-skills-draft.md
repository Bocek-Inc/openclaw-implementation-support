# OpenClawのSkills（スキル）とは？全53種の一覧とおすすめ活用法を徹底解説

<!-- 対策KW: openclaw skills -->

---

## 導入文

「OpenClawのSkillsって何ができるの？」「たくさんあるけど、どれを入れればいいの？」と気になっている方も多いのではないでしょうか。

OpenClawを導入したものの、Skillsの設定が複雑で「結局何を有効にすればいいのかわからない」「セキュリティが心配で手が出せない」と感じている方もいるかもしれません。

この記事では、OpenClawのSkillsの仕組みから公式53種の全リスト、おすすめスキル、セキュリティ対策まで、初心者にもわかりやすく解説します。

---

## OpenClawとは？

OpenClawは、あなたのPCやサーバー上で24時間365日稼働し、自律的に仕事をこなしてくれるオープンソースのAIエージェントです。

チャット型AIとは異なり、ファイル作成・メール送信・コード修正・Web検索などを実際に実行できます。SlackやTelegramなど使い慣れたチャットアプリから操作できるのも特徴です。

OpenClawの基本については、こちらの記事で詳しく解説しています。

<!-- 内部リンク: openclaw とは記事 -->

---

## OpenClawのSkills（スキル）とは？

OpenClawのSkillsとは、**AIエージェントに特定タスクの実行方法を教える「教科書」のような仕組み**です。

Skillsを追加すると、OpenClawはメールの管理、ノートの整理、GitHub操作など、より幅広いタスクをこなせるようになります。

### OpenClawのSkillsの仕組み — ToolsとSkillsの違い

OpenClawのSkillsを理解するうえで重要なのが、**Tools（ツール）とSkills（スキル）の違い**です。

<!-- 【画像: skills-vs-tools.png】AI生成イメージ。alt: OpenClawのToolsとSkillsの違いを示す概念図 -->

| 比較項目 | Tools（ツール） | Skills（スキル） |
|---------|---------------|----------------|
| **役割** | OpenClawが「何をできるか」を決める | Toolsの「使い方」を教える |
| **たとえ** | 臓器（手・目・口） | 教科書（マニュアル） |
| **具体例** | read（ファイル読取）、exec（コマンド実行）、browser（ブラウザ操作） | gog（Google Workspace連携）、obsidian（ノート管理）、github（GitHub操作） |
| **権限** | 有効/無効を切り替えることで権限を制御 | **追加しても権限は変わらない** |

ポイントは、**Skillsを追加してもOpenClawの権限は変わらない**ということです。Skillsはあくまで「やり方を教える教科書」であり、実際にアクションを実行できるかどうかはToolsの設定で決まります。

例えば、`obsidian`スキルをインストールしても、Toolsの`write`（ファイル書き込み）が無効なら、OpenClawはノートを書き込めません。

### OpenClawのSkillsが動作する3つの条件

OpenClawのSkillsを使って実際にタスクを実行するには、以下の**3つの条件が全て揃う必要**があります。

<!-- 【画像: skills-3conditions.png】AI生成イメージ。alt: OpenClawのSkillsが動作するための3つの条件を示す図解 -->

「OpenClawでGmailを読む」場合を例に説明します。

| 条件 | 内容 | Gmailの場合 |
|------|------|-----------|
| **①設定（Configuration）** | Toolsで該当機能を許可する | `exec`（コマンド実行）を有効にする |
| **②導入（Installation）** | 必要なCLIツールがインストール済み | `gog`（Google Workspace連携ツール）をインストール |
| **③認可（Authorization）** | 外部サービスへの認証を完了する | Googleアカウントでログインしてアクセス権を付与 |

この3つが揃って初めて、OpenClawはGmailにアクセスしてメールを読むことができます。どれか1つでも欠けていると動作しません。

### OpenClawのSkillsの3つの種類

OpenClawのSkillsは、取得元によって以下の3種類に分かれます。

| 種類 | 説明 | スキル数 | セキュリティ |
|------|------|---------|------------|
| **Bundled（組み込み）** | OpenClawにプリインストールされている公式スキル | 53種 | 公式が管理。安全性が高い |
| **Managed（ClawHub経由）** | [ClawHub](https://clawhub.ai)（公式スキルストア）からインストール | 13,700以上 | コミュニティ製。導入前の確認が必要 |
| **Workspace（ローカル）** | ユーザーが自作して配置 | 任意 | 自分で管理 |

スキルが競合した場合の**優先度**は、**Workspace（最高）＞ Managed ＞ Bundled（最低）**です。つまり、自作スキルが最も優先されます。

また、**バンドルスキルはデフォルトで自動的に読み込まれます**。対応するCLIツールがインストールされていれば、自動で有効化される仕組みです。不要なスキルを無効にしたい場合は、`skills.allowBundled`でホワイトリスト管理ができます（詳しくはセキュリティ対策のセクションで解説します）。

---

## OpenClawの公式スキル全53種一覧【カテゴリ別】

OpenClawには53種の公式バンドルスキルがプリインストールされています。カテゴリごとに一覧を紹介します。

各スキルの「リスク」は、そのスキルがアクセスするデータや外部サービスの範囲に基づく目安です。リスクが高いほど、利用時に慎重な検討が必要になります。

### OpenClawのビジネス・業務効率化スキル（7種）

| スキル名 | 対応サービス | 主な機能 | リスク |
|---------|------------|---------|-------|
| gog | Google Workspace | Gmail・カレンダー・Tasks・Drive・Docs・Sheetsの操作 | Medium |
| himalaya | IMAP/SMTP | メールの送受信（Google以外のメールサービス向け） | High |
| things-mac | Things 3 | タスク管理（Mac専用） | Low |
| apple-reminders | リマインダー | Appleリマインダーの管理（Mac専用） | Low |
| trello | Trello | ボード・カード・リストの操作 | Medium |
| 1password | 1Password | パスワードの検索・取得 | Very High |
| nano-pdf | PDF | PDFの編集・操作 | Low |

**1password**はパスワード管理ツール全体へのアクセス権を持つため、リスクが非常に高い点に注意が必要です。利用する場合は、AI専用の保管庫を作成することを推奨します。

### OpenClawのノート管理スキル（4種）

| スキル名 | 対応サービス | 主な機能 | リスク |
|---------|------------|---------|-------|
| obsidian | Obsidian | ローカルファイルベースのノート管理 | Low |
| notion | Notion | クラウドベースのノート・ドキュメント管理 | Medium |
| apple-notes | Apple Notes | Appleメモの管理（Mac専用） | Low |
| bear-notes | Bear | Bearアプリのノート管理（Mac専用） | Low |

OpenClawをVM（仮想マシン）上で動かしている場合、`apple-notes`や`bear-notes`はMacローカル専用のため使えません。VM環境なら、クラウドベースの`notion`が最も導入しやすい選択肢です。

### OpenClawのチャット・SNSスキル（6種）

| スキル名 | 対応サービス | 主な機能 | リスク |
|---------|------------|---------|-------|
| slack | Slack | メッセージ履歴検索・チャンネル管理 | Medium |
| discord | Discord | メッセージ履歴検索・チャンネル管理 | Medium |
| wacli | WhatsApp | メッセージの送受信・履歴アクセス | Very High |
| imsg | iMessage | メッセージの送受信（Mac専用） | Very High |
| bluebubbles | iMessage（外部） | 外部サーバー経由のiMessage連携 | High |
| bird | X（Twitter） | ポスト・検索・アカウント操作 | Very High |

チャット・SNS系スキルは、**Toolsの`message`（メッセージ送信のみ）とは異なり、プラットフォームへのフルアクセス権**を持ちます。メッセージ履歴の読み取りや会話の同期も行えるため、利用は慎重に検討してください。

### OpenClawの開発者向けスキル（4種）

| スキル名 | 対応サービス | 主な機能 | リスク |
|---------|------------|---------|-------|
| github | GitHub | `gh` CLI経由でPR確認・Issue管理・Actions操作 | Medium |
| coding-agent | AIコーディングツール | Claude Code・Cursor等をバックグラウンドで呼び出し | Medium |
| tmux | ターミナル | 複数ターミナルセッションの管理 | Low |
| session-logs | ログ | 過去の会話ログの検索・分析 | Low |

`coding-agent`は、OpenClawから別のAIコーディングツールをバックグラウンドで起動できるスキルです。例えば、Telegramから「このリポジトリをクローンして、デモサイトを作って」と指示すると、Claude Codeが自律的にコーディングを行い、完了通知が届きます。

### OpenClawのクリエイティブスキル（5種）

| スキル名 | 対応サービス | 主な機能 | リスク |
|---------|------------|---------|-------|
| openai-image-gen | OpenAI | テキストからの画像生成（DALL-E） | Low |
| nano-banana-pro | Gemini | テキストからの画像生成（Gemini） | Low |
| video-frames | 動画 | 動画からのフレーム抽出 | Low |
| gifgrep | GIF | GIF画像の検索 | Low |
| canvas | キャンバス | 図やフローチャートの作成 | Low |

### OpenClawの音声スキル（4種）

| スキル名 | 対応サービス | 主な機能 | リスク |
|---------|------------|---------|-------|
| sag | ElevenLabs | 高品質なテキスト読み上げ（TTS） | Low |
| openai-whisper | Whisper（ローカル） | 音声をテキストに変換（オフライン） | Low |
| openai-whisper-api | Whisper（クラウド） | 音声をテキストに変換（API経由） | Low |
| sherpa-onnx-tts | Sherpa ONNX | オフラインでのテキスト読み上げ | Low |

### OpenClawの音楽スキル（4種）

| スキル名 | 対応サービス | 主な機能 | リスク |
|---------|------------|---------|-------|
| spotify-player | Spotify | 音楽の再生・プレイリスト管理 | Low |
| sonoscli | Sonos | Sonosスピーカーの操作 | Low |
| blucli | BluOS | BluOSデバイスの操作 | Low |
| songsee | 音声可視化 | オーディオの可視化 | Low |

### OpenClawのスマートホーム・生活スキル（4種）

| スキル名 | 対応サービス | 主な機能 | リスク |
|---------|------------|---------|-------|
| openhue | Philips Hue | スマートライトの操作 | Low |
| eightctl | Eight Sleep | スマートマットレスの温度制御 | Low |
| food-order | 複数プラットフォーム | フードデリバリーの注文 | High |
| ordercli | Foodora | Foodoraからの注文 | Medium |

### OpenClawのAI連携スキル（3種）

| スキル名 | 対応サービス | 主な機能 | リスク |
|---------|------------|---------|-------|
| gemini | Google Gemini | Geminiモデルの呼び出し | Low |
| oracle | Oracle CLI | Oracle CLIの操作 | Low |
| mcporter | MCP | MCP（Model Context Protocol）連携 | Medium |

### OpenClawのシステム・ユーティリティスキル（6種）

| スキル名 | 対応サービス | 主な機能 | リスク |
|---------|------------|---------|-------|
| clawhub | ClawHub | スキルの検索・インストール・更新 | Low |
| skill-creator | スキル作成 | 新しいスキルの作成支援 | Low |
| healthcheck | ヘルスチェック | OpenClawの稼働状態確認 | Low |
| summarize | 要約 | テキストや文書の要約 | Low |
| weather | 天気 | 天気予報の取得 | Low |
| peekaboo | macOS UI | macOSのUI要素操作（Mac専用） | High |

### OpenClawのその他のスキル（6種）

| スキル名 | 対応サービス | 主な機能 | リスク |
|---------|------------|---------|-------|
| goplaces | Google Places | 周辺の店舗・施設検索 | Low |
| local-places | ローカルプロキシ | ローカル環境経由の場所検索 | Low |
| camsnap | RTSPカメラ | ネットワークカメラの映像取得 | Medium |
| blogwatcher | RSS | RSSフィードの監視・通知 | Low |
| model-usage | 使用量トラッキング | AIモデルの利用状況モニタリング | Low |
| voice-call | 音声通話 | 音声通話の発着信 | High |

---

## OpenClawのおすすめスキル7選【目的別】

53種もあると「結局どれを入れればいいの？」と迷うかもしれません。ここでは、**導入が簡単で実用性の高いスキル**を7つ厳選して紹介します。

| # | スキル名 | カテゴリ | おすすめポイント | 活用例 |
|---|---------|---------|---------------|-------|
| ① | **gog** | ビジネス | Google Workspace全体（Gmail・カレンダー・Tasks・Drive等）を一元管理。最も実用性が高い | 「今日の未読メールを要約して」「今週の予定を教えて」 |
| ② | **summarize** | システム | 設定不要ですぐ使える。長文の要約に便利 | 「この記事を3行で要約して」→ ニュースや議事録の要約 |
| ③ | **weather** | システム | 設定不要。cron（定期実行）と組み合わせてデイリーブリーフに | 毎朝の天気予報をTelegramに自動通知 |
| ④ | **github** | 開発 | `gh` CLI経由でGitHub操作。CI/CDエラーの自動診断も可能 | 「このPRのビルド失敗の原因を調べて」→ エラーログを分析 |
| ⑤ | **nano-banana-pro** | クリエイティブ | Geminiの画像生成をOpenClaw経由で利用。APIキー設定のみで使える | 「ブログ用のアイキャッチ画像を作って」→ 画像生成 |
| ⑥ | **clawhub** | システム | ClawHubのスキル検索・インストールをAI経由で実行 | 「Slack連携できるスキルを探して」→ 検索＆インストール |
| ⑦ | **healthcheck** | システム | OpenClawの稼働状態をチェック。cronで定期監視に使える | 「ヘルスチェックして」→ システム状態レポート |

### OpenClawのおすすめスキルの組み合わせ例

スキルは単体でも便利ですが、**複数を組み合わせる**ことで、より強力な自動化が実現できます。

| 組み合わせ | 実現できること |
|-----------|-------------|
| **gog + summarize + weather + cron** | 毎朝決まった時間に、カレンダー予定・未読メール要約・天気予報をまとめて通知（デイリーブリーフ） |
| **github + coding-agent** | CI/CDエラー発生時にエラーログを自動分析し、修正PRの作成まで実行 |
| **blogwatcher + summarize** | 特定トピックのRSSフィードを監視し、新着記事を要約して毎日通知 |

---

## ClawHub（クローハブ）でOpenClawのスキルを拡張する方法

公式の53種では足りない場合は、**ClawHub（クローハブ）**からサードパーティ製のスキルをインストールできます。

### ClawHubとは？OpenClawの公式スキルストア

[ClawHub](https://clawhub.ai)は、OpenClawの公式スキルレジストリです。コミュニティが開発した**13,700以上のスキル**が登録されています（[公式ドキュメント](https://docs.openclaw.ai/tools/clawhub)）。

最も人気のあるスキルは**Capability Evolver**（35,000ダウンロード）で、AIエージェントが自分自身の動作履歴を分析し、自動的に機能を改善していくメタスキルです。

### ClawHubからOpenClawにスキルをインストールする手順

ClawHubからのスキルインストールは、以下の3ステップで完了します。

**①スキルを検索する**
```bash
clawhub search キーワード
```

**②スキルをインストールする**
```bash
clawhub install スキル名
```

**③インストール済みスキルを更新する**
```bash
clawhub update --all
```

インストールしたスキルは、次回のOpenClawセッション開始時に自動で読み込まれます。

### ClawHubのOpenClawスキルのセキュリティリスクと対策

ClawHubには便利なスキルが多数ある一方、**セキュリティリスクも存在します**。

セキュリティ企業Snykの調査では、ClawHub上の約3,984件のスキルをスキャンした結果、**約283件（約7%）に重大な脆弱性が見つかっています**（[The Hacker News](https://thehackernews.com/2026/02/openclaw-integrates-virustotal-scanning.html)）。悪意のあるスキルがインフォスティーラー（情報窃取マルウェア）を仕込んでいた事例も報告されています。

2026年2月からは**VirusTotal自動スキャン**が統合され、悪意のあるスキルのダウンロードがブロックされるようになりましたが、インストール前の確認は引き続き重要です。

**インストール前にやるべきこと：**

| 対策 | 方法 |
|------|------|
| スキルの中身を確認 | `clawhub inspect スキル名` でSKILL.mdの内容と権限を確認 |
| GitHubリポジトリを確認 | スキルのソースコードを目視でレビュー |
| サンドボックスで実行 | 本番環境に入れる前にテスト環境で動作確認 |

---

## OpenClawのSkills利用時のセキュリティ対策

### OpenClawのSkillsのリスクレベル一覧

公式バンドルスキル53種のリスクレベルをまとめました。**公式スキルはOpenClaw開発元が管理しているため、基本的に安全**です。リスクが高いのは、外部サービスへのフルアクセス権を持つスキルです。

| リスクレベル | 該当スキル | 特徴 |
|------------|-----------|------|
| **Very High** | wacli、imsg、bird、1password | 個人データ・SNS・パスワードへのフルアクセス |
| **High** | himalaya、bluebubbles、food-order、peekaboo、voice-call | メール送受信・UI操作・通話など影響範囲が大きい |
| **Medium** | notion、trello、gog、slack、discord、github、coding-agent、ordercli、mcporter、camsnap | 外部サービス連携あり。OAuth等で権限管理可能 |
| **Low** | その他33種 | ローカル処理中心。外部通信が限定的 |

### OpenClawのSkillsを安全に使うための5つのポイント

**①`skills.allowBundled`でホワイトリスト管理する**

バンドルスキルはデフォルトで全て有効になります。使うスキルだけを明示的に許可するのが安全です。

```json
{
  "skills": {
    "allowBundled": [
      "gog", "github", "summarize",
      "weather", "clawhub", "healthcheck"
    ]
  }
}
```

この設定をOpenClawの設定ファイル（`openclaw.json`）に記述すると、リストに含まれないスキルは無効化されます。

**②リスクレベルが「High」以上のスキルは本当に必要か検討する**

特に`1password`（パスワード管理全体へのアクセス）や`wacli`（WhatsAppのフルアクセス）は、利用する前に本当に必要かを慎重に判断してください。

**③ClawHubスキルはインストール前に`clawhub inspect`で確認する**

```bash
clawhub inspect スキル名
```

このコマンドで、スキルが要求する権限や環境変数、ソースコードの内容を確認できます。

**④SKILL.mdの内容をコードレビューと同様に確認する**

OpenClawのスキルは`SKILL.md`というマークダウンファイルで定義されています。コードレビューと同じ感覚で、中身を読んでから有効にすることをお勧めします。

**⑤テスト環境で先に動作確認する**

本番環境に導入する前に、サンドボックスやテスト用の環境で動作を確認しましょう。特にClawHubからインストールしたサードパーティ製スキルには、この手順が重要です。

---

## まとめ：OpenClawのSkillsを理解して、AIエージェントを使いこなそう

今回、OpenClawのSkillsについて解説してきましたが、いかがだったでしょうか。

この記事のポイントを振り返ります。

| ポイント | 内容 |
|---------|------|
| Skillsの役割 | AIエージェントに使い方を教える「教科書」。権限は変わらない |
| 公式スキル | 53種がプリインストール。OpenClaw開発元が管理しており安全 |
| おすすめスキル | まずはgog（メール・カレンダー）、summarize（要約）、weather（天気）から |
| ClawHub | 13,700以上のサードパーティスキル。便利だがセキュリティに注意 |
| セキュリティ | `skills.allowBundled`でホワイトリスト管理。インストール前の確認を徹底 |

OpenClawのSkillsは、正しく理解して使えば、日常業務を大幅に効率化できる仕組みです。まずは安全なスキルから1つ試してみて、徐々に活用の幅を広げていくことをお勧めします。

<!-- 内部リンク: openclaw セキュリティ記事 -->
<!-- 内部リンク: openclaw 使い方記事 -->
<!-- 内部リンク: openclaw できること記事 -->
