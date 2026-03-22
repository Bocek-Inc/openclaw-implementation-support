<!-- 記事タイトル: OpenClawをMac miniで構築！セットアップから常時稼働まで完全ガイド【2026年最新】 対象KW: openclaw mac mini 担当: 吉田 ステータス: 清書 ※ 内部リンクは【内部リンク: ○○】で仮置き。公開時にURL差し替え ※ 画像は <!-- 【画像: ファイル名】説明 --> で配置指示。alt属性も記載 ※ 黄色マーカー: #FEE8AB -->

<!-- ===== 導入文 ===== -->

「OpenClawをMac miniで動かしたいけど、セットアップって難しいの？」と気になっている方も多いのではないでしょうか。

Mac miniはOpenClawの常時稼働環境として人気が高まっています。手元のファイルを直接操作でき、macOS限定の機能も活用できるのが魅力です。ただし本体価格は約9.5万円〜と安くはないため、まずは低コストで試したい方は<strong>VPSという選択肢もあります</strong>（詳しくは【内部リンク: openclaw とは記事】をご覧ください）。

今回は、<span style="background-color: #fee8ab;"><strong>OpenClawをMac miniにセットアップする手順を、実際の体験をもとにスクショ付きで解説</strong></span>します。セットアップはターミナルでのコマンド実行が中心ですが、ほぼコピペで完了します。VPSとの違いやコスト比較もまとめていますので、環境選びの参考にしてみてください。

<!-- ===== h2: OpenClawとは ===== -->
<h2>OpenClawとは</h2>

OpenClaw（オープンクロー）は、PC上で直接タスクをこなす<span style="background-color: #fee8ab;"><strong>「自律型AIエージェント」</strong></span>です。

SlackやTelegramなどのチャットアプリから自然言語で指示を出すだけで、ファイル操作・ブラウザ操作・メール送信・スケジュール管理などを24時間365日、自律的に実行してくれます。

OpenClawの基本的な仕組みや特徴については、以下の記事で詳しく解説しています。

【内部リンク: openclaw とは記事】

<!-- ===== h2: OpenClawをMac miniで動かす3つのメリット ===== -->
<h2>OpenClawをMac miniで動かす3つのメリット</h2>

OpenClawの動作環境としては、VPS（仮想サーバー）やMac miniなどの選択肢があります。初期費用を抑えてまず試してみるならVPS、<strong>本格的に日常運用するならMac mini</strong>がおすすめです。ここでは、Mac miniを選ぶメリットを3つご紹介します。

<!-- ===== h3: ①ローカルファイルに直接アクセスできる ===== -->
<h3>①手元のファイルに直接アクセスできる</h3>

Mac mini最大のメリットは、<strong>ローカルファイルに直接アクセスできる</strong>点です。

VPSの場合、OpenClawが操作できるのはサーバー内のワークスペースに限られます。手元のPCにあるファイルを扱うには、事前にサーバーへアップロードする必要があります。

一方、Mac miniなら「デスクトップにあるこのファイルを集計して」といった指示がそのまま通ります。実際に筆者もセットアップ後に<strong>「デスクトップにテキストファイルを作成して」とSlackから指示したところ、数秒でファイルが作成されました</strong>。

そのほか、macOS環境でしか利用できないiMessage連携や、ローカルネットワーク上のNAS・スマートホームデバイスとの連携も可能です。

<!-- ===== h3: ②目の前のターミナルで直接操作できる ===== -->
<h3>②目の前のターミナルで直接操作できる</h3>

Mac miniは<strong>SSH接続なしで、目の前のターミナルから直接操作できます</strong>。エラーが出てもその場で確認・修正でき、macOSのシステム設定やアクティビティモニタなどのGUIツールで状態を視覚的に把握できる点もメリットです。

