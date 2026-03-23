---
topic: Mac mini OpenClaw + Claude Code セットアップログ
status: in-progress
date: 2026-03-21
updated: 2026-03-22
use_for: 記事ネタ（mac mini記事 / 使い方記事）
---

# Mac mini セットアップログ

## 完了済み（記事化済み）
- [x] OpenClawインストール
- [x] Slack連携（Socket Mode）

---

## ステップ1: Claude Codeインストール（2026-03-22 完了）

### 実施手順
```bash
# Node.jsバージョン確認 → v25.7.0（OK）
node --version

# Claude Codeインストール（sudoが必要だった）
sudo npm install -g @anthropic-ai/claude-code

# 初回起動 → Anthropic Console accountを選択
claude

# APIキーを専用キーに切り替え
claude config set apiKey
# → claude-code-macmini用のキーを設定
```

### つまづきポイント（記事ネタ）
- `npm install -g` で権限エラー（EACCES）→ `sudo` が必要
- 認証方法の選択肢が3つあり、API課金なら「Anthropic Console account」を選ぶ
- APIキーはOpenClaw用とClaude Code用で分けるべき（コスト管理のため）
- `claude config set apiKey` で専用キーに切り替え可能

### 気づき
- OpenClaw用APIキーとClaude Code用APIキーは分けた方がよい
  - 用途別のコスト把握ができる
  - 万が一の漏洩時に片方だけ無効化できる

---

## ステップ2: GitHubアクセス設定（2026-03-22 進行中）

### サービスアカウントの検討

#### 結論
- GitHub・Notion用にBot専用アカウントを作る
- 会社のGoogle Workspaceでメールアドレスを取得するのがベスト（月680円〜）
- 上司への相談が必要 → 回答待ちの間は個人アカウントで進める

#### 上司への相談テンプレート
> OpenClaw（AI自動化ツール）用のサービスアカウントとして、Google Workspaceユーザーを1つ追加したいです。
>
> **なぜ必要か：**
> - GitHub・Notionなど外部サービスにBot用アカウントを作るのに、メールアドレスが必要
> - 個人アカウントと分けることで、「誰がやった操作か」が明確になる（コミット履歴やログで混ざらない）
> - Botに必要最小限の権限だけ与えられる（個人アカウントのフル権限を渡さなくて済む）
>
> **用途：**
> - Slack経由でのPRレビュー、バグ修正、チケット作成の自動化
> - GitHub・Notionへのアクセス（最小権限で運用）
>
> **コスト：** 月680円〜（Google Workspace 1ユーザー追加）

### SSH鍵の設定
```bash
# Mac miniでSSH鍵を作成
ssh-keygen -t ed25519 -C "openclaw@bocek"
# → 全部Enterでスキップ

# 公開鍵を表示してコピー
cat ~/.ssh/id_ed25519.pub

# → GitHubのSettings > SSH keys に登録
# Title: openclaw-macmini
```

### クローン対象リポジトリ
| リポジトリ | 用途 |
|-----------|------|
| taskhub-monorepo | メイン開発 |
| taskhub-backend | バックエンド |
| taskhub-neo-frontend | フロントエンド |
| agepoyo | 開発 |
| uogashi | 開発 |
| bocek-infrastructure | インフラ |
| bocek-architecture-document | 設計ドキュメント |

### メモ欄（作業中に記録）
- Mac miniのopenclawアカウントからGitHubへSSH接続 → Permission denied（鍵未登録のため）
- Gmailアカウント作成を試みたが電話番号認証で弾かれた
- 会社のGoogle Workspaceで専用アドレス（openclaw@bocek.co.jp）を取得できた
- Bot用GitHubアカウント「openclaw-bocek」を作成、Bocek-Inc Orgに招待・join完了
- SSH鍵を登録し、GitHub接続確認OK
- MacBookからSSHでMac miniに接続して操作する方法を確立（コピペ問題の解決）

---

## ステップ3: リポジトリをMac miniにクローン

### 方針
- 開発リポジトリ7つをすべてMac miniにクローン
- `~/repos/` 配下に配置
- Claude Codeから複数リポジトリを横断してバグ修正・PRレビュー等を行う

### 手順（SSH鍵登録後に実施）
```bash
mkdir -p ~/repos && cd ~/repos
git clone git@github.com:Bocek-Inc/taskhub-monorepo.git
git clone git@github.com:Bocek-Inc/taskhub-backend.git
git clone git@github.com:Bocek-Inc/taskhub-neo-frontend.git
git clone git@github.com:Bocek-Inc/agepoyo.git
git clone git@github.com:Bocek-Inc/uogashi.git
git clone git@github.com:Bocek-Inc/bocek-infrastructure.git
git clone git@github.com:Bocek-Inc/bocek-architecture-document.git
```

### メモ欄（作業中に記録）


---

## ステップ4: Claude Code Skillの設定（2026-03-22 完了）

