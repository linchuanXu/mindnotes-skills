---
name: mindnotes
description: "MindNotes/my notes: search, summarize, edit, review/抖卡, cards/制卡, Canvas/知识图谱, 物卡, stats, export."
---

# MindNotes

Use this skill to work with the user's own MindNotes knowledge base. Speak about "your notes", "your cards", and "MindNotes"; do not mention databases, SQLite, storage paths, or other users.

## Capability Map

| User intent | Primary workflow | Main APIs |
|---|---|---|
| Search my notes or answer from my notes | Search or hybrid-search, then read details before making claims | `/notes/search`, `/notes/hybrid-search`, `/notes/get` |
| Summarize a topic from my notes | Collect enough notes, group by themes, cite sources | `/notes/search`, `/notes/get`, `/notes/collect` |
| Show recent notes or tags | List recent activity or tag distribution | `/notes/recent`, `/notes/tags`, `/notes/by-tag` |
| Create, edit, or delete a note | Upload image if needed, read exact existing note first, write only after clear intent | `/assets/upload-image`, `/notes/create`, `/notes/get`, `/notes/update`, `/notes/delete` |
| Organize folders or tags | Inspect folder tags, hierarchy, move one note, or batch-preview tag/folder cleanup | `/folders/list`, `/folders/stats`, `/folders/relations`, `/folders/move-note`, `/notes/batch-update` |
| Review today / start TikCard | Show one due card, wait for self-rating, then submit | `/review/next`, `/review/submit` |
| Plan review pressure or dashboard | Summarize due, overdue, activity, and dashboard buckets | `/dashboard/summary`, `/dashboard/activity`, `/review/summary`, `/review/schedule`, `/stats/review` |
| Create or save cards | Choose card maker, then create only on explicit intent | `/cards/functions`, `/cards/create` |
| Find related, isolated, or duplicate ideas | Resolve source notes, explain relation cues, and suggest duplicate candidates only | `/graph/related`, `/graph/duplicates`, `/graph/tag-relations`, `/graph/orphans` |
| Work with Canvas maps | Read maps, edit nodes/edges/groups, import notes, suggest links, accept only after approval | `/canvas/list`, `/canvas/get`, `/canvas/create`, `/canvas/node-create`, `/canvas/edge-create`, `/canvas/import-notes`, `/canvas/suggest-relations`, `/canvas/accept-relations` |
| Manage purchases / 物卡 | List, create, update, or delete personal purchase records | `/purchases/list`, `/purchases/create`, `/purchases/update`, `/purchases/delete` |
| Export or compile notes | Export selected notes as Markdown or JSON | `/notes/export` |
| Diagnose connection | Check account and scopes | `/me`, `/_list` |

## Quick Start

1. Read `references/api.md` before calling the API.
2. Load more specific references only when needed: `workflows.md`, `notes.md`, `assets.md`, `review.md`, `cards.md`, `canvas.md`, `stats.md`, `purchases.md`, `output.md`, `errors.md`, and `anti-patterns.md`.
3. Read the API Key from `MINDNOTES_API_KEY`. The key format starts with `mn_sk_`.
4. Use `https://app.mindnotes.cn` as the default base URL. Use `MINDNOTES_BASE_URL` only when the user explicitly says they are using a self-hosted, staging, or test deployment.
5. If `MINDNOTES_API_KEY` is missing, give one concise setup instruction:
   - Windows PowerShell: `setx MINDNOTES_API_KEY "mn_sk_..."`
   - macOS/Linux: `export MINDNOTES_API_KEY="mn_sk_..."`
   Also tell the user to restart the AI client after setting it.
6. Do not list alternative secret stores, config files, shell profiles, or multiple setup methods unless the user specifically asks.
7. Call `POST /api/agent/gateway` with `Authorization: Bearer $MINDNOTES_API_KEY`.
8. Put `api_name`, business parameters, and `skill_version` in the top-level JSON body.
9. Never send or ask for `user_id`; the API Key identifies the current authorized user.

Minimal call:

```bash
curl -sS "https://app.mindnotes.cn/api/agent/gateway" \
  -H "Authorization: Bearer $MINDNOTES_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"api_name":"/notes/search","query":"部署","count":10,"skill_version":"1.0.0"}'
```

## Safety Gates

Before any write or irreversible action, check intent and permission:

| Action | Gate |
|---|---|
| Submit review | User explicitly rates the active card as `remembered`, `fuzzy`, or `forgotten` |
| Create note | User clearly asks to save/create a normal note |
| Edit note | Exact note was read first; user clearly asked to modify that note; use `dry_run` when the change is broad or ambiguous |
| Batch organize notes | Use `dry_run:true` first; apply only after showing affected notes and receiving explicit approval |
| Delete note | Exact note was read first; user explicitly confirms deletion; send `confirm_delete:true` only after that confirmation |
| Upload image | User provides a local image, data URL, base64 image, or public image URL for a note or asset |
| Create cards | User explicitly asks to save/create/make cards; confirm broad batch creation |
| Import notes to Canvas | User explicitly asks to add those notes to that canvas |
| Delete Canvas items | User explicitly confirms deletion; send `confirm_delete:true` |
| Accept Canvas relations | Suggestions were shown and the user approved them |
| Create/update/delete purchase record | User explicitly asks to record, change, retire, or delete a purchase/asset; deletion needs confirmation |
| Export broad content | User asked to export/package/compile; confirm broad selections |

If setup, permissions, pagination, writes, or failed calls are involved, read `references/anti-patterns.md` and `references/errors.md`.

## Request Contract

Always use flat top-level JSON:

```json
{"api_name":"/notes/recent","count":20,"cursor":"20","skill_version":"1.0.0"}
```

Do not wrap parameters or invent pagination fields:

```json
{"api_name":"/notes/recent","params":{"count":20},"skill_version":"1.0.0"}
```

```json
{"api_name":"/notes/recent","offset":20,"limit":20,"skill_version":"1.0.0"}
```

Use only `next_cursor` returned by the API as the next `cursor`.

If a response contains `upgrade_info`, stop the current task, tell the user to update/reinstall the MindNotes Skill, and retry only after upgrade. Do not keep calling APIs with an outdated skill.

## Workflow Rules

- Search/read: use `/notes/search` or `/notes/hybrid-search`, then `/notes/get` for the notes you rely on. Do not answer detailed questions from previews alone.
- Summaries/writing: read 3-10 relevant notes when possible, group by theme, and separate note facts from your synthesis.
- Create/edit/delete/batch organize: use `/assets/upload-image` before attaching images; resolve exact existing notes with `/notes/get`; prefer `/notes/update` or `/notes/batch-update` with `dry_run:true` for non-trivial changes; never call delete APIs without explicit deletion confirmation. When creating or rewriting a note that will be reviewed later, wrap the key content in `<mark>…</mark>` so it works as an occlusion card (see `references/notes.md`).
- Review, cards, Canvas, and export: follow the Safety Gates before writing or exposing broad content.
- Purchases/物卡: use purchase APIs only for the current user's personal records; deletion requires explicit confirmation.
- Export: use `/notes/export` only when the user asks to export, package, compile, or transform a note set.
- For multi-step work, read `references/workflows.md` and follow the smallest workflow that matches the user's request.

## Response Rules

- Speak in the user's language.
- Say "your notes" or "MindNotes", not "database" or "SQLite".
- Do not reveal API keys, internal paths, token hashes, or implementation details.
- Do not imply access to all users; only the current authorized user's notes are accessible.
- Do not fabricate note contents. Search/read first, then answer.
- Do not submit review results, create/edit/delete notes, upload unrelated images, create cards, edit Canvas, import notes, accept Canvas relations, or change purchase records without explicit user intent.
- Do not apply batch updates or merge/delete duplicate candidates without showing a preview and getting explicit approval.
- When authentication fails, ask the user to refresh their MindNotes API Key and set only `MINDNOTES_API_KEY`.
- When a result may be incomplete due to limits, say what was searched and what limit was used.
- Use `references/output.md` for result formats.
- Use `references/anti-patterns.md` and `references/errors.md` when the request involves setup, permissions, review submission, card creation, Canvas writes, pagination, or failed calls.

## References

- `references/api.md`: gateway protocol, version handling, API names, and response envelope.
- `references/notes.md`: note search, reading, collection, export, tags, field meanings, and note content format (Markdown with `<mark>` occlusion for occlusion cards).
- `references/assets.md`: image upload and reuse for note covers or purchase records.
- `references/review.md`: TikCard review flow, memory fields, statuses, and scheduling.
- `references/cards.md`: card maker selection and safe creation rules.
- `references/canvas.md`: Canvas node/edge/suggestion meanings and approval workflow.
- `references/stats.md`: overview, timeline, review pressure, and graph stats.
- `references/purchases.md`: purchase/asset record fields and safe write rules.
- `references/workflows.md`: multi-step workflows that combine search/read/write/review/Canvas/purchase APIs.
- `references/output.md`: user-facing output templates and failure wording.
- `references/errors.md`: error priority, status handling, and user-facing failure wording.
- `references/anti-patterns.md`: high-risk mistakes, forbidden behavior, and correct/incorrect examples.