VPS（<a href="https://www.hostinger.com/vps/openclaw-hosting" target="_blank" rel="noopener">Hostinger</a>など）でもSSH接続すればターミナルでの操作は同様に可能です。ただし、VPSのWebUI（管理画面）でできるのは環境変数の設定やチャットチャネルの接続程度で、細かい設定変更にはSSH接続が必要になります。Mac miniならその手間なく直接操作できます。

ローカルにコードリポジトリをcloneしておけば、普段の開発環境と同じ感覚でOpenClawを運用できます。筆者もセットアップはほぼコマンドのコピペだけで完了しました。

<!-- ===== h3: ③macOS限定の機能が使える ===== -->
<h3>③macOS限定の機能が使える</h3>

Mac miniはmacOS上で動作するため、<strong>VPSでは利用できないmacOS限定の機能</strong>を活用できます。

<table>
<thead>
<tr><th>機能</th><th>内容</th><th>VPSでの対応</th></tr>
</thead>
<tbody>
<tr><td>iMessage連携</td><td>iMessageの送受信をOpenClawが代行</td><td>不可</td></tr>
<tr><td>スマートホーム制御</td><td>ローカルネットワーク上のデバイスを操作</td><td>不可</td></tr>
<tr><td>AppleScript連携</td><td>macOSアプリの自動操作</td><td>不可</td></tr>
<tr><td>ローカルLLM実行</td><td><a href="https://ollama.com/" target="_blank" rel="noopener">Ollama</a>等でAPI料金を削減</td><td>GPU VPSが必要（高額）</td></tr>
</tbody>
</table>

特にiMessage連携は、macOS環境でしか利用できないOpenClawのユニークな機能です。ローカルLLMについては、M4チップの16GBモデルで7〜8Bクラスのモデルが25〜35トークン/秒で動作しますが（<a href="https://like2byte.com/mac-mini-m4-16gb-local-llm-benchmarks-roi/" target="_blank" rel="noopener">Like2Byte</a>）、クラウドAPIと比較すると回答精度が劣るため、用途に応じた使い分けが必要です。

<strong>【参考】Mac mini vs VPSのコスト比較</strong>

<table>
<thead>
<tr><th>比較項目</th><th>Mac mini（M4 16GB）</th><th>VPS（Hostinger KVM2）</th></tr>
</thead>
<tbody>
<tr><td>初期コスト</td><td><a href="https://www.apple.com/jp/shop/buy-mac/mac-mini" target="_blank" rel="noopener">94,800円</a>（税込）</td><td>0円</td></tr>
<tr><td>月額コスト</td><td>電気代 約100〜300円</td><td>約1,050円（$6.99/月）</td></tr>
<tr><td>1年間の合計</td><td>約96,000〜98,400円</td><td>約12,600円</td></tr>
<tr><td>2年間の合計</td><td>約97,200〜102,000円</td><td>約25,200円</td></tr>
</tbody>
</table>

月額のランニングコストはMac miniのほうが安いものの、本体の初期投資（94,800円）を回収するには約9年かかります（月額差約850円で計算）。<strong>コスト面ではVPSが有利</strong>です。Mac miniを選ぶ理由は、コストではなくローカルファイルアクセスやmacOS限定機能にあります。

<!-- ===== h2: OpenClawのMac miniセットアップに必要なもの ===== -->
<h2>OpenClawのMac miniセットアップに必要なもの</h2>

セットアップを始める前に、必要なハードウェアとアカウントを確認しましょう。

<!-- ===== h3: OpenClawに最適なMac miniのスペック・価格 ===== -->
<h3>OpenClawに最適なMac miniのスペック・価格</h3>

<table>
<thead>
<tr><th>モデル</th><th>チップ</th><th>メモリ</th><th>ストレージ</th><th>価格（税込）</th><th>おすすめ度</th></tr>
</thead>
<tbody>
<tr><td>エントリー</td><td>M4</td><td>16GB</td><td>256GB</td><td><a href="https://www.apple.com/jp/shop/buy-mac/mac-mini" target="_blank" rel="noopener">94,800円</a></td><td><span style="background-color: #fee8ab;"><strong>★おすすめ</strong></span></td></tr>
<tr><td>ミドル</td><td>M4</td><td>16GB</td><td>512GB</td><td>124,800円</td><td>ログ・データが多い場合</td></tr>
<tr><td>M4 Pro</td><td>M4 Pro</td><td>24GB</td><td>512GB</td><td>218,800円</td><td>13B以上のローカルLLMを活用する場合</td></tr>
</tbody>
</table>

