---
name: mindnotes
description: Connect to a user's authorized MindNotes account to search, read, summarize, organize, relate, review, and export their personal notes through the MindNotes Skill API. Use when the user asks about "my MindNotes notes", "my notes", personal knowledge, study review, note summaries, related ideas, tags, recent notes, or exporting/organizing MindNotes content.
---

# MindNotes

Use this skill to help the user work with their own MindNotes knowledge base. Treat MindNotes as a personal note system: users should talk about "my notes" and "my knowledge", not databases, tables, or storage paths.

## Quick Start

1. Read `references/api.md` before calling the API.
2. Use `MINDNOTES_API_KEY` from the environment or ask the user to generate one in MindNotes if it is missing.
3. Use `MINDNOTES_BASE_URL` when set; otherwise use `https://app.mindnotes.cn`.
4. Call `POST /api/agent/gateway` with `Authorization: Bearer $MINDNOTES_API_KEY`.
5. Put `api_name`, business parameters, and `skill_version` in the top-level JSON body.
6. Never send or ask for `user_id`; the API Key identifies the current user.

Minimal call:

```bash
curl -sS "$MINDNOTES_BASE_URL/api/agent/gateway" \
  -H "Authorization: Bearer $MINDNOTES_API_KEY" \
  -H "Content-Type: application/json" \
  -d '{"api_name":"/notes/search","query":"部署","count":10,"skill_version":"1.0.0"}'
```

## Workflow

For search or question-answering:

1. Search with `/notes/search`.
2. Read the most relevant notes with `/notes/get` before making claims about detailed content.
3. Answer from retrieved content only. If nothing relevant is found, say so plainly.
4. Cite sources by title and update date. Include note ids only when the user needs to open or disambiguate notes.

For summaries, reports, outlines, and writing:

1. Search by the user's topic, tag, or time hint.
2. Read enough note details to cover the answer, usually 3-10 notes.
3. Group information by theme rather than by raw search order.
4. Preserve uncertainty: distinguish direct note content from your synthesis.

For recent activity or review:

1. Use `/review/today` or `/review/next` when the user wants to study now.
2. Use `/review/preview` before explaining what "remembered", "fuzzy", or "forgotten" will do.
3. Use `/review/submit` only when the user clearly says the card result. This requires `review:write`.
4. Use `/review/summary`, `/review/schedule`, and `/stats/overview` for planning and review pressure.
5. Avoid judging the user's habits; keep the tone practical and supportive.

For card creation:

1. Use `/cards/functions` when unsure which card maker fits the request.
2. Use `/cards/create` when the user asks to save, make, generate, or turn content into cards. This requires `cards:write`.
3. Confirm before creating many cards from broad or long input.

For related ideas:

1. If the user references a specific note, resolve it with search if needed.
2. Call `/graph/related` with the selected `note_id`.
3. Explain why notes are related using shared tags or visible content cues.

For Canvas and knowledge maps:

1. Use `/canvas/list` and `/canvas/get` when the user asks about an existing knowledge map.
2. Use `/canvas/import-notes` only when the user asks to add notes to a canvas. This requires `canvas:write`.
3. Use `/canvas/suggest-relations` to propose links; use `/canvas/accept-relations` only after the user approves. This requires `canvas:write`.

For exports:

1. Use `/notes/export` only when the user asks to export, package, compile, or transform a set of notes.
2. Prefer Markdown for writing and review; use JSON for tools or data processing.
3. Do not create or update notes unless a future write API is documented.

## Response Rules

- Speak in the user's language.
- Say "your notes" or "MindNotes", not "database" or "SQLite".
- Do not reveal API keys, internal paths, token hashes, or implementation details.
- Do not imply access to all users; only the current authorized user's notes are accessible.
- Do not fabricate note contents. Search/read first, then answer.
- Do not submit review results, create cards, or modify canvases without explicit user intent.
- When API authentication fails, ask the user to generate or refresh their MindNotes API Key.
- When a result may be incomplete due to limits, say what was searched and what limit was used.

## References

- `references/api.md`: gateway format, API names, parameters, and response shapes.
- `references/workflows.md`: concrete task workflows and output formats.
