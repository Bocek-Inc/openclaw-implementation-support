# Skills・ClawHub関連

## スキルとは
- AIに特定タスクの実行方法を教える「テキストブック（教科書）」
- Tools（ツール）が「臓器」＝何ができるかを決定、Skills（スキル）が「教科書」＝ツールの組み合わせ方を教える
- スキルはマークダウン形式の指示文書（SKILL.md）で定義される
- スキルを入れてもOpenClawの権限は変わらない（権限はToolsで制御）
- [出典: 公式ドキュメント https://docs.openclaw.ai/tools/skills, WenHao Yu記事]

## スキルが動作する3条件
1. **Configuration（設定）**: Toolsで該当機能が許可されている（例: exec）
2. **Installation（導入）**: 必要なCLIツールやブリッジがインストール済み
3. **Authorization（認可）**: 外部サービスへのログイン・認証が完了
- [出典: WenHao Yu記事 2026-02-05]

## スキルの3種類
1. **Bundled（組み込み）**: OpenClawにプリインストール（53種類）
2. **Managed（ClawHub経由）**: ClawHubからインストール
3. **Workspace（ローカル）**: ユーザーが自作・ローカル配置
- **優先度**: ワークスペース(`<workspace>/skills`) > 管理・ローカル(`~/.openclaw/skills`) > バンドル
- **形式**: 各スキルはYAML frontmatter付きのSKILL.mdファイルで定義
- **追加の読み込みパス**: `skills.load.extraDirs` で設定可能（最低優先度）
- **デフォルト動作**: バンドルスキルは自動ロード。対応CLIツールがインストール済みなら自動で有効化
- **ホワイトリスト制御**: `skills.allowBundled` で有効にするスキルを明示的に指定可能
- [出典: 公式ドキュメント https://docs.openclaw.ai/tools/skills]

## バンドルスキル全53種（カテゴリ別）

### ノート（4）
| スキル名 | 対応サービス | リスク |
|---------|------------|-------|
| obsidian | Obsidian | Low |
| notion | Notion | Medium |
| apple-notes | Apple Notes | Low |
| bear-notes | Bear | Low |

### タスク管理（3）
| スキル名 | 対応サービス | リスク |
|---------|------------|-------|
| things-mac | Things 3 | Low |
| apple-reminders | Reminders | Low |
| trello | Trello | Medium |

### ビジネス・メール（2）
| スキル名 | 対応サービス | リスク |
|---------|------------|-------|
| gog | Google Workspace（Gmail/Calendar/Tasks/Drive/Docs/Sheets） | Medium |
| himalaya | IMAP/SMTP（メール送受信のみ） | High |

### チャット（5）
| スキル名 | 対応サービス | リスク |
|---------|------------|-------|
| slack | Slack | Medium |
| discord | Discord | Medium |
| wacli | WhatsApp | Very High |
| imsg | iMessage | Very High |
| bluebubbles | iMessage（外部） | High |

### SNS（1）
| スキル名 | 対応サービス | リスク |
|---------|------------|-------|
| bird | X (Twitter) | Very High |

### 開発（4）
| スキル名 | 対応サービス | リスク |
|---------|------------|-------|
| github | GitHub（gh CLI経由） | Medium |
| coding-agent | AIコーディング（Claude Code, Cursor等） | Medium |
| tmux | ターミナル管理 | Low |
| session-logs | ログ検索・分析 | Low |

### 音楽（4）
| スキル名 | 対応サービス | リスク |
|---------|------------|-------|
| spotify-player | Spotify | Low |
| sonoscli | Sonos | Low |
| blucli | BluOS | Low |
| songsee | Audio Visualization | Low |

### スマートホーム（2）
| スキル名 | 対応サービス | リスク |
|---------|------------|-------|
| openhue | Philips Hue | Low |
| eightctl | Eight Sleep | Low |

### フードデリバリー（2）
| スキル名 | 対応サービス | リスク |
|---------|------------|-------|
| food-order | 複数プラットフォーム | High |
| ordercli | Foodora | Medium |

### クリエイティブ（5）
| スキル名 | 対応サービス | リスク |
|---------|------------|-------|
| openai-image-gen | OpenAI画像生成 | Low |
| nano-banana-pro | Gemini画像生成 | Low |
| video-frames | 動画フレーム抽出 | Low |
| gifgrep | GIF検索 | Low |
| canvas | キャンバス操作 | Low |

### 音声（4）
| スキル名 | 対応サービス | リスク |
|---------|------------|-------|
| sag | ElevenLabs TTS | Low |
| openai-whisper | 音声テキスト変換（ローカル） | Low |
| openai-whisper-api | 音声テキスト変換（クラウド） | Low |
| sherpa-onnx-tts | オフラインTTS | Low |

### セキュリティ（1）
| スキル名 | 対応サービス | リスク |
|---------|------------|-------|
| 1password | 1Password | Very High |

### AI連携（3）
| スキル名 | 対応サービス | リスク |
|---------|------------|-------|
| gemini | Gemini | Low |
| oracle | Oracle CLI | Low |
| mcporter | MCP連携 | Medium |

### システム・ユーティリティ（6）
| スキル名 | 対応サービス | リスク |
|---------|------------|-------|
| clawhub | スキル管理 | Low |
| skill-creator | スキル作成 | Low |
| healthcheck | ヘルスチェック | Low |
| summarize | 要約 | Low |
| weather | 天気情報 | Low |
| peekaboo | macOS UI操作 | High |

### 場所（2）
| スキル名 | 対応サービス | リスク |
|---------|------------|-------|
| goplaces | Google Places | Low |
| local-places | ローカルプロキシ | Low |

### メディア（1）
| スキル名 | 対応サービス | リスク |
|---------|------------|-------|
| camsnap | RTSPカメラ | Medium |

### ニュース（1）
| スキル名 | 対応サービス | リスク |
|---------|------------|-------|
| blogwatcher | RSSモニター | Low |

### ドキュメント（1）
| スキル名 | 対応サービス | リスク |
|---------|------------|-------|
| nano-pdf | PDF編集 | Low |

### モニタリング（1）
| スキル名 | 対応サービス | リスク |
|---------|------------|-------|
| model-usage | 使用量トラッキング | Low |

### 通話（1）
| スキル名 | 対応サービス | リスク |
|---------|------------|-------|
| voice-call | 音声通話 | High |

[出典: WenHao Yu記事 Appendix 2026-03-17取得]

## ClawHub
- OpenClawの公式スキルレジストリ（https://clawhub.com）
- **登録スキル数**: 13,700以上（2026年3月時点のコミュニティ製スキル）
- スキルの公開・入手が可能
- 約4000ファイルをスキャン → 283個（約7%）に脆弱性
- 2026年2月からVirusTotal自動スキャン統合済み（悪意あるダウンロードをブロック）
- **セキュリティ**: 公開登録にはGitHubアカウント1週間以上の履歴が必須、3件以上の報告で自動非表示
- **人気スキル1位**: Capability Evolver（35,000ダウンロード）— AIが動作中に自動的に自身の機能を進化させる
- [出典: 動画3, 公式ドキュメント, WenHao Yu記事, Apiyi.com]

## CLIコマンド
```bash
clawhub search       # スキル検索
clawhub install      # スキルインストール
clawhub update --all # 全スキル更新
clawhub publish      # スキル公開
clawhub sync         # 同期
clawhub inspect <slug> # スキルの権限・ソース確認（セキュリティチェック）
```
- [出典: 公式ドキュメント]

## SKILL.mdの構造（自作スキル開発）
```yaml
---
name: skill-name
description: "スキルの説明。コロン+スペースを含む場合は引用符で囲む"
metadata: {"openclaw":{"emoji":"📊","requires":{"bins":["bash"],"env":["API_KEY"],"os":["linux","darwin"]}}}
---

# スキル名

## What it does
スキルの目的と使用タイミング

## Inputs needed
必要なパス、URL、ID、認証情報

## Workflow
1) ステップ1
2) ステップ2

## Output format
出力形式の指定

## Guardrails
- データ捏造禁止
- サービス再起動禁止
```

### 自作スキルの配置先
- `~/.openclaw/skills/スキル名/SKILL.md` — 全エージェント共通
- `<workspace>/skills/スキル名/SKILL.md` — 特定エージェント専用

### オプションのfrontmatterキー
- `user-invocable`: true/false — スラッシュコマンドとして公開するか
- `disable-model-invocation`: true/false — モデルプロンプトから除外するか
- `command-dispatch: tool` — モデルをバイパスして直接ツール呼び出し
- `homepage` — Skills UIに表示するURL

[出典: 公式ドキュメント, LumaDock記事]

## トークンコスト
- 基本オーバーヘッド195文字 + スキルごと97文字 + 名前・説明・位置情報
- スキル数が多すぎるとLLMのコンテキストを圧迫するため注意
- `skills.allowBundled` でホワイトリスト管理が推奨
- [出典: 公式ドキュメント]

## スキル導入時のセキュリティ注意
- 300以上の悪意あるスキル発見報告あり（約20%がリスクあり）
- サードパーティスキルは「未信頼コード」として扱う
- インストール前に `clawhub inspect <slug>` で確認
- サンドボックス環境での実行を推奨
- SKILL.mdの内容はコードレビューと同等に確認する
- [出典: 動画1, 動画3, 公式ドキュメント, LumaDock記事]

## ハートビート（cron）との連携
- cron: 定時タスクの自動実行
- message: 結果をTelegram/Slack/Discordに通知
- パターン: **trigger + action + deliver**
- 活用例: デイリーブリーフ、メールトリアージ、CI/CDモニタリング、コンテンツリサーチ
- [出典: 動画1, 動画3, WenHao Yu記事]

## 不足情報
- [x] 主要スキルの個別レビュー → 53スキル全リスト取得済み
- [x] スキル開発方法の詳細 → SKILL.md構造・配置先・公開方法取得済み

---
*最終更新: 2026-03-17（53スキル完全リスト追加、SKILL.md自作方法追加、セキュリティ情報更新、ClawHub情報更新）*
