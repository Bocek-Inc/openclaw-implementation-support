# OpenClaw ブラウザ操作機能 調査レポート

調査日: 2026-03-22

---

## 1. OpenClawはブラウザを操作できるか？

**結論: はい、可能。** OpenClawはChrome DevTools Protocol (CDP) を通じて、ブラウザを本格的に操作できる。スクリーンショットに頼るビジュアル推論方式ではなく、ブラウザのレンダリングエンジンとプロトコルレベルで通信するため、高速かつ正確な操作が可能。

### 1.1 実現可能な操作

| 操作 | 対応状況 | 備考 |
|------|----------|------|
| クリック | 対応 | ref番号（例: e12）で要素を指定してクリック |
| テキスト入力 | 対応 | `type e15 "text"` のような形式 |
| スクロール | 対応 | ページ操作全般に対応 |
| フォーム送信 | 対応 | フォームの自動入力・送信が可能 |
| ドラッグ&ドロップ | 対応 | GUI操作全般をカバー |
| スクリーンショット撮影 | 対応 | `--labels`オプションでref番号オーバーレイ付きも可能 |
| PDF出力 | 対応 | ページのPDFエクスポートに対応 |
| ファイルアップロード | 対応 | ファイル選択ダイアログの操作が可能 |
| ネットワーク傍受 | 対応 | リクエスト・レスポンスの監視が可能 |
| 動画録画 | 対応 | ブラウザセッションの動画記録が可能 |

### 1.2 技術的な仕組み

**基盤技術: Chrome DevTools Protocol (CDP)**

- CDPはChromeの内蔵DevToolsと同じ低レベル通信プロトコル
- Page、Network、DOM、Runtime等のドメインにわたる約300のコマンドに対応
- 内部的にはPlaywrightをCDP制御エンジンとして使用し、高レベルの抽象化を提供

**ページ認識方式: アクセシビリティツリーベース**

- `Accessibility.getFullAXTree`でアクセシビリティツリーを取得
- ロールベースの要素参照（ref）を生成し、AIがページ構造を理解
- `--format aria`オプションでARIAツリーの直接取得も可能
- スクリーンショット+画像認識ではなく、構造的にページを理解するためLLMトークンを節約

### 1.3 ブラウザ制御の3つのモード

| モード | ポート | 用途 | 特徴 |
|--------|--------|------|------|
| **Extension Relay** | 18792 | 既存Chromeタブの制御 | ログイン状態を保持したまま操作可能。普段使いのブラウザを直接制御 |
| **OpenClaw Managed** | 18800-18899 | 隔離されたChromiumインスタンス | 安全な自動化向け。個人ブラウザとは完全分離 |
| **Remote CDP** | 任意 | クラウドホストのブラウザ | VPSやリモート環境から接続 |

### 1.4 主要コマンド

```bash
# ブラウザの起動
openclaw browser start

# ページのスナップショット（インタラクティブ要素の一覧取得）
openclaw browser snapshot --interactive

# 要素をクリック
openclaw browser click e12

# テキスト入力
openclaw browser type e15 "入力テキスト"

# スクリーンショット（refラベル付き）
openclaw browser snapshot --labels
```

---

## 2. デバッグ用途での活用

### 2.1 Webアプリのデバッグ

**対応可能。Chrome DevTools MCP統合により高度なデバッグが可能。**

| デバッグ機能 | 対応状況 | 詳細 |
|--------------|----------|------|
| DOM要素の確認 | 対応 | アクセシビリティツリーやスナップショットで構造を把握 |
| コンソールログ取得 | 対応 | Chrome DevTools MCP経由でRuntime.consoleログにアクセス |
| ネットワーク監視 | 対応 | Network.requestやresponseの傍受・分析が可能 |
| パフォーマンス計測 | 対応 | `performance_start_trace`でCore Web Vitals（LCP, CLS, INP）を計測 |
| デバイスエミュレーション | 対応 | モバイル等の各種デバイス表示をエミュレート |

### 2.2 E2Eテストの自動実行

**対応可能。自然言語でテスト定義ができる点が大きな特徴。**