### 結論
別途インストール不要。`coding-agent`スキルがOpenClaw 2026.3.2に同梱されており、`claude`コマンドが入っていれば自動的に`✓ ready`になる。

### 実施した設定
```json
// ~/.claude/settings.json
{
  "permissions": {
    "defaultMode": "bypassPermissions",
    "additionalDirectories": ["/Users/openclaw/repos"]
  },
  "env": {
    "GH_TOKEN": "（GitHubトークン）"
  }
}
```

### つまづきポイント（記事ネタ）
- `coding-agent`スキルは追加インストール不要だった（同梱済み）
- `bypassPermissions`を設定しないと、自動化タスク中に確認ダイアログが大量に出る
- `additionalDirectories`を設定しないと`~/repos/`配下のリポジトリにアクセスできない

---

## ステップ5: 動作確認（2026-03-22 完了）

### Slackから試したこと
- 「taskhub-backendの最新PRを教えて」→ PR一覧が返ってきた ✅

### 結果記録
- Slackの任意チャンネルから`@OpenClawMacmini`でメンション → PR一覧取得成功（2026-03-22）

---

## トラブルシューティング

| 問題 | 原因 | 解決方法 |
|------|------|----------|
| npm install -g で EACCES | 権限不足 | `sudo` を付けて実行 |
| Slack接続が深夜に切れる | ネットワーク不安定（当初スリープかと思ったがネットワーク問題だった） | ネットワーク復旧で解決 |
| GitHub SSH接続 Permission denied | SSH鍵未登録 | 鍵作成→GitHub登録で解決 |
| Gmail作成で電話番号認証に弾かれた | 同一番号での複数アカウント制限 | 会社のGoogle Workspaceで専用アドレスを作る方針に |
| Slackメンションに反応しない | `groupPolicy: "allowlist"`がデフォルト | `"open"`に変更 |
| launchd起動後にSlack接続が切れる | Slackトークンがplistに未設定（zshrcのenv変数はlaunchdに引き継がれない） | plistの`EnvironmentVariables`にトークンを追加 |
| coding-agentがGitHub認証エラー | `gh`のトークンがkeyringに保存されず、サブプロセスに引き継がれない | `~/.config/gh/hosts.yml`に直接トークンを書き込む |

---

## ステップ6: Notion連携（2026-03-22 完了）

### 実施内容
- Notion Internal Integration「OpenClaw Bot」を作成
  - 権限: Read/Update/Insert content + コメント読み書き
- `NOTION_API_KEY`を`~/.claude/settings.json`とplistに追加
- notionスキルが`✓ Ready`になったことを確認

### つまづきポイント（記事ネタ）
- `ntn_`始まりが新しいNotionトークン形式（旧: `secret_`）
- Integration作成後、アクセスさせたいページに個別で「Connections → OpenClaw Bot」を追加する必要がある
- 編集履歴に「OpenClaw Bot」と表示されるので誰が編集したか明確

---

## ステップ7: ナレッジ整備・動作確認（2026-03-22 完了）

### 実施内容
- `~/.openclaw/workspace/TOOLS.md`に全リポジトリの役割・困りごとマップを追記
  - Project D（PHP→Kotlin移行）の背景
  - ビジネス用語からリポジトリへのマッピング表
  - `openclaw-implementation-support`リポジトリも追加
- `cc-company`プラグインをインストール（`/company`スキルが使えるように）
- `/companyスキル`: `claude plugin marketplace add` → `claude plugin install company@cc-company`

### 動作確認結果
- Slackスレッドからバグ修正依頼 → PRが自動作成された ✅
- Notionページを読んで仕様確認 ✅
- 複数チャンネルからのメンションに反応 ✅

### 残課題
- ~~チャンネルによってスレッド返信にならないことがある~~ → SOUL.mdで解決
- ~~口調がまちまち~~ → SOUL.mdで解決

### 口調・スレッド返信の設定（記事ネタ）
`~/.openclaw/workspace/SOUL.md` に以下を追記することで改善：
```markdown
## 日本語での応対ルール
- 常に日本語で返答する
- 口調: 丁寧すぎず、フレンドリー。「〜ですね」「〜しました」程度
- 「Great!」「承知いたしました」などの過剰な前置きは不要
- Slackでの返答は必ずスレッド内で行う（チャンネルに新規投稿しない）
```
- SOUL.mdはOpenClawのキャラクター・行動指針を定義するファイル
- チャンネルごとに口調を変えることもできる（例: 特定チャンネルだけギャル口調など）
- デフォルトは英語・フォーマルなので、日本語チームで使う場合は必ず設定すべきポイント

---

## 今後のTODO
- [ ] 上司にGoogle Workspace追加ユーザーを相談
- [ ] Bot用GitHubアカウント作成（会社アドレス取得後）
- [ ] Notion Integration作成
- [ ] 個人アカウントのSSH鍵を削除→Bot用に切り替え
