# OnePress Deck — ClawHub Skill

A ClawHub/OpenClaw skill that turns a rough request ("make me a deck about X") into a well-formed task for [OnePress](https://www.getonepress.com) — the AI work partner that does cited research and produces shareable slide decks (live HTML link, PDF, editable PPTX).

## What it does

- Detects deck / research-with-sources / investor-update intents
- Asks the minimum clarifying questions (audience, goal, must-include data)
- Produces a paste-ready OnePress prompt + the link to run it

## Install

```bash
clawhub install onepress-deck
```

Or clone this repo and drop `SKILL.md` into your agent's skills directory.

## Usage

Just ask your agent for a deck:

> "I need a 10-slide competitive landscape deck on AI coding agents for a partner meeting Friday."

The skill will clarify what's missing, draft the task, and hand you a prompt + link.

## Roadmap

- **v1.0.0** — handoff flow (this release)
- **v2 (planned)** — direct API calls (`ONEPRESS_API_KEY`) returning deck + citations + download links in-chat

Issues and feedback welcome — they directly shape the v2 API surface.
