# MindNotes Skill API

Use this reference before making any MindNotes API call.

## Endpoint

Default base URL:

```text
https://app.mindnotes.cn
```

Use `MINDNOTES_BASE_URL` only for self-hosted, staging, or test deployments. Do not suggest it during normal setup.

Gateway:

```http
POST /api/agent/gateway
Authorization: Bearer mn_sk_...
Content-Type: application/json
```

Every request body is JSON. Put all fields at the top level:

```json
{"api_name":"/notes/search","query":"agent skill","count":10,"skill_version":"1.0.0"}
```

Never wrap business parameters in `params`, `data`, or `body`.

## Authentication

Read the token from:

```text
MINDNOTES_API_KEY
```

The token format starts with `mn_sk_`.

If no token is available, tell the user to open MindNotes Skill settings and generate a personal API Key. Then give exactly one concise setup command for their OS:

Windows PowerShell:

```powershell
setx MINDNOTES_API_KEY "mn_sk_..."
```

macOS/Linux:

```bash
export MINDNOTES_API_KEY="mn_sk_..."
```

Tell the user to restart the AI client after setting it. Do not list alternative secret stores, config files, shell profiles, or multiple setup methods unless the user asks. Do not ask for account passwords. Do not ask for `user_id`.

## Version

Always send:

```json
"skill_version":"1.0.0"
```

If the response contains `upgrade_info`, stop all current API work. Tell the user the MindNotes Skill must be updated or reinstalled, then retry only after the user updates it.

Upgrade response shape:

```json
{
  "ok": false,
  "error": {"code": "upgrade_required", "message": "MindNotes Skill upgrade required"},
  "upgrade_info": {
    "current_version": "0.9.0",
    "latest_version": "1.0.0",
    "minimum_version": "1.0.0",
    "message": "Please update MindNotes Skill to 1.0.0 before continuing."
  },
  "skill_version": "1.0.0"
}
```

## Common Response Envelope

Success:

```json
{"ok":true,"data":{...}}
```

Error:

```json
{"ok":false,"error":{"message":"...","code":"..."}}
```

If `ok` is false, explain the failure in user-facing language. For `401`, ask the user to refresh the API Key. For `403`, explain that the Key lacks the required permission. For `426` or `upgrade_required`, stop and tell the user to update the Skill.

## API Index

