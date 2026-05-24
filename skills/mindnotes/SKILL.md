---
name: mindnotes
description: Connect to a user's authorized MindNotes account to search, read, summarize, organize, relate, review, create cards, inspect stats, work with Canvas maps, and export their personal notes through the MindNotes Skill API. Use when the user asks about "my MindNotes notes", "my notes", personal knowledge, study review, note summaries, related ideas, tags, recent notes, TikCard review, card creation, Canvas knowledge maps, or exporting/organizing MindNotes content.
---

# MindNotes

Use this skill to work with the user's own MindNotes knowledge base. Speak about "your notes", "your cards", and "MindNotes"; do not mention databases, SQLite, storage paths, or other users.

## Capability Map

| User intent | Primary workflow | Main APIs |
|---|---|---|
| Search my notes or answer from my notes | Search, then read details before making claims | `/notes/search`, `/notes/get` |
| Summarize a topic from my notes | Collect enough notes, group by themes, cite sources | `/notes/search`, `/notes/get`, `/notes/collect` |
| Show recent notes or tags | List recent activity or tag distribution | `/notes/recent`, `/notes/tags`, `/notes/by-tag` |
| Review today / start TikCard | Show one due card, wait for self-rating, then submit | `/review/next`, `/review/submit` |
| Plan review pressure | Summarize due, overdue, and future buckets | `/review/summary`, `/review/schedule`, `/stats/review` |
| Create or save cards | Choose card maker, then create only on explicit intent | `/cards/functions`, `/cards/create` |
| Find related ideas | Resolve a source note, then explain visible relation cues | `/graph/related`, `/graph/tag-relations` |
| Work with Canvas maps | Read maps, import notes, suggest links, accept only after approval | `/canvas/list`, `/canvas/get`, `/canvas/import-notes`, `/canvas/suggest-relations`, `/canvas/accept-relations` |
| Export or compile notes | Export selected notes as Markdown or JSON | `/notes/export` |
| Diagnose connection | Check account and scopes | `/me`, `/_list` |

## Quick Start

1. Read `references/api.md` before calling the API.
2. Load more specific references only when needed: `notes.md`, `review.md`, `cards.md`, `canvas.md`, `stats.md`, and `output.md`.
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

- Search/read: use `/notes/search`, then `/notes/get` for the notes you rely on. Do not answer detailed questions from previews alone.
- Summaries/writing: read 3-10 relevant notes when possible, group by theme, and separate note facts from your synthesis.
- Review: call `/review/submit` only after the user explicitly rates the card as `remembered`, `fuzzy`, or `forgotten`. Do not infer the rating from your own judgment.
- Cards: call `/cards/create` only when the user asks to save, create, generate, or turn content into cards. If they only ask a question, answer without creating cards.
- Canvas: call `/canvas/accept-relations` only after showing suggestions and getting user approval.
- Export: use `/notes/export` only when the user asks to export, package, compile, or transform a note set.

## Response Rules

- Speak in the user's language.
- Say "your notes" or "MindNotes", not "database" or "SQLite".
- Do not reveal API keys, internal paths, token hashes, or implementation details.
- Do not imply access to all users; only the current authorized user's notes are accessible.
- Do not fabricate note contents. Search/read first, then answer.
- Do not submit review results, create cards, import notes, or accept Canvas relations without explicit user intent.
- When authentication fails, ask the user to refresh their MindNotes API Key and set only `MINDNOTES_API_KEY`.
- When a result may be incomplete due to limits, say what was searched and what limit was used.
- Use `references/output.md` for result formats.

## References

- `references/api.md`: gateway protocol, version handling, API names, and response envelope.
- `references/notes.md`: note search, reading, collection, export, tags, and field meanings.
- `references/review.md`: TikCard review flow, memory fields, statuses, and scheduling.
- `references/cards.md`: card maker selection and safe creation rules.
- `references/canvas.md`: Canvas node/edge/suggestion meanings and approval workflow.
- `references/stats.md`: overview, timeline, review pressure, and graph stats.
- `references/output.md`: user-facing output templates and failure wording.
