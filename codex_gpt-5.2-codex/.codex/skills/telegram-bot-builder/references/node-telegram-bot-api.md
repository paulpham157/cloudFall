# node-telegram-bot-api Quickstart (Node.js)

## Install

```bash
npm install node-telegram-bot-api
```

## Basic bot (polling)

```js
import TelegramBot from 'node-telegram-bot-api';

const token = process.env.TELEGRAM_BOT_TOKEN;
if (!token) throw new Error('Missing TELEGRAM_BOT_TOKEN');

const bot = new TelegramBot(token, { polling: true });

bot.onText(/\/start/, (msg) => {
  bot.sendMessage(msg.chat.id, 'Welcome!');
});

bot.on('message', (msg) => {
  if (msg.text && msg.text !== '/start') {
    bot.sendMessage(msg.chat.id, `You said: ${msg.text}`);
  }
});
```

## Inline keyboard

```js
bot.sendMessage(chatId, 'Choose one:', {
  reply_markup: {
    inline_keyboard: [
      [{ text: 'Option A', callback_data: 'opt:a' }],
      [{ text: 'Option B', callback_data: 'opt:b' }],
    ],
  },
});

bot.on('callback_query', (query) => {
  const chatId = query.message.chat.id;
  bot.sendMessage(chatId, `Picked: ${query.data}`);
});
```

## Webhook mode

```js
const bot = new TelegramBot(token);
await bot.setWebHook('https://example.com/telegram/webhook');
```

## Notes
- Use `bot.setWebHook` to switch from polling to webhooks.
- Handle retries and duplicate updates when using webhooks.
