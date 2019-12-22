# BoltでSlack botを作ってみる
## Boltって？
- Slackアプリを作成するためのフレームワーク https://github.com/SlackAPI/bolt
- TypeScriptで書かれたNode.jsモジュール
- Slack社謹製
- [日本語ドキュメントあり](https://slack.dev/bolt/ja-jp/tutorial/getting-started)

## 使ってみる
[入門ガイド](https://slack.dev/bolt/ja-jp/tutorial/getting-started)を参考にアプリを作成し、BotユーザのOAuthトークンを取得しておく

ワークスペースの初期化
```shell
mkdir hellobot
cd hellobot
npm init
```

トークンを環境変数にセット
```powershell
$env:SLACK_SIGNING_SECRET = hogehoge
$env:SLACK_BOT_TOKEN = foobar
```

Boltをインストール
```shell
npm install @slack/bolt
```

app.jsを作成し、下記のコードを追加
```javascript
const { App } = require('@slack/bolt');

// Initializes your app with your bot token and signing secret
const app = new App({
  token: process.env.SLACK_BOT_TOKEN,
  signingSecret: process.env.SLACK_SIGNING_SECRET
});

(async () => {
  // Start your app
  await app.start(process.env.PORT || 3000);

  console.log('⚡️ Bolt app is running!');
})();
```

SlackからのWebhookを受け取れるように、アプリを起動し、ngrokでTCPポート3000をインターネット上に公開
```shell
node app.js
ngrok http 3000
```

[ngrokが生成したURLをイベントに登録](https://slack.dev/bolt/ja-jp/tutorial/getting-started#%E3%82%A4%E3%83%99%E3%83%B3%E3%83%88%E3%81%AE%E8%A8%AD%E5%AE%9A)

app.jsにメッセージを投稿するコードを追記

```javascript
// こんにちは または こんにちわ に返信する
app.message(/こんにち[はわ]/, async ({message, say}) => {
  say(`こんにちは、<@${message.user}>さん`);
});
```

Botをチャンネルに追加し、挨拶してみる

![Botとの会話](https://raw.githubusercontent.com/umany/til/master/_images/slack-hello-bot.png)

挨拶を返すBotが出来た。