OpenClawのE2Eテスト機能の利点:

- **自然言語テスト定義**: テストシナリオを日本語/英語で記述可能。コードを書く必要がない
- **セルフヒーリング**: UIの変更に対して自動的に適応。セレクタが変わっても動作し続ける
- **クロスプラットフォーム**: デスクトップ・モバイル両方のテストに対応
- **インテリジェントレポート**: テスト結果を分析し、根本原因を推論

典型的なユースケース:
- エンドツーエンドUIテスト
- 回帰テスト
- フォーム駆動のワークフローテスト
- 自動データ抽出

### 2.3 エラー画面のスクショ→Slack報告

**対応可能。** OpenClawの既存機能の組み合わせで実現できる。

想定される構成:
1. **cronスケジューリング**でWebアプリを定期的にチェック
2. **ブラウザツール**でページを開き、エラー状態を検出
3. **スクリーンショット撮影**でエラー画面をキャプチャ
4. **Slackスキル**でキャプチャ画像とエラー情報をチャンネルに投稿

OpenClawはSlack、Discord、Telegram等のメッセージングプラットフォームとネイティブ統合されているため、このようなワークフローは比較的容易に構築できる。

---

## 3. ブラウザ操作関連のスキル・プラグイン

### 3.1 主要なClawHubスキル

ClawHub（公式スキルレジストリ）には2026年2月時点で13,729以上のスキルが登録されている。ブラウザ関連の主要スキル:

| スキル名 | 概要 |
|----------|------|
| **agent-browser** | Rustベースの高速ヘッドレスブラウザ自動化CLI。ナビゲーション、クリック、タイプ、スナップショット等をサポート。Node.jsフォールバック付き |
| **browser-use** | ブラウザ操作の基本スキル |
| **browser-js** | JavaScript実行を含むブラウザ操作 |
| **browser-automation-skill** | 汎用ブラウザ自動化スキル |
| **webapp-testing** | Webアプリケーションのテスト用スキル |
| **camofox-browser** | Camoufox（C++フィンガープリントスプーフィング）を使ったヘッドレスブラウザプラグイン |

### 3.2 Chrome DevTools MCP統合

GoogleのChrome DevTools MCPサーバーとの統合により、以下が可能:
- ページの読み取り・操作
- 要素のクリック・フォーム入力
- パフォーマンストレースの記録
- ネットワークリクエストの分析
- Core Web Vitalsの計測

### 3.3 Computer Use機能について

OpenClawは主にCDP（Chrome DevTools Protocol）ベースのブラウザ操作を採用しており、Anthropic Claude の Computer Use（画面キャプチャ+座標クリック方式）とは異なるアプローチを取っている。

- **OpenClawの方式**: プロトコルレベルでブラウザと通信。高速・正確だがブラウザ外のデスクトップ操作には非対応
- **Computer Useの方式**: スクリーンショット→画像解析→マウス/キーボード操作。汎用的だが低速

ただし、OpenClawはスクリーンキャプチャ機能も持っているため、ブラウザ以外のデスクトップアプリケーション操作も一定程度可能（macOSのFull Disk Access、Accessibility、Screen Recording権限が必要）。

---

## 4. 制約・注意点

### 4.1 VPS vs Mac mini の違い

| 項目 | VPS（Linux） | Mac mini |
|------|--------------|----------|
| ブラウザモード | ヘッドレスが基本 | GUI付きブラウザも利用可能 |
| セットアップ | Docker対応。ヘッドレスChromiumサンドボックスあり | Homebrew等でネイティブインストール |
| Extension Relay | VPS側にはブラウザがないため、ローカルPCのChromeにNodeホストを入れてリレーする構成が必要 | 直接Chromeを制御可能 |
| 権限 | 基本的に制約なし | Full Disk Access、Accessibility、Screen Recording の3権限が必要 |
| ヘッドレス運用 | デフォルトでヘッドレス | HDMIダミープラグ（約$8-10）で仮想ディスプレイを認識させるか、`~/.openclaw/config.json`で`"headless": true`を設定 |
| リモートアクセス | SSH | SSH + RustDesk（リモートデスクトップ）でGUI操作も可能 |
| パフォーマンス | サーバースペック次第 | Apple Siliconで高速。常時稼働に向いている |