| API | Scope | Reference |
|---|---|---|
| `/_list` | `notes:read` | this file |
| `/me` | `notes:read` | this file |
| `/notes/search` | `notes:read` | `notes.md` |
| `/notes/hybrid-search` | `notes:read` | `notes.md` |
| `/notes/similar` | `notes:read` | `notes.md` |
| `/notes/recent` | `notes:read` | `notes.md` |
| `/notes/get` | `notes:read` | `notes.md` |
| `/notes/history` | `notes:read` | `notes.md` |
| `/notes/tags` | `notes:read` | `notes.md` |
| `/notes/by-tag` | `notes:read` | `notes.md` |
| `/notes/due` | `notes:read` | `review.md` |
| `/notes/random` | `notes:read` | `notes.md` |
| `/notes/snippets` | `notes:read` | `notes.md` |
| `/notes/create` | `notes:write` | `notes.md` |
| `/notes/import` | `notes:write` | `notes.md` |
| `/notes/restore-version` | `notes:write` | `notes.md` |
| `/notes/topic-note` | `notes:write` | `notes.md` |
| `/notes/topic-refresh` | `notes:write` | `notes.md` |
| `/notes/topic-cluster-notes` | `notes:write` | `notes.md` |
| `/notes/topic-cluster-sync` | `notes:write` | `notes.md` |
| `/notes/update` | `notes:write` | `notes.md` |
| `/notes/batch-update` | `notes:write` | `notes.md` |
| `/notes/delete` | `notes:write` | `notes.md` |
| `/assets/upload-image` | `notes:write` | `assets.md` |
| `/notes/collect` | `notes:export` | `notes.md` |
| `/notes/topic-brief` | `notes:export` | `notes.md` |
| `/notes/topic-clusters` | `notes:export` | `notes.md` |
| `/notes/topic-draft` | `notes:export` | `notes.md` |
| `/notes/export` | `notes:export` | `notes.md` |
| `/stats/overview` | `stats:read` | `stats.md` |
| `/stats/timeline` | `stats:read` | `stats.md` |
| `/stats/review` | `stats:read` | `stats.md` |
| `/dashboard/summary` | `stats:read` | `stats.md` |
| `/dashboard/activity` | `stats:read` | `stats.md` |
| `/graph/related` | `graph:read` | `stats.md` |
| `/graph/duplicates` | `graph:read` | `stats.md` |
| `/graph/merge-preview` | `graph:read` | `notes.md` |
| `/graph/tag-relations` | `graph:read` | `stats.md` |
| `/graph/orphans` | `graph:read` | `stats.md` |
| `/folders/list` | `notes:read` | `notes.md` |
| `/folders/stats` | `stats:read` | `stats.md` |
| `/folders/relations` | `notes:read` | `notes.md` |
| `/folders/set-relations` | `notes:write` | `notes.md` |
| `/folders/move-note` | `notes:write` | `notes.md` |
| `/review/today` | `notes:read` | `review.md` |
| `/review/next` | `notes:read` | `review.md` |
| `/review/preview` | `notes:read` | `review.md` |
| `/review/summary` | `stats:read` | `review.md` |
| `/review/schedule` | `stats:read` | `review.md` |
| `/review/submit` | `review:write` | `review.md` |
| `/cards/functions` | `notes:read` | `cards.md` |
| `/cards/evaluate` | `notes:read` | `cards.md` |
| `/cards/create` | `cards:write` | `cards.md` |
| `/canvas/list` | `canvas:read` | `canvas.md` |
| `/canvas/get` | `canvas:read` | `canvas.md` |
| `/canvas/relation-types` | `canvas:read` | `canvas.md` |
| `/canvas/create` | `canvas:write` | `canvas.md` |
| `/canvas/update` | `canvas:write` | `canvas.md` |
| `/canvas/delete` | `canvas:write` | `canvas.md` |
| `/canvas/node-create` | `canvas:write` | `canvas.md` |
| `/canvas/node-update` | `canvas:write` | `canvas.md` |
| `/canvas/node-delete` | `canvas:write` | `canvas.md` |
| `/canvas/edge-create` | `canvas:write` | `canvas.md` |
| `/canvas/edge-update` | `canvas:write` | `canvas.md` |
| `/canvas/edge-delete` | `canvas:write` | `canvas.md` |
| `/canvas/group-create` | `canvas:write` | `canvas.md` |
| `/canvas/group-update` | `canvas:write` | `canvas.md` |
| `/canvas/group-delete` | `canvas:write` | `canvas.md` |
| `/canvas/batch-delete` | `canvas:write` | `canvas.md` |
| `/canvas/layout-save` | `canvas:write` | `canvas.md` |
| `/canvas/importable-notes` | `canvas:read` | `canvas.md` |
| `/canvas/import-notes` | `canvas:write` | `canvas.md` |
| `/canvas/suggest-relations` | `canvas:read` | `canvas.md` |
| `/canvas/accept-relations` | `canvas:write` | `canvas.md` |
| `/purchases/list` | `purchases:read` | `purchases.md` |
| `/purchases/categories` | `purchases:read` | `purchases.md` |
| `/purchases/create` | `purchases:write` | `purchases.md` |
| `/purchases/update` | `purchases:write` | `purchases.md` |
| `/purchases/delete` | `purchases:write` | `purchases.md` |

## Diagnostics

List available APIs:

```json
{"api_name":"/_list","skill_version":"1.0.0"}
```

Check the current authorized account and scopes:

```json
{"api_name":"/me","skill_version":"1.0.0"}
```

Use `/me` only for diagnostics or when the user asks which account is connected.