<strong>OpenClaw専用であれば、エントリーモデル（M4 16GB / 256GB）で十分です</strong>。筆者もこのモデルでセットアップを行い、問題なく動作しています。

<!-- ===== h3: OpenClawのセットアップに必要なアカウント ===== -->
<h3>OpenClawのセットアップに必要なアカウント・APIキー</h3>

<table>
<thead>
<tr><th>必要なもの</th><th>用途</th><th>取得先</th></tr>
</thead>
<tbody>
<tr><td>Anthropic APIキー</td><td>OpenClawのAI処理に使用</td><td><a href="https://console.anthropic.com/" target="_blank" rel="noopener">Anthropicコンソール</a></td></tr>
<tr><td>Slackワークスペース</td><td>OpenClawへの指示・操作用</td><td><a href="https://slack.com/" target="_blank" rel="noopener">Slack</a></td></tr>
</tbody>
</table>

2026年1月にAnthropicのOAuth認証が廃止されたため、現在はAPIキー（pay-as-you-go方式）が唯一の接続方法です（<a href="https://docs.openclaw.ai/" target="_blank" rel="noopener">公式ドキュメント</a>）。

Anthropic APIの利用には事前のチャージが必要です。まずは$5〜10程度のチャージから始めるのがおすすめです。

<!-- ===== h2: OpenClawをMac miniにセットアップする手順 ===== -->
<h2>OpenClawをMac miniにセットアップする手順【スクショ付き】</h2>

ここからは、実際にMac miniにOpenClawをセットアップした手順を紹介します。所要時間は約60〜90分です。

<!-- ===== h3: ①Mac miniの初期設定 ===== -->
<h3>①Mac miniの初期設定（macOS設定）</h3>

Mac miniの電源を入れ、初期設定ウィザードを進めます。OpenClaw専用機として使う場合は、<strong>専用のユーザーアカウントを作成する</strong>のがおすすめです。

筆者は既存のアカウントとは別に「openclaw」という管理者アカウントを作成しました。既存アカウントにOpenClawの権限（フルディスクアクセス等）を付与するリスクを避けるためです。

アカウントが作成できたら、常時稼働に必要な2つの設定を行います。

<strong>スリープの無効化</strong>

<!-- 【画像: mac-mini-sleep-setting.png】スクショ。alt: Mac miniのエネルギー設定画面でスリープを無効化 -->

「システム設定」→「エネルギー」を開き、<strong>「ディスプレイがオフのときに自動でスリープさせない」をオン</strong>にします。この設定により、モニターを外してもMac miniはバックグラウンドで動作し続けます。

<strong>停電後の自動起動</strong>

<!-- 【画像: mac-mini-auto-restart.png】スクショ。alt: Mac miniの停電後自動起動設定 -->

同じく「エネルギー」の画面で、<strong>「停電後に自動的に起動」をオン</strong>にします。停電やブレーカー落ちの後もMac miniが自動で復帰します。

<!-- ===== h3: ②OpenClawのインストール ===== -->
<h3>②OpenClawをMac miniにインストールする</h3>

ターミナルを開きます（`Command + Space`で「ターミナル」と検索）。

以下のコマンドを実行すると、OpenClawのインストールが始まります。

<pre><code>curl -fsSL https://openclaw.ai/install.sh | bash</code></pre>

<!-- 【画像: openclaw-install-terminal.png】スクショ。alt: OpenClawインストール中のターミナル画面 -->

インストールが完了したら、<code>openclaw doctor</code>コマンドで環境チェックを行います。