### 4.2 ヘッドレス vs GUI付きブラウザ

- **ヘッドレスブラウザ**: VPS、Docker、サーバー環境で使用。`"headless": true`で設定
- **GUI付きブラウザ**: Mac miniやデスクトップ環境で使用。Extension Relayモードなら普段使いのChromeをそのまま制御可能
- **OpenClaw Managedモード**: 隔離されたChromiumインスタンスを起動するため、ヘッドレス/GUI両方に対応

### 4.3 その他の制約・注意点

- **セキュリティ**: Extension Relayモードは既存のログイン状態を共有するため、悪意のある指示には注意が必要
- **認証が必要なサイト**: Extension Relayモードならログイン済みセッションを利用可能。Managedモードではプロファイル機能で認証情報を保持
- **JavaScript SPAサイト**: CDPベースのためSPAも問題なく操作可能
- **Linux環境のトラブルシューティング**: 公式ドキュメントに専用ページあり（依存ライブラリ不足等）

---

## 5. まとめ

OpenClawのブラウザ操作機能は非常に充実しており、以下の点で実用的:

1. **CDP（Chrome DevTools Protocol）ベース**の高速・正確なブラウザ制御
2. **3つの接続モード**（Extension Relay / Managed / Remote CDP）で多様な環境に対応
3. **アクセシビリティツリーベース**のページ認識により、LLMトークンを効率的に使用
4. **E2Eテスト**を自然言語で定義でき、セルフヒーリング機能で保守コストが低い
5. **Chrome DevTools MCP統合**でパフォーマンス計測やネットワーク監視も可能
6. **ClawHub**に多数のブラウザ関連スキルが公開済み
7. **Mac mini**ならGUI付きブラウザも使え、VPSではヘッドレスで運用可能

デバッグ用途としては、コンソールログ取得、ネットワーク監視、パフォーマンス計測まで対応しており、エラー画面のスクショ→Slack通知のようなワークフローも構築可能。

---

## Sources

- [Browser (OpenClaw-managed) - OpenClaw 公式ドキュメント](https://docs.openclaw.ai/tools/browser)
- [How OpenClaw Controls Your Browser: Modes, CDP, and Automation (2026)](https://blog.laozhang.ai/en/posts/openclaw-browser-control)
- [OpenClaw Browser Automation: Web Browsing & Computer Use [2026]](https://clawtank.dev/blog/openclaw-browser-automation)
- [Mastering OpenClaw Browser Capabilities: 5 Core Features](https://help.apiyi.com/en/openclaw-browser-automation-guide-en.html)
- [E2E Test Automation with OpenClaw: A Practical Guide](https://jangwook.net/en/blog/en/openclaw-e2e-test-automation-guide/)
- [OpenClaw + Chrome DevTools MCP (Medium)](https://medium.com/@tentenco/openclaw-chrome-devtools-mcp-how-to-give-your-local-agent-full-browser-control-a4e7c723a649)
- [agent-browser skill (GitHub)](https://github.com/openclaw/skills/blob/main/skills/thesethrose/agent-browser/SKILL.md)
- [OpenClaw on Mac Mini: The Best Always-On Home Server Setup](https://boilerplatehub.com/blog/openclaw-mac-mini)
- [Running OpenClaw on a Headless Mac](https://travis.media/blog/running-openclaw-headless-mac/)
- [Browser Troubleshooting - OpenClaw](https://docs.openclaw.ai/tools/browser-linux-troubleshooting)
- [OpenClaw Wikipedia](https://en.wikipedia.org/wiki/OpenClaw)
- [webapp-testing skill (LobeHub)](https://lobehub.com/skills/openclaw-skills-webapp-testing)
- [OpenClaw Browser Relay Guide 2026](https://www.aifreeapi.com/en/posts/openclaw-browser-relay-guide)
- [Browser Control | DeepWiki](https://deepwiki.com/moltbook/openclaw/13.4-browser-control)
