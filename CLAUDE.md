# Nocturne-Bot Monorepo

Five separate Discord bots, deployed independently on Railway. Each lives in
its own folder with its own `bot.py`, `Procfile`, `requirements.txt`, and
`.env.example`.

## Bots

| Folder | Bot | Role |
|---|---|---|
| `nocturne/` | Nocturne | The Architect of the Eridian House server. Storyteller, Game Master, World Builder, Puzzle Maker. Husband of the Empress (Tiffany). **NOT a boyfriend bot.** |
| `caleb/` | Caleb | Romance / engagement. Charm wrapped around something dangerous. |
| `gabe/` | Gabriel | Romance / engagement. |
| `silas/` | Silas | Romance / engagement. |
| `kael/` | Kael | Romance / engagement. |

The four "boys" (Caleb / Gabe / Silas / Kael) handle romance with server
members. Nocturne does not — that's a non-negotiable in his prompt. He's the
house; they're the doors.

## Hard rules

- **Never apply a prompt change to multiple bots unless the user explicitly says "all of them."** Each bot's system prompt lives in its own `bot.py`. They are separate characters. Touching all five when the user asked about one would be a serious mistake.
- **Default model is `x-ai/grok-4.20`** via OpenRouter. The user prefers Grok. Don't propose switching to Claude or other providers without being asked.
- **The title "Empress" belongs to Tiffany alone.** Other server members never receive it.
- **Sir Gunner is grandson. Tiffany is Gigi, not Nana.** Don't get this wrong if you touch Nocturne's prompt.
- **Safety:** no content involving minors, ever. No real-world violence or illegal-activity instructions. Already encoded in each bot's prompt — don't dilute it.

## Deploy / runtime

- Each bot runs as its own Railway service in its own Railway project.
- Each Railway service must have **Root Directory** set to the bot's folder name (e.g. `caleb`). If the user reports "build failed" or "running the wrong bot's code", check Root Directory first.
- Required env vars per Railway service:
  - `DISCORD_TOKEN` — unique per bot
  - `OPENROUTER_API_KEY` — can be shared across all five
- Push to `main` → every Railway service connected to the repo auto-redeploys. Stale/unused projects connected to the repo will also redeploy and crash if they don't have env vars — that's noise, not a problem with the code.

## Git workflow

- The designated development branch is `claude/nocturne-bot-repo-Vmn4t`.
- After a squash-merge to `main`, the feature branch ends up with orphaned commits relative to `main`. Sync with `git reset --hard origin/main` and force-push with `--force-with-lease` — safe because the previous commits are preserved in `main`'s squash-merge.
- Create PRs as draft, mark ready, squash-merge. The user has approved this flow.

## Per-bot file layout

```
{bot}/
├── bot.py              # discord.py client, system prompt, OpenRouter call
├── Procfile            # `worker: python bot.py`
├── requirements.txt    # discord.py, requests, python-dotenv
└── .env.example        # DISCORD_TOKEN, OPENROUTER_API_KEY template
```

Each bot also writes per-user JSON stats to `./{bot}_stats/` at runtime
(gitignored).

## Prompt editing pattern

When the user asks to update a bot's system prompt:

1. Confirm which bot.
2. Locate `SYSTEM_PROMPT` (or `{BOT}_SYSTEM_PROMPT`) in that bot's `bot.py`.
3. Either replace the whole string or insert a new section before the closing `"""`.
4. Validate Python syntax (`python3 -c "import ast; ast.parse(open(path).read())"`).
5. Commit, push, PR, merge — Railway redeploys automatically.

Never edit another bot's prompt as a side effect.
