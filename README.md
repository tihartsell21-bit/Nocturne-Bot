# Discord Bots Monorepo

Eridian House Discord bots. Each lives in its own folder and is deployed as a separate Railway service.

## Bots

| Folder | Bot | Model |
|---|---|---|
| `nocturne/` | Nocturne — Empress's consort | `anthropic/claude-opus-4-20250514` |
| `caleb/` | Caleb — charm wrapped around danger | `anthropic/claude-sonnet-4-20250514` |
| `gabe/` | Gabe — the soldier who came home | `anthropic/claude-sonnet-4-20250514` |
| `silas/` | Silas — the pastor falling | `anthropic/claude-sonnet-4-20250514` |
| `kael/` | Kael — the shadow that watches | `anthropic/claude-sonnet-4-20250514` |

## Repository layout

```
.
├── <bot-name>/
│   ├── bot.py              # entry point
│   ├── requirements.txt    # python deps
│   ├── Procfile            # Railway start command (worker: python bot.py)
│   └── .env.example        # required env vars (copy to .env locally)
├── .gitignore              # excludes .env, venvs, caches, *_stats/
└── README.md
```

Each bot folder is **self-contained**: own deps, own start command, own env vars.

## Per-bot commands

When mentioned in a server (or DM'd), each bot responds to:

- `!<name>_clear` — clear the channel's conversation history (works for any user)
- `!<name>_stats` — show usage stats (server admins only)

Where `<name>` is the bot's lowercase name: `!caleb_stats`, `!nocturne_clear`, etc.

## Running a bot locally

```bash
cd <bot-name>
cp .env.example .env       # then fill in real keys
pip install -r requirements.txt
python bot.py
```

## Deploying to Railway

Create **one Railway service per bot** in the same Railway project:

1. In your Railway project, click **+ New** → **GitHub Repo** → pick this repo.
2. Open the new service's **Settings** → **Service** → set **Root Directory** to the bot's folder (e.g. `caleb`).
3. Railway detects the `Procfile` automatically and runs `python bot.py`.
4. Under **Variables**, add:
   - `DISCORD_TOKEN` — that bot's Discord token (each bot has its own).
   - `OPENROUTER_API_KEY` — can be shared across services if you want billing in one place, or per-bot if you want to isolate spend.
5. **Deploy.**

Repeat for each of the 5 bot folders. Services run, scale, and log independently.

### ⚠️ Stats persistence on Railway

Each bot writes per-user stats to `./<name>_stats/*.json`. Railway's default filesystem is **ephemeral** — these files are wiped on every redeploy or restart.

Options:

- **Accept it** — stats reset on each restart. Fine if they're nice-to-have.
- **Attach a Railway Volume** to each service, mounted at the bot's `_stats` directory. Persists across restarts.
- **Move to a database** (Postgres, Railway's built-in, etc.) — most durable.

## Required Discord intents

In the Discord Developer Portal, under each bot's **Bot** tab, enable:

- **Message Content Intent**

Without it the bots cannot see message text and won't reply to mentions.

## Security

- **Never commit `.env`** — it's gitignored.
- Every bot reads credentials from environment variables only.
- If a key is ever committed, **rotate it immediately**: Discord Developer Portal for tokens, OpenRouter dashboard for API keys. Git history exposes leaked keys permanently until rotated.
