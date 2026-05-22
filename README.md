# Nocturne-Bot

A Discord bot embodying Nocturne — the persona behind the Eridian House publishing imprint. Powered by Claude via OpenRouter.

## Setup

### 1. Install dependencies

```bash
pip install -r requirements.txt
```

### 2. Configure credentials

Copy the example env file and fill in your keys:

```bash
cp .env.example .env
```

Edit `.env`:

- `DISCORD_TOKEN` — from the [Discord Developer Portal](https://discord.com/developers/applications), under your application's **Bot** tab.
- `OPENROUTER_API_KEY` — from [OpenRouter](https://openrouter.ai/keys).

**Never commit `.env`.** It is gitignored.

### 3. Enable bot intents

In the Discord Developer Portal, under your bot's **Bot** tab, enable:

- **Message Content Intent**

### 4. Invite the bot

Generate an invite URL under **OAuth2 → URL Generator** with scopes `bot` and `applications.commands`, and permissions to **Send Messages** and **Read Message History**. Open the URL to add the bot to your server.

## Run

```bash
python nocturne_bot.py
```

You should see:

```
Initializing Nocturne...
Loading Codex...
Nocturne has awakened. Logged in as <bot-name>
```

## Usage

- **In a server**: mention the bot (`@Nocturne <message>`) to speak with him.
- **In DMs**: any message will reach him.

Nocturne keeps the last 20 messages per channel as in-session memory. Memory resets when the process restarts.

## Files

- `nocturne_bot.py` — bot entry point, system prompt, and OpenRouter client.
- `requirements.txt` — Python dependencies.
- `.env.example` — template for required environment variables.
