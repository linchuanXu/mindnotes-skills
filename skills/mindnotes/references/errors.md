# Error Reference

Use this when a MindNotes API call fails, setup is missing, permissions are unclear, or pagination/upgrade behavior matters.

## Error Priority

1. If `upgrade_info` exists, stop API work and tell the user to update or reinstall the MindNotes Skill.
2. If the response status is `401`, ask the user to refresh the MindNotes API Key and set only `MINDNOTES_API_KEY`.
3. If the response status is `403`, explain that the current API Key lacks the required permission in user-facing terms.
4. If the response status is `404`, say the requested note/card/canvas/item was not found or is no longer available.
5. If the response status is `429`, say MindNotes is rate-limiting requests and retry later only if the user asks.
6. If results are empty, state exactly what was searched and avoid guessing.
7. If pagination returns no `next_cursor`, stop paginating.

## User-Facing Wording

| Case | Wording |
|---|---|
| Missing Key | "需要先设置 `MINDNOTES_API_KEY`，然后重启 AI 客户端。" |
| Invalid Key | "这个 API Key 无效或已过期，请在 MindNotes 重新生成。" |
| Missing Permission | "这个 API Key 缺少所需权限。" |
| Upgrade Required | "MindNotes Skill 需要更新后才能继续。" |
| Empty Search | "我没有在你的笔记里找到相关内容；我搜索的是..." |
| Not Found | "我没有找到这个项目，它可能已被删除或你当前的 Key 没有权限访问。" |

Do not expose stack traces, backend paths, token hashes, storage details, or raw exception objects.

## Retry Rules

- Retry only transient network failures or explicit `429`/`5xx` responses.
- Do not retry authentication, permission, validation, deletion-confirmation, or upgrade failures.
- Do not keep calling APIs after `upgrade_info`; updating the Skill is required first.

## Pagination Rules

- Use only `next_cursor` returned by the API.
- Do not invent `offset`, page numbers, or guessed cursors.
- Tell the user when a summary is limited by `count` or pagination.
