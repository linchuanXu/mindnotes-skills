# Anti-Patterns Reference

Use this reference when a request touches setup, permissions, review submission, card creation, Canvas writes, pagination, or failed API calls.

## Never Do These

| Risk | Wrong behavior | Correct behavior |
|---|---|---|
| Setup drift | List secret stores, config files, shell profiles, and several setup options | Give exactly one OS-appropriate `MINDNOTES_API_KEY` command and tell the user to restart the AI client |
| Storage leak | Say "database", "SQLite", paths, token hashes, or table names | Say "your notes", "your cards", or "MindNotes" |
| Cross-user leak | Say or imply access to all users' data | Say the API Key only grants the current authorized user's notes |
| Parameter wrapping | Put business fields inside `params`, `data`, or `body` | Put `api_name`, parameters, and `skill_version` at the top level |
| Pagination invention | Use `offset`, page numbers, or guessed cursors | Reuse only the returned `next_cursor` |
| Preview hallucination | Summarize details from search previews | Call `/notes/get` or `/notes/collect` before detailed claims |
| Note edit from preview | Rewrite or retag a note from search preview only | Call `/notes/get`, show or preview the intended change, then update |
| Accidental deletion | Delete a note from a vague request or without confirmation | Read the note first and call `/notes/delete` only with `confirm_delete:true` after explicit user approval |
| Review guessing | Submit `remembered`, `fuzzy`, or `forgotten` from your own judgment | Ask the user to self-rate before `/review/submit` |
| Silent card writes | Create cards while answering a normal question | Create cards only when the user clearly asks to save/create/make cards |
| Canvas write without consent | Accept suggested relations immediately | Show suggestions first, then accept only after approval |
| Upgrade loop | Continue API calls after `upgrade_info` | Stop and tell the user to update/reinstall the Skill |

## Correct And Incorrect Requests

Correct flat request:

```json
{"api_name":"/notes/recent","count":20,"cursor":"20","skill_version":"1.0.0"}
```

Incorrect wrapped request:

```json
{"api_name":"/notes/recent","params":{"count":20},"skill_version":"1.0.0"}
```

Incorrect invented pagination:

```json
{"api_name":"/notes/recent","offset":20,"limit":20,"skill_version":"1.0.0"}
```

Correct review submission only after user rating:

```json
{"api_name":"/review/submit","note_id":"abc123","status":"fuzzy","skill_version":"1.0.0"}
```

Do not call this just because the user's answer looked partially correct.

Correct edit preview before applying a broad rewrite:

```json
{"api_name":"/notes/update","note_id":"abc123","content":"新的完整内容","dry_run":true,"skill_version":"1.0.0"}
```

Correct deletion only after explicit confirmation:

```json
{"api_name":"/notes/delete","note_id":"abc123","confirm_delete":true,"skill_version":"1.0.0"}
```

## Permission And Error Priority

1. If `upgrade_info` exists, stop and ask the user to update the Skill.
2. If authentication fails, ask the user to refresh the API Key and set only `MINDNOTES_API_KEY`.
3. If permission is missing, name the missing capability in user language.
4. If results are empty, state what was searched and avoid guessing.
5. If the request would write data, confirm intent unless it was already explicit.
6. If the request deletes data, require explicit deletion confirmation.
