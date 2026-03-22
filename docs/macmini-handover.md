# Mac mini セットアップ引き継ぎ

このファイルはMac mini上のClaude Codeへの作業引き継ぎ用です。作業完了後は削除してOKです。

---

## あなた（Claude Code）の状況

- Mac mini上で動いています
- ユーザーは「しゅーへい」（開発初心者）です。1ステップずつ確認しながら進めてください
- このリポジトリは `~/repos/openclaw-implementation-support/` にクローン済みです
- 他の開発リポジトリも `~/repos/` にクローン済みです

## 全体の目的

Mac miniでOpenClaw + Claude Codeを常時稼働させ、Slack経由で以下の業務を自動化したい：

- PRコードレビューの一次対応
- バグ修正→PR作成
- 要求リスト（Notion）の管理
- 開発チケットの自動作成
- 技術リサーチ・ナレッジ蓄積

詳細は `.company/secretary/notes/2026-03-21-openclaw-slack-workflow-ideas.md` を参照。

## 完了済みのステップ

1. ✅ OpenClawインストール・Slack連携（Socket Mode）
2. ✅ Claude Codeインストール（`sudo npm install -g @anthropic-ai/claude-code`）
3. ✅ Claude Code認証（Anthropic Console → 専用APIキー `claude-code-macmini` を設定済み）
4. ✅ Bot用GitHubアカウント作成（`openclaw-bocek`、メール: `openclaw@bocek.co.jp`）
5. ✅ SSH鍵作成・GitHub登録、Bocek-Inc Orgにjoin済み
6. ✅ 開発リポジトリ7つ + このリポジトリをクローン（`~/repos/` 配下）

## 次にやるステップ

### ステップ4: Claude Code Skillの設定
- OpenClawからClaude Codeを呼び出せるようにする
- 参考: https://github.com/Enderfga/openclaw-claude-code-skill
- OpenClawの設定ファイル: `~/.openclaw/openclaw.json`
- 複数リポジトリ（~/repos/配下）を横断して作業できるようにしたい

### ステップ5: Slackから動作確認
- Slackから簡単なタスクを投げて、Claude Codeが応答するか確認
- 例: 「taskhub-backendの最新PRを確認して」

## クローン済みリポジトリ一覧（~/repos/）

- taskhub-monorepo
- taskhub-backend
- taskhub-neo-frontend
- agepoyo
- uogashi
- bocek-infrastructure
- bocek-architecture-document
- openclaw-implementation-support（このリポジトリ）

## 重要なルール

- 作業の記録は `.company/research/topics/macmini-setup-log.md` に追記すること（記事ネタになる）
- 各ステップでつまづいたこと・気づいたことも記録すること
- しゅーへいは開発初心者なので、わかりやすく1ステップずつ説明すること
- 不明点があれば必ず確認してから進めること
- 日本語で対応すること

## 関連ファイル

- `.company/research/topics/macmini-setup-log.md` — セットアップの全記録
- `.company/secretary/notes/2026-03-21-openclaw-slack-workflow-ideas.md` — やりたいことの全体像
- `.company/research/topics/openclaw-claudecode-integration.md` — Claude Code連携のリサーチ
