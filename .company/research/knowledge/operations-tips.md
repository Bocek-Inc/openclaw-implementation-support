# OpenClaw 運用知見・トラブルシュート

実際の運用から得られた知見をまとめる。

---

## 🔧 設定・Config

### openclaw.json の hot reload

`openclaw.json` を書き換えると、OpenClaw は設定変更を自動検知して Slack 接続を**再起動**（hot reload）する。
これにより一時的に（数秒〜数十分）メッセージを受け取れなくなることがある。

**落とし穴:**
- AIが `edit` / `write` ツールで `openclaw.json` を変更すると、ユーザーの意図しないタイミングで再起動が走る
- 再起動中に届いたメンションはキューに溜まるが、タイミングによって取りこぼす可能性がある

**対策:**
- AIに config の直接変更を許可する場合は「確認→承認→実施」のフローを徹底する
- 変更後は必ずユーザーに再起動した旨を通知する
- HEARTBEAT.md や AGENTS.md に「configの変更は要確認」と明記しておく

---

## 📋 Cron ジョブ

### cronジョブの二重投稿問題

AI に「`message action=send` で Slack に投稿して」と指示しつつ、cron の `delivery.mode: "announce"` を設定すると**二重投稿**になる。

**仕組み:**
1. AI が `message` ツールで Slack に投稿（1回目）
2. cron 完了時に `delivery.mode: "announce"` が AI の返答 summary を同チャンネルに自動投稿（2回目）

**解決策:**
- AIが自分で投稿するcronジョブは `delivery.mode: "none"` を設定する
- AI に投稿させず、cron の announce だけ使う場合は `delivery.mode: "announce"` のまま、AI 側の `message send` 指示を除く

```json
// openclaw.json の cron 設定例（AI が自分で投稿する場合）
{
  "id": "daily-report",
  "delivery": {
    "mode": "none"
  }
}
```

---

## 💬 チャンネル対応

### 新チャンネルへの対応追加フロー

OpenClaw が新しい Slack チャンネルでメンションに反応するには、`openclaw.json` の allowlist への追加が必要。

**手順:**
1. チャンネルの ID を確認（例: `C08UB1Y9F7T`）
2. `openclaw.json` の `slack.channels` に追加（`allow: true`, `requireMention: true` など設定）
3. hot reload が走り自動で再起動される
4. 再起動完了後にメンションしてもらって動作確認

**注意:**
- hot reload 中に届いたメンションは取りこぼす可能性がある
- 追加後は「今は受け取れるはず、もう一度メンションしてください」と案内すると丁寧

---

## 🤖 AIエージェント・PR作成

### チーム別 PR レビュー依頼ルール

同じリポジトリでも、依頼者によってレビュー依頼先を変えるケースがある。

**例（agepoyo）:**
- 通常: リード=`OhVIton`、メイン=`dev3-be` チーム
- Sho さん（U08ACRPB338）から指示された場合 → `Bocek-Inc/dev1` チームにレビュー依頼

**Tips:**
- このようなルールは TOOLS.md や AGENTS.md に明記しておくことで、セッションをまたいで一貫した対応が可能になる
- `gh pr edit --add-reviewer` でレビュアーの変更も可能

---

## 🐛 バグ調査

### organizationId フィルター漏れ（マルチテナント系）

マルチテナント SaaS のバグとして「他組織のデータが混入する」系は、DB クエリの `organizationId` フィルター漏れが典型原因。

**調査の起点:**
- どのサービス・クエリで集計しているか特定（統計系は専用のサービスに集約されていることが多い）
- JOIN するテーブルのどこかで org フィルターが抜けていないか確認
- 他の類似クエリと比較して、フィルター条件の差分を探す

**実例 (2026-03-26):**
`omoide/AppMonthlyUsageQueries.kt` の `fetchTaskUsageCountsGrouped` で `organizationId` フィルターなしに全組織の利用回数を合算していた。
他クエリは `UsersTable` を INNER JOIN して org フィルターをかけていたが、このクエリだけ抜けていた。

---

## 🔄 Gateway / サービス管理

### Gateway の SIGTERM と再起動

OpenClaw の Gateway プロセスが SIGTERM を受けると、数秒でシャットダウン → 自動再起動する。

**確認方法:**
```bash
# ログ確認
openclaw gateway logs

# ステータス確認
openclaw gateway status
```

**ユーザーへの説明例:**
「X時X分に SIGTERM を受けて一瞬ダウン → X秒後に再起動、という流れでした。再起動自体は完了していますが、再起動中のメッセージは取りこぼした可能性があります。」