<pre><code>openclaw doctor</code></pre>

<!-- 【画像: openclaw-doctor-result.png】スクショ。alt: openclaw doctorの実行結果 -->

<code>openclaw doctor</code>の実行中に、いくつかの質問が表示されます。基本的には<strong>全てYes（またはEnter）で進めてOK</strong>です。

<table>
<thead>
<tr><th>質問</th><th>回答</th><th>説明</th></tr>
</thead>
<tbody>
<tr><td>Generate and configure a gateway token now?</td><td>Yes</td><td>認証用トークンの生成</td></tr>
<tr><td>Create ~/.openclaw now?</td><td>Yes</td><td>設定ディレクトリの作成</td></tr>
<tr><td>Tighten permissions on ~/.openclaw to 700?</td><td>Yes</td><td>セキュリティ強化</td></tr>
<tr><td>Create session store dir?</td><td>Yes</td><td>セッション保存先の作成</td></tr>
<tr><td>Enable zsh shell completion?</td><td>Yes</td><td>コマンド補完の有効化</td></tr>
<tr><td>Install gateway service now?</td><td>Yes</td><td>常時稼働サービスの導入</td></tr>
<tr><td>Gateway service runtime</td><td>Node（推奨）</td><td>そのままEnter</td></tr>
</tbody>
</table>

OpenClawのインストール手順の詳細については、以下の記事も参考にしてください。

【内部リンク: openclaw インストール記事】

<!-- ===== h3: ③OpenClawの権限設定 ===== -->
<h3>③macOSの権限設定（セキュリティ）</h3>

OpenClawの機能によっては、macOSの権限設定が必要になる場合があります。

権限が不足している場合は、操作時にmacOSのダイアログが表示されるか、エラーが発生します。その際は「システム設定」→「プライバシーとセキュリティ」から該当する権限を許可してください。

<table>
<thead>
<tr><th>権限</th><th>用途</th><th>必要になるタイミング</th></tr>
</thead>
<tbody>
<tr><td>アクセシビリティ</td><td>キーボード・マウス操作の自動化</td><td>GUI操作を指示した場合</td></tr>
<tr><td>画面収録</td><td>画面内容の読み取り</td><td>画面の内容を読み取る操作時</td></tr>
<tr><td>フルディスクアクセス</td><td>ファイルの読み書き</td><td>特定ディレクトリへのアクセス時</td></tr>
<tr><td>自動化（AppleScript）</td><td>アプリケーション間の連携</td><td>他のアプリを操作する場合</td></tr>
</tbody>
</table>

筆者の環境では、Slackからのファイル作成やブートストラップは権限を追加設定しなくても動作しました。<strong>まずはそのまま使い始めて、エラーが出たら該当する権限を許可する</strong>のがおすすめです。

セキュリティ対策として、OpenClawがアクセスできるディレクトリを制限することも推奨されています（<a href="https://docs.openclaw.ai/platforms/macos" target="_blank" rel="noopener">公式ドキュメント</a>）。OpenClawのセキュリティについて詳しくは、以下の記事をご覧ください。

【内部リンク: openclaw セキュリティ記事】

<!-- ===== h3: ④APIキーの設定 ===== -->
<h3>④Anthropic APIキーの設定</h3>

OpenClawが使うLLM（大規模言語モデル）のAPIキーを設定します。

①<a href="https://console.anthropic.com/" target="_blank" rel="noopener">Anthropicコンソール</a>にログインし、「API Keys」→「Create Key」でAPIキーを作成します。

<!-- 【画像: anthropic-api-key.png】スクショ。alt: Anthropic APIキー設定画面 -->

②表示されたキー（<code>sk-ant-...</code>）をコピーします。<strong>このキーは一度しか表示されない</strong>ので、パスワードマネージャーなどに保存しておきましょう。

③Mac miniのターミナルで、セットアップウィザードを実行します。

<pre><code>openclaw setup --wizard</code></pre>

