# MindNotes Skill API

Use this reference when calling MindNotes.

## Endpoint

Default base URL:

```text
https://app.mindnotes.cn
```

Use `MINDNOTES_BASE_URL` if the user or environment provides a different base URL.

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

Do not wrap business parameters in `params`, `data`, or `body`.

## Authentication

Read the token from:

```text
MINDNOTES_API_KEY
```

If no token is available, tell the user to open MindNotes Skill settings and generate a personal API Key. Do not ask for account passwords. Do not ask for `user_id`.

## Common Response Envelope

Success:

```json
{"ok":true,"data":{...}}
```

Error:

```json
{"ok":false,"error":{"message":"...","code":"..."}}
```

If `ok` is false, explain the failure in user-facing language. For `401`, ask the user to refresh the API Key. For `403`, explain that the Key lacks the required permission.

## APIs

### `/_list`

List available APIs and parameter hints.

```json
{"api_name":"/_list","skill_version":"1.0.0"}
```

### `/me`

Check the current authorized account and scopes.

```json
{"api_name":"/me","skill_version":"1.0.0"}
```

Use only for diagnostics or when the user asks which account is connected.

### `/notes/search`

Search the user's notes.

Parameters:

| Field | Type | Required | Notes |
|---|---|---:|---|
| `query` | string | yes | Search text |
| `count` | number | no | Default 20, max 50 |
| `field` | string | no | Usually omit; backend defaults to content |

Example:

```json
{"api_name":"/notes/search","query":"部署","count":10,"skill_version":"1.0.0"}
```

Response data:

```json
{"notes":[{"id":"...","title":"...","preview":"...","tags":[],"updated_at":"..."}],"query":"部署","next_cursor":null}
```

### `/notes/recent`

List recent notes.

Parameters:

| Field | Type | Required | Notes |
|---|---|---:|---|
| `count` | number | no | Default 20, max 100 |
| `cursor` | string | no | Use returned `next_cursor` |
| `sort_by` | string | no | `updated_at`, `created_at`, or `next_review` |

Example:

```json
{"api_name":"/notes/recent","count":20,"skill_version":"1.0.0"}
```

### `/notes/get`

Read one note in full.

Parameters:

| Field | Type | Required | Notes |
|---|---|---:|---|
| `note_id` | string | yes | Note id from search/recent/export |

Example:

```json
{"api_name":"/notes/get","note_id":"abc123","skill_version":"1.0.0"}
```

Response data includes `note.content`, `note.title`, `note.tags`, `note.updated_at`, and `note.memory`.

### `/notes/tags`

List tag counts.

```json
{"api_name":"/notes/tags","skill_version":"1.0.0"}
```

### `/notes/by-tag`

List notes matching one or more tags.

```json
{"api_name":"/notes/by-tag","tag":"AI","count":20,"skill_version":"1.0.0"}
```

Use `tags` with an array and `match:"all"` when every tag must match.

### `/notes/due`

List notes due before a date.

```json
{"api_name":"/notes/due","count":20,"skill_version":"1.0.0"}
```

### `/notes/random`

Randomly sample notes for review or rediscovery.

```json
{"api_name":"/notes/random","tag":"产品","count":5,"skill_version":"1.0.0"}
```

### `/notes/snippets`

Return keyword snippets from matching notes.

```json
{"api_name":"/notes/snippets","query":"复习","count":10,"skill_version":"1.0.0"}
```

### `/stats/overview`

Get note and review overview.

```json
{"api_name":"/stats/overview","skill_version":"1.0.0"}
```

Response data includes:

| Field | Meaning |
|---|---|
| `total_notes` | Total notes |
| `recent_30d_notes` | Notes updated or created recently |
| `with_memory_count` | Notes with memory/review history |
| `due_review_count` | Notes due for review |
| `top_tags` | Most common tags |

### `/stats/timeline`

Get recent note activity by day.

```json
{"api_name":"/stats/timeline","days":30,"skill_version":"1.0.0"}
```

### `/stats/review`

Get review distribution, due notes, upcoming notes, and unscheduled examples.

```json
{"api_name":"/stats/review","days_ahead":7,"skill_version":"1.0.0"}
```

