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

## ステップ4: Claude Code Skillの設定

### 手順
```bash
# OpenClawのClaude Code Skillをインストール
# 参考: https://github.com/Enderfga/openclaw-claude-code-skill
```

### 設定ファイルに追加する内容（要調査）
- ワークスペースのパス指定
- 対象リポジトリの設定

### メモ欄（作業中に記録）


---

## ステップ5: 動作確認

### Slackから試すこと
1. 「secretary/todos/ の最新TODOを見せて」
2. 「research/topics/ のリサーチ一覧を出して」
3. 「inbox/にメモを追加して」（書き込みテスト）

### 結果記録


---

## トラブルシューティング

| 問題 | 原因 | 解決方法 |
|------|------|----------|
| npm install -g で EACCES | 権限不足 | `sudo` を付けて実行 |
| Slack接続が深夜に切れる | ネットワーク不安定（当初スリープかと思ったがネットワーク問題だった） | ネットワーク復旧で解決 |
| GitHub SSH接続 Permission denied | SSH鍵未登録 | 鍵作成→GitHub登録で解決 |
| Gmail作成で電話番号認証に弾かれた | 同一番号での複数アカウント制限 | 会社のGoogle Workspaceで専用アドレスを作る方針に |
| `openclaw daemon install` でBootstrap failed | SSH/ヘッドレス経由だとlaunchctlのGUIセッションが見えない | Mac miniにデスクトップログインした状態（画面共有 or モニター接続）でターミナルから実行する |
| `nohup openclaw gateway &` が即終了 | 既に `openclaw gateway &` で起動したプロセスがポートを掴んでいた | 既存プロセスを活かし `disown %1` でSSHセッションから切り離す |
| `disown -a` がjob not found | zshでは `-a` オプションが使えない | `disown %1` で対応 |

---

## 今後のTODO
- [ ] 上司にGoogle Workspace追加ユーザーを相談
- [ ] Bot用GitHubアカウント作成（会社アドレス取得後）
- [ ] Notion Integration作成
- [ ] 個人アカウントのSSH鍵を削除→Bot用に切り替え