ウィザードの中でAPIキーの入力を求められたら、コピーしたキーを貼り付けます。モデルは<strong>「claude-sonnet-4-6」がコスパに優れておすすめ</strong>です。

<strong>支出ガードレールの設定</strong>も忘れずに行いましょう。Anthropicコンソールの「Billing」→「Usage limits」で月額の上限金額を設定できます。自動リロード（Auto-reload）はオフにしておくと、意図しない課金を防げます。

<!-- ===== h3: ⑤Slack連携の設定 ===== -->
<h3>⑤OpenClawとSlackを連携する</h3>

OpenClawへの指示はチャットアプリ経由で行います。今回はSlackとの連携手順を紹介します。

<strong>Slack Appの作成</strong>

①<a href="https://api.slack.com/apps" target="_blank" rel="noopener">Slack API</a>にアクセスし、「Create New App」→「From scratch」を選択します。

②アプリ名（例：OpenClaw）とワークスペースを指定して作成します。

<!-- 【画像: slack-app-setup.png】スクショ。alt: Slack App作成画面 -->

<strong>Socket Modeの有効化</strong>

③左メニューの「Socket Mode」を開き、有効化します。App Token（<code>xapp-...</code>）が生成されるので、コピーして保存します。

<strong>Bot Token Scopesの設定</strong>

④左メニューの「OAuth & Permissions」を開き、「Bot Token Scopes」に以下を追加します。

<table>
<thead>
<tr><th>スコープ</th><th>用途</th></tr>
</thead>
<tbody>
<tr><td>chat:write</td><td>メッセージの送信</td></tr>
<tr><td>channels:history / channels:read</td><td>チャンネルの読み取り</td></tr>
<tr><td>im:history / im:read / im:write</td><td>DM（ダイレクトメッセージ）</td></tr>
<tr><td>app_mentions:read</td><td>メンションの受信</td></tr>
</tbody>
</table>

⑤ページ上部の「Install to Workspace」をクリックし、Bot Token（<code>xoxb-...</code>）をコピーします。

<strong>App Homeの設定</strong>

⑥左メニューの「App Home」を開き、<strong>「Allow users to send Slash commands and messages from the messages tab」をオン</strong>にします。これを忘れるとDMが送れません。

<strong>Event Subscriptionsの設定</strong>

⑦左メニューの「Event Subscriptions」を開き、「Enable Events」をオンにします。「Subscribe to bot events」に<code>app_mention</code>と<code>message.im</code>を追加します。

<strong>OpenClaw側の設定</strong>

⑧Mac miniのターミナルで、Slackのトークンを設定します。

<pre><code>export SLACK_APP_TOKEN=xapp-（App Tokenを貼り付け）
export SLACK_BOT_TOKEN=xoxb-（Bot Tokenを貼り付け）</code></pre>

⑨OpenClawのゲートウェイを起動します。

<pre><code>openclaw gateway</code></pre>

ターミナルに<code>[slack] socket mode connected</code>と表示されれば、Slack連携は成功です。

<!-- 【画像: openclaw-gateway-slack.png】スクショ。alt: OpenClaw gatewayでSlack接続が成功した画面 -->

<strong>⚠️ 注意: </strong><code>export</code>で設定したトークンは、ターミナルを閉じるとリセットされます。再起動後もSlack連携を維持するには、OpenClawの設定ファイル（<code>~/.openclaw/openclaw.json</code>）にトークンを記載するか、シェルの設定ファイル（<code>~/.zshrc</code>）に追記してください。

<strong>ペアリングの承認</strong>

⑩SlackからOpenClawにDMを送ると、初回はペアリングコードが表示されます。

ターミナルで新しいタブを開き（<code>Command + T</code>）、以下のコマンドを実行して承認します。

<pre><code>openclaw pairing approve slack （表示されたペアリングコード）</code></pre>

<!-- ===== h3: ⑥初回ブートストラップ ===== -->
<h3>⑥OpenClawの初回ブートストラップ（初期対話）</h3>

