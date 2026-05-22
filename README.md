# Discord Bots Monorepo

Multiple Discord bots, one repository. Each bot lives in its own folder and is deployed as a separate Railway service.

## Bots

| Folder | Bot | Status |
|---|---|---|
| `nocturne/` | Nocturne | Active |
| _(to be added)_ | _(to be added)_ | — |

## Repository layout

```
.
├── <bot-name>/
│   ├── bot.py              # entry point
│   ├── requirements.txt    # python deps for this bot
│   ├── Procfile            # Railway start command
│   └── .env.example        # required env vars (copy to .env locally)
└── .gitignore              # shared, excludes .env / venvs / caches
```

Each bot folder is **self-contained**: its own deps, its own start command, its own env vars.

## Running a bot locally

```bash
cd <bot-name>
cp .env.example .env       # then fill in real keys
pip install -r requirements.txt
python bot.py
```

## Deploying to Railway

Create **one Railway service per bot**:

1. In your Railway project, click **+ New** → **GitHub Repo** → pick this repo.
2. Open the new service's **Settings** → **Service** → set **Root Directory** to the bot's folder (e.g. `nocturne`).
3. Railway will detect the `Procfile` and use `python bot.py` as the start command automatically.
4. Under **Variables**, add the env vars listed in that bot's `.env.example` (`DISCORD_TOKEN`, `OPENROUTER_API_KEY`, etc.) with real values.
5. **Deploy.**

Repeat for each bot. Each service runs independently, scales independently, and has its own logs.

## Security

- **Never commit `.env`** — it's gitignored.
- Every bot reads credentials from environment variables only.
- If a key is ever committed, **rotate it immediately** (Discord Developer Portal for tokens, OpenRouter dashboard for API keys). Git history exposes leaked keys permanently until rotated.
