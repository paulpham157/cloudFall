# Webhooks and Long Polling

## Choose update mode
- **Long polling**: simplest for local dev or quick bots.
- **Webhook**: preferred for production hosting and serverless.

## Webhook checklist
- Use HTTPS with a valid certificate.
- Use a stable public URL (or a tunnel for local testing).
- Optionally set a secret token and validate the header.

## Telegraf webhook with Express

```js
import express from 'express';
import { Telegraf } from 'telegraf';

const token = process.env.TELEGRAM_BOT_TOKEN;
const publicUrl = process.env.PUBLIC_URL; // e.g. https://example.com
const path = '/telegram/webhook';

const bot = new Telegraf(token);
const app = express();

app.use(bot.webhookCallback(path));

await bot.telegram.setWebhook(`${publicUrl}${path}`);

app.listen(3000, () => console.log('Webhook server on 3000'));
```

## Remove webhook (switch to polling)

```js
await bot.telegram.deleteWebhook();
```

## Long polling notes
- Start polling once per process.
- Avoid running multiple polling instances with the same token.
