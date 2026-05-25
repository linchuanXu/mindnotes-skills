# Notes Reference

Use this when searching, reading, editing, deleting, collecting, or exporting the user's notes.

## Search And Read

Search:

```json
{"api_name":"/notes/search","query":"部署","count":10,"skill_version":"1.0.0"}
```

Read a note before making detailed claims:

```json
{"api_name":"/notes/get","note_id":"abc123","skill_version":"1.0.0"}
```

Search results contain previews. Previews are enough for ranking and short match lists, but not enough for detailed summaries, quotes, plans, or decisions. Use `/notes/get` for the notes you rely on.

## Recent, Tags, And Pagination

Recent notes:

```json
{"api_name":"/notes/recent","count":20,"cursor":"20","sort_by":"updated_at","skill_version":"1.0.0"}
```

Only use the returned `next_cursor` as the next request's `cursor`. Do not invent `offset`, page numbers, or arithmetic cursors.

`sort_by` values:

| Value | Meaning |
|---|---|
| `updated_at` | Recently changed notes |
| `created_at` | Recently created notes |
| `next_review` | Upcoming review order, not creation time |

Tags:

```json
{"api_name":"/notes/tags","skill_version":"1.0.0"}
```

```json
{"api_name":"/notes/by-tag","tag":"AI","count":20,"skill_version":"1.0.0"}
```

Use `tags:["AI","产品"]` and `match:"all"` only when every tag must match.

## Collect And Export

Use `/notes/collect` for synthesis, reports, and writing when the user wants you to work from a topic-sized set of notes:

```json
{"api_name":"/notes/collect","query":"学习方法","count":10,"skill_version":"1.0.0"}
```

`/notes/collect` returns full notes plus synthesis helpers:

| Field | Meaning |
|---|---|
| `top_tags` | Tag counts across the collected notes |
| `themes` | Tag-based theme groups with source note titles and ids |
| `timeline` | Collected notes ordered by recent update |
| `notes` | Full note objects for detailed synthesis |

Use `themes` and `timeline` to structure summaries before reading every note linearly.

## Edit And Delete

Edit only one resolved note at a time. Search if needed, then call `/notes/get` first so the user can disambiguate the exact note.

Preview an edit when the change is broad, destructive, or generated from your own rewrite:

```json
{"api_name":"/notes/update","note_id":"abc123","content":"新的完整内容","expected_updated_at":"2026-05-23T12:00:00","dry_run":true,"skill_version":"1.0.0"}
```

Apply an edit only after the user clearly asks to change that note:

```json
{"api_name":"/notes/update","note_id":"abc123","title":"新标题","tags":["AI","复盘"],"expected_updated_at":"2026-05-23T12:00:00","skill_version":"1.0.0"}
```

Editable fields are `title`, `content`, `tags`, and `image`. For `content`, send the complete replacement content, not a vague instruction. Include `expected_updated_at` from the last `/notes/get` when available; if the API returns `conflict`, read the note again before retrying.

Delete requires explicit user confirmation. You may preview deletion first:

```json
{"api_name":"/notes/delete","note_id":"abc123","dry_run":true,"skill_version":"1.0.0"}
```

Only after the user confirms deletion:

```json
{"api_name":"/notes/delete","note_id":"abc123","confirm_delete":true,"expected_updated_at":"2026-05-23T12:00:00","skill_version":"1.0.0"}
```

Use `/notes/export` only when the user asks to export, package, compile, or transform notes:

```json
{"api_name":"/notes/export","format":"markdown","query":"AI 产品","count":30,"skill_version":"1.0.0"}
```

Permission boundary:

| Scope | Allows |
|---|---|
| `notes:read` | Search, list, and read notes |
| `notes:write` | Edit or delete explicitly selected notes |
| `notes:export` | Collect or export full note sets |

Do not treat `notes:read` as permission to export large bodies of content.
Do not treat `notes:write` as permission to edit/delete without user intent.

## Field Meanings

| Field | Meaning |
|---|---|
| `id` | Note id for follow-up API calls |
| `title` | User-facing note title |
| `preview` | Short snippet; not complete source material |
| `content` | Full note body, returned by `/notes/get`, `/notes/collect`, or export |
| `tags` | User note tags |
| `updated_at` | Last update time |
| `created_at` | Creation time |
| `memory.next_review` | Review due time, not creation or update time |
| `memory.interval_days` | Current review interval |
| `memory.d` | Memory difficulty |
| `memory.s` | Memory stability |
| `memory.r` | Current retrievability probability when returned by review APIs |

## Safe Behavior

- Do not say a note contains something unless it was in a retrieved preview or full note.
- Do not expose note ids unless needed for disambiguation or follow-up actions.
- Do not edit or delete a note from search previews alone; read the exact note first.
- Do not delete a note unless the user explicitly confirms deletion.
- If broad export may be large, confirm the selection first.
- If no relevant notes are found, say what query was searched and avoid guessing.