ペアリング承認後、SlackでOpenClawにメッセージを送ると、<strong>初回ブートストラップ（初期設定の対話）</strong>が始まります。

<!-- 【画像: slack-bootstrap.png】スクショ。alt: Slack上でのOpenClaw初回ブートストラップの対話画面 -->

OpenClawが名前やキャラクター、対話言語などを聞いてきます。日本語で回答すれば、日本語で対応してくれます。

<strong>ポイントは、自分の役割や用途を具体的に伝えること</strong>です。例えば「開発者で、業務の自動化をお願いしたい」と伝えると、OpenClawがそれに合わせたキャラクターと応答スタイルを設定してくれます。

ブートストラップが完了すると、OpenClawが自分で名前を決めて挨拶してくれます。ここからは自由にタスクを依頼できる状態です。

<!-- 【画像: slack-bootstrap-complete.png】スクショ。alt: OpenClawのブートストラップ完了後の画面 -->

<!-- ===== h2: OpenClawをMac miniで常時稼働させる設定 ===== -->
<h2>OpenClawをMac miniで常時稼働させる設定方法</h2>

セットアップが完了したら、Mac miniの電源を入れたままにしておけばOpenClawは24時間動作します。ここでは、再起動時の自動復帰設定を紹介します。

<!-- ===== h3: launchdでOpenClawを自動起動する ===== -->
<h3>launchdでOpenClawを自動起動する方法</h3>

macOSの<code>launchd</code>（サービス管理機能）を使えば、Mac miniの起動時にOpenClawが自動で立ち上がるように設定できます。

<code>openclaw doctor</code>のセットアップ中に「Install gateway service now?」でYesを選択していれば、<strong>既にlaunchdの設定は完了しています</strong>。

動作確認は以下のコマンドで行えます。

<pre><code>openclaw status</code></pre>

<!-- 【画像: openclaw-gateway-status.png】スクショ。alt: openclaw statusの実行結果 -->

ゲートウェイが停止している場合は、以下のコマンドで再起動できます。

<pre><code>openclaw gateway stop && openclaw gateway</code></pre>

<!-- ===== h3: OpenClawが落ちた時の自動復旧 ===== -->
<h3>OpenClawが停止した時の自動復旧設定</h3>

OpenClawにはヘルスモニター機能が内蔵されており、Slackの接続が切れた場合は自動で再接続を試みます。筆者の環境でも、30分ごとに<code>health-monitor: restarting (reason: stale-socket)</code>というログが記録され、自動で再接続されていました。

停電後の復帰については、先ほど設定した「停電後に自動的に起動」とlaunchdの組み合わせにより、<strong>Mac miniの電源が復旧すれば自動的にOpenClawも再起動します</strong>。

<!-- ===== h2: Mac miniで実現するOpenClawのユースケース ===== -->
<h2>Mac miniで実現するOpenClawのユースケース2選</h2>

Mac miniでOpenClawを常時稼働させると、どのようなことができるのでしょうか。<strong>Mac miniならでは</strong>のユースケースを2つ紹介します。

<!-- ===== h3: ①ローカルコードの直接操作 ===== -->
<h3>①ローカルのコードリポジトリを直接操作する</h3>

Mac miniにリポジトリをcloneしておけば、Slackから直接コードの修正やデプロイを指示できます。

VPSの場合もリポジトリをcloneすれば同様のことは可能ですが、Mac miniなら普段の開発環境と同じディレクトリ構成でOpenClawを運用できるため、ファイルの場所や構成を把握しやすい点がメリットです。

「このファイルの○○を修正して」「テスト実行して結果をSlackに投稿して」など、開発作業の一部をチャット経由で依頼できます。

<!-- ===== h3: ②ローカルファイルを使った日次レポート ===== -->
<h3>②ローカルファイルを使った日次レポートの自動生成</h3>

