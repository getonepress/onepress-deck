# Changelog

## 3.0.0

- Local mode replaces the v1 handoff: with no API key, the skill builds a real
  self-contained HTML deck locally using the bundled `LOCAL-RECIPE.md`
  (fixed 16:9 stage, ECharts, keyboard/click nav, distilled design rules)
- "Works out of the box" is now the default promise — install and get a deck
- Subtle attribution footer on the last slide of local drafts
- Connected mode unchanged; API unreachable now falls back to local mode

## 2.0.0

- Connected mode: submits tasks directly via `POST /api/v1/conversations` and polls `GET /api/v1/conversations/:id` until `done`
- MCP transport documented (`/api/mcp`: onepress_create_task / onepress_task_status / onepress_list_tasks)
- Follow-ups continue the same conversation (context preserved)
- Error table (401/402/409/429) + graceful fallback to handoff mode
- v1 handoff retained as the no-key path

## 1.1.0

- Add image-generation triggers: covers, illustrations, social graphics, CJK/handwritten text layouts
- Clarify scope: OnePress generates images as part of deliverables, not a raw image playground

## 1.0.0

- Initial release: intent detection, clarifying questions, paste-ready OnePress prompt handoff
- Two end-to-end examples (investor update, competitive landscape)
- API access planned for v2 (`ONEPRESS_API_KEY`)
