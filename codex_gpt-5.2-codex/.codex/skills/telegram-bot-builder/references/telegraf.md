# Telegraf Quickstart (Node.js)

## Install

```bash
npm install telegraf
```

## Basic bot (ESM)

```js
import { Telegraf, Markup } from 'telegraf';

const token = process.env.TELEGRAM_BOT_TOKEN;
if (!token) throw new Error('Missing TELEGRAM_BOT_TOKEN');

const bot = new Telegraf(token);

bot.start((ctx) => ctx.reply('Welcome!'));
bot.command('ping', (ctx) => ctx.reply('pong'));
bot.on('text', (ctx) => ctx.reply(`You said: ${ctx.message.text}`));

bot.catch((err, ctx) => {
  console.error('Bot error', err);
  ctx.reply('Something went wrong.');
});

bot.launch();
```

## Inline keyboard and callback queries

```js
bot.command('menu', (ctx) => {
  return ctx.reply('Choose one:',
    Markup.inlineKeyboard([
      Markup.button.callback('Option A', 'opt:a'),
      Markup.button.callback('Option B', 'opt:b'),
    ])
  );
});

bot.on('callback_query', async (ctx) => {
  const data = ctx.callbackQuery.data;
  await ctx.answerCbQuery();
  if (data === 'opt:a') return ctx.reply('A selected');
  if (data === 'opt:b') return ctx.reply('B selected');
});
```

## Session state (in-memory)

```js
import { session } from 'telegraf';

bot.use(session());

bot.command('count', (ctx) => {
  ctx.session.count = (ctx.session.count || 0) + 1;
  ctx.reply(`Count: ${ctx.session.count}`);
});
```

## Files and media

```js
bot.command('photo', (ctx) => ctx.replyWithPhoto('https://picsum.photos/400'));
```

## Notes
- For production, consider persistent session storage.
- Keep callback data short (up to 64 bytes).
- Use `bot.telegram` for raw API calls when needed.