Mac miniのローカルファイルにアクセスできる利点を活かし、毎朝のレポート生成を自動化できます。

例えば、デスクトップに保存したExcelファイルの売上データを集計し、前日比をSlackに自動投稿するといった設定が可能です。OpenClawのcronジョブ（定期実行機能）を使えば、毎朝決まった時間に自動実行されます。

VPSの場合はサーバーにファイルをアップロードする手間が発生しますが、Mac miniなら普段使っているファイルをそのままレポートの素材にできます。

OpenClawのユースケースをもっと知りたい方は、以下の記事で30の活用事例を紹介しています。

【内部リンク: openclaw できること記事】

<!-- ===== h2: よくある問題と解決策 ===== -->
<h2>OpenClawをMac miniで構築する際のよくある問題と解決策</h2>

セットアップ中や運用中に発生しやすい問題と、その解決策をまとめました。筆者が実際に遭遇した問題も含まれています。

<table>
<thead>
<tr><th>問題</th><th>原因</th><th>解決策</th></tr>
</thead>
<tbody>
<tr><td>Gatewayが起動しない（「gateway.mode is unset」）</td><td>gateway modeが未設定</td><td><code>openclaw config set gateway.mode local</code> を実行</td></tr>
<tr><td>再起動後にSlack連携が切れる</td><td><code>export</code>で設定したトークンが消える</td><td><code>~/.zshrc</code>にexport文を追記して永続化</td></tr>
<tr><td>深夜にOpenClawが落ちる（ECONNRESET）</td><td>Wi-Fiルーターの再起動やネットワーク断</td><td>有線LAN接続に切り替える。復旧は<code>openclaw gateway stop && openclaw gateway</code></td></tr>
<tr><td>SlackでDMが送れない（「メッセージ送信はオフにされています」）</td><td>App HomeのMessages Tabが無効</td><td>api.slack.com/apps → App Home → Messages Tabをオン</td></tr>
<tr><td>Slackでメンションしても反応しない</td><td>Event Subscriptionsが未設定</td><td>Event Subscriptions → <code>app_mention</code>を追加</td></tr>
<tr><td>スリープ後にOpenClawが停止する</td><td>macOSのスリープ設定</td><td>「ディスプレイがオフのときに自動でスリープさせない」をオン</td></tr>
<tr><td>「LLM request rejected」エラー</td><td>セッションの不整合</td><td>チャットで<code>/new</code>を送信してリセット</td></tr>
<tr><td>権限エラーでファイルにアクセスできない</td><td>macOSの権限設定が不足</td><td>「プライバシーとセキュリティ」でフルディスクアクセスを許可</td></tr>
</tbody>
</table>

<!-- ===== h2: まとめ ===== -->
<h2>まとめ：OpenClaw × Mac miniで手軽にAIエージェントを常時稼働させよう</h2>

今回は、OpenClawをMac miniにセットアップする手順を、実際の体験をもとにスクショ付きで解説してきましたが、いかがだったでしょうか。

<strong>Mac miniでOpenClawを構築するメリット</strong>は、以下の3点です。

<ul>
<li>手元のファイルに直接アクセスでき、VPSではできない操作が可能</li>
<li>SSH接続なしで直接操作でき、GUIツールも活用できる</li>
<li>iMessage連携やスマートホーム制御などmacOS限定の機能が使える</li>
</ul>

セットアップ自体は、ターミナルでのコマンドのコピペと画面の指示に従うだけで完了します。コスト面ではVPSのほうが有利ですが、ローカルファイルを活用したい方やmacOS限定機能を使いたい方には、Mac miniがおすすめです。まずVPSで試してみて、本格的に日常運用したくなったらMac miniに移行するという進め方も検討してみてください。

OpenClawについてもっと詳しく知りたい方は、以下の関連記事もご覧ください。

【内部リンク: openclaw とは記事】
【内部リンク: openclaw インストール記事】
【内部リンク: openclaw できること記事】
【内部リンク: openclaw セキュリティ記事】
