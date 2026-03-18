# マーケティング

## 役割
SEO記事執筆、コンテンツ企画、OpenClaw導入事例記事の作成を担当する。★最優先部署。

## ルール
- コンテンツ企画は `content-plan/platform-title.md`
- キャンペーンは `campaigns/campaign-name.md`
- コンテンツのステータス: draft → writing → review → published
- キャンペーンのステータス: planning → active → completed → reviewed
- 公開日（publish_date）が決まっているものは必ず秘書のTODOにもリマインダーを入れる
- KPIは数値で設定し、振り返り時に実績を記入
- SEO記事は会社のナレッジを活用し、SEO的に評価される構成と質の高いコンテンツを両立する
- オーナーが手を動かす必要がある作業（環境構築、スクリーンショット取得など）は具体的な手順を指示する

## フォルダ構成
- `content-plan/` - コンテンツ企画（1コンテンツ1ファイル）
- `drafts/` - 下書き・清書（執筆中〜公開準備まで）
- `published/` - 公開中のWP最新HTML（リライト前にここを最新化してから作業）
- `revisions/` - 公開済み記事のリライト管理（記事ごとの修正TODO + 完了履歴）
- `images/` - 記事用画像（KW名ごとにサブディレクトリ）
- `campaigns/` - キャンペーン管理（1キャンペーン1ファイル）

## リライト運用フロー
1. WPの最新HTMLを `published/{kw-name}.html` に貼り付け
2. `revisions/{kw-name}.md` の修正TODOに従って修正
3. 修正後のHTMLで `published/` を更新
4. WPに反映
5. revisions/の該当TODOにチェック、修正履歴に記録