### `/graph/related`

Find notes related to a source note.

Parameters:

| Field | Type | Required | Notes |
|---|---|---:|---|
| `note_id` | string | yes | Source note id |
| `count` | number | no | Default 10, max 30 |

Example:

```json
{"api_name":"/graph/related","note_id":"abc123","count":10,"skill_version":"1.0.0"}
```

### `/graph/tag-relations`

Find common tag co-occurrences.

```json
{"api_name":"/graph/tag-relations","tag":"AI","count":30,"skill_version":"1.0.0"}
```

### `/graph/orphans`

Find notes without tags or without shared tag connections.

```json
{"api_name":"/graph/orphans","mode":"no_shared_tags","count":20,"skill_version":"1.0.0"}
```

### `/notes/collect`

Collect full note details for synthesis, reports, or writing.

```json
{"api_name":"/notes/collect","query":"学习方法","count":10,"skill_version":"1.0.0"}
```

## Review APIs

### `/review/today`

Get cards due today.

```json
{"api_name":"/review/today","count":20,"skill_version":"1.0.0"}
```

### `/review/next`

Get the next card to study, including preview outcomes.

```json
{"api_name":"/review/next","skill_version":"1.0.0"}
```

### `/review/preview`

Preview scheduling outcomes for one card.

```json
{"api_name":"/review/preview","note_id":"abc123","skill_version":"1.0.0"}
```

### `/review/submit`

Submit a review result. Requires `review:write`.

```json
{"api_name":"/review/submit","note_id":"abc123","status":"remembered","skill_version":"1.0.0"}
```

Allowed statuses: `remembered`, `fuzzy`, `forgotten`. The API also accepts close variants such as `good`, `hard`, and `again`.

### `/review/summary`

Get due, overdue, and 7-day review pressure.

```json
{"api_name":"/review/summary","skill_version":"1.0.0"}
```

### `/review/schedule`

Get future review buckets.

```json
{"api_name":"/review/schedule","days":14,"skill_version":"1.0.0"}
```

## Card Creation APIs

### `/cards/functions`

List available card makers.

```json
{"api_name":"/cards/functions","skill_version":"1.0.0"}
```

### `/cards/create`

Create and save cards from text. Requires `cards:write`.

```json
{"api_name":"/cards/create","function_id":"ai_save","text":"要保存的内容","tags":["AI"],"skill_version":"1.0.0"}
```

## Canvas APIs

### `/canvas/list`

List the user's canvases.

```json
{"api_name":"/canvas/list","skill_version":"1.0.0"}
```

### `/canvas/get`

Read a canvas graph.

```json
{"api_name":"/canvas/get","canvas_id":"canvas-1","skill_version":"1.0.0"}
```

### `/canvas/import-notes`

Import notes into a canvas. Requires `canvas:write`.

```json
{"api_name":"/canvas/import-notes","canvas_id":"canvas-1","note_ids":["abc123"],"skill_version":"1.0.0"}
```

### `/canvas/suggest-relations`

Suggest relations between canvas nodes.

```json
{"api_name":"/canvas/suggest-relations","canvas_id":"canvas-1","skill_version":"1.0.0"}
```

### `/canvas/accept-relations`

Accept suggested relations. Requires `canvas:write`.

```json
{"api_name":"/canvas/accept-relations","canvas_id":"canvas-1","suggestions":[{"source_node_id":"node-1","target_node_id":"node-2","type":"related"}],"skill_version":"1.0.0"}
```

### `/notes/export`

Export a set of notes.

Parameters:

| Field | Type | Required | Notes |
|---|---|---:|---|
| `format` | string | no | `markdown` or `json`; default `markdown` |
| `query` | string | no | Search query |
| `tag` | string | no | Filter by tag |
| `note_ids` | array | no | Specific note ids |
| `count` | number | no | Default 50, max 200 |

Examples:

```json
{"api_name":"/notes/export","format":"markdown","query":"AI 产品","count":30,"skill_version":"1.0.0"}
```

```json
{"api_name":"/notes/export","format":"json","tag":"FSRS","count":50,"skill_version":"1.0.0"}
```

## Version

Always send:

```json
"skill_version":"1.0.0"
```
