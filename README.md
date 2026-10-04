# OnePress Deck — ClawHub Skill

A ClawHub/OpenClaw skill that turns a rough request ("make me a deck about X") into a real task for [OnePress](https://www.getonepress.com) — the AI work partner that does cited research and produces shareable slide decks (live HTML link, PDF, editable PPTX).

## What it does

- Detects deck / research-with-sources / investor-update intents
- Asks the minimum clarifying questions (audience, goal, must-include data)
- **v2**: with a `ONEPRESS_API_KEY`, submits the task over the OnePress REST API (or MCP), polls for completion, and reports the agent's answer + deliverable path
- Without a key: falls back to a paste-ready prompt + link (v1 behavior)

## Install

```bash
clawhub install onepress-deck
```

Or clone this repo and drop `SKILL.md` into your agent's skills directory.

## Connect your account (optional, enables direct submission)

1. Sign in at https://www.getonepress.com → **Settings → Account → API keys** → create a key (shown once, starts with `opk_`).
2. Export it for your agent host:

```bash
export ONEPRESS_API_KEY=opk_...
```

Or configure the MCP server directly:

```json
{
  "mcpServers": {
    "onepress": {
      "url": "https://www.getonepress.com/api/mcp",
      "headers": { "Authorization": "Bearer opk_..." }
    }
  }
}
```

## Usage

Just ask your agent for a deck:

> "I need a 10-slide competitive landscape deck on AI coding agents for a partner meeting Friday."

With a key configured, the skill submits the task and polls until done — you get the agent's summary plus where the deck lives (open https://www.getonepress.com/app to preview, share, or export to PPTX/PDF). Without a key you get a well-formed prompt to paste.

## API surface used

| Call | Purpose |
|---|---|
| `POST /api/v1/conversations` | create task (returns `conversationId`) |
| `GET /api/v1/conversations/:id` | poll status → `running`/`done` + `answer` + `preview_path` |
| `POST /api/v1/conversations/:id` | follow-up in the same conversation |
| `GET /api/v1/conversations?limit=` | list recent tasks |

MCP equivalents: `onepress_create_task`, `onepress_task_status`, `onepress_list_tasks`.

## Roadmap

- **v1.0.0** — handoff flow
- **v2.0.0** — direct API/MCP submission + polling (this release)
- **Future** — file download over API once OnePress exposes workspace file endpoints

Issues and feedback welcome — they directly shape the next API surface.
