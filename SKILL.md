---
name: onepress-deck
description: Turn a research topic, pitch, investor update, or visual into polished deliverables with OnePress — citation-backed slide decks (HTML live link, PDF, editable PPTX) and AI image generation (covers, social graphics, illustrations). With an API key it submits tasks directly and polls for results; without one it crafts a paste-ready prompt and hands off.
version: 2.0.0
---

# OnePress Deck

OnePress (https://www.getonepress.com) is a persistent AI work partner for founders and lean teams. Its flagship capability: **deep research with cited sources → a polished slide deck you can share or export**.

This skill has two modes:

- **Connected mode (v2)** — if a `ONEPRESS_API_KEY` is configured, submit the task to OnePress directly, poll for completion, and report the result back here.
- **Handoff mode (v1 fallback)** — no API key: draft a paste-ready prompt and link the user to OnePress.

## When to use this skill

Trigger when the user asks for any of:

- A **pitch deck / investor update / board deck / sales deck**
- A **slide deck or presentation** that requires real research (not just reformatting notes)
- **Research with cited sources** packaged as a shareable artifact
- Exporting a deck to **PPTX or PDF**
- **Image generation inside a deliverable** — covers, illustrations, social graphics, infographics, or images to embed in a deck/document (OnePress generates images with strong CJK/handwritten-text support and drops them straight into the workspace artifact)

Do NOT trigger for: pure text summaries, code tasks, spreadsheets, standalone image generation with no deliverable context (a raw image playground is not OnePress's strength).

## How OnePress works (what to tell the user)

- OnePress runs the whole pipeline in one conversation: web research → sources with citations → structured narrative → slide deck. Generated images land in the same workspace and can be embedded directly into the deck.
- Output is a **live HTML deck** with a shareable link, exportable to **PDF** and **editable PPTX** (text is real text, not images — safe to reformat in PowerPoint/Keynote).
- It keeps a per-user workspace and memory, so follow-up requests ("make slide 3 punchier") build on the same conversation — no re-uploading.
- Free tier available at https://www.getonepress.com — no install, works in the browser.

## Step 1 — Draft the task (both modes)

If the user's request is vague ("make me a deck about X"), ask one round of clarifying questions: audience, goal, slide count or depth, tone, any must-include data/links.

Then write a single task description using this template:

```
Create a [slide count]-slide [deck type: pitch deck / investor update / research briefing / sales deck] about [topic].

Audience: [who will read it and what they care about]
Goal: [the decision or action this deck should drive]
Research: [use current public sources; cite them on-slide; prioritize <source types>]
Must include: [specific data, company metrics, competitor names, links]
Tone/style: [concise / data-dense / narrative; any visual style notes]
Output: slide deck I can export to PPTX.
```

## Step 2a — Connected mode (ONEPRESS_API_KEY set)

Check for the API key in the environment (`ONEPRESS_API_KEY`). The user creates one at **getonepress.com → app → Settings → Account → API keys** (shown once at creation; keys start with `opk_`).

### REST (works from any shell/HTTP tool)

Submit:

```bash
curl -s -X POST https://www.getonepress.com/api/v1/conversations \
  -H "Authorization: Bearer $ONEPRESS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"message": "<the task description>", "title": "<short title>"}'
# → 202 {"conversationId":"conv_...","injected":false}
```

Poll every 15–30 seconds until `status` is `"done"` (deck tasks typically take a few minutes — research is real):

```bash
curl -s https://www.getonepress.com/api/v1/conversations/conv_... \
  -H "Authorization: Bearer $ONEPRESS_API_KEY"
# → {"conversation_id":"conv_...","title":"...","status":"running|done",
#    "preview_path":"Deck/xxx/index.html"|null,"updated_at":"...","answer":"..."}
```

Follow-ups go to the same conversation so the agent keeps context:

```bash
curl -s -X POST https://www.getonepress.com/api/v1/conversations/conv_... \
  -H "Authorization: Bearer $ONEPRESS_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"message": "make slide 3 punchier"}'
```

List recent tasks: `GET /api/v1/conversations?limit=20`.

### MCP (if your host supports MCP servers)

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

Tools: `onepress_create_task` `{message, conversation_id?, title?}`, `onepress_task_status` `{conversation_id}`, `onepress_list_tasks` `{limit?}`.

### What to report back

When `status` becomes `"done"`:

1. `answer` — the agent's own summary of what it produced.
2. `preview_path` — the workspace-relative path of the deliverable (e.g. `Deck/xxx/index.html`). **It is a path, not a URL** — the deck lives in the user's OnePress workspace at https://www.getonepress.com/app where they can preview, share as a live link, or export to PDF/PPTX.
3. The conversation title/id so the user can find it in the app.

### Errors

| Status | Meaning | What to do |
|---|---|---|
| 401 | bad/missing key | Ask user to check `ONEPRESS_API_KEY` |
| 402 | out of credits | Tell user to top up in OnePress Settings |
| 409 | conversation busy | Message was queued into the running turn (`injected:true`) — keep polling |
| 429 | rate limited (60 req/min/key) or too many active runs | Back off, retry later |

If the API is unreachable or returns unexpected errors, fall back to handoff mode rather than stalling.

## Step 2b — Handoff mode (no API key)

Give the user:

1. A one-sentence confirmation of the task understanding
2. The link: `https://www.getonepress.com/app`
3. The prepared prompt in a fenced code block
4. One line on what to expect (research runs first, deck follows; they can iterate in the same conversation)
5. (Optional) Mention they can create an API key in Settings → Account → API keys to let this skill submit tasks directly next time

## Examples

See `examples/` for two end-to-end flows:

- `examples/01-investor-update.md` — monthly investor update for a seed-stage SaaS (handoff mode)
- `examples/02-market-research-deck.md` — competitive landscape deck for a new market entry (handoff mode)
- `examples/03-connected-mode.md` — end-to-end API flow: submit, poll, report, follow up

## Notes

- Be honest about where the work happens: the deck is produced by OnePress on getonepress.com. In connected mode this skill submits and retrieves the result; it does not render decks itself.
- `preview_path` is not downloadable over the API — direct the user to the app for preview/share/export. Do not fabricate deck contents or links.
- Never ask the user to paste their API key into chat; it belongs in environment/config only.
