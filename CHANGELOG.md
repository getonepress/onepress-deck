# Changelog

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
