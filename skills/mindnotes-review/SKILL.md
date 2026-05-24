---
name: mindnotes-review
description: Run MindNotes TikCard review sessions through a user's authorized MindNotes account. Use when the user asks to start today's review, continue reviewing cards, preview remembered/fuzzy/forgotten scheduling, submit review results, inspect due cards, overdue cards, or future review pressure.
---

# MindNotes Review

Use this skill for MindNotes TikCard review workflows. Speak about "your cards" and "your review", not internal APIs or storage.

## Connection

Use `MINDNOTES_API_KEY` from the environment. Use `MINDNOTES_BASE_URL` when set; otherwise use `https://app.mindnotes.cn`.

Call:

```http
POST /api/agent/gateway
Authorization: Bearer $MINDNOTES_API_KEY
Content-Type: application/json
```

Put all fields at the top level and always include `"skill_version":"1.0.0"`.

## Workflow

For "start today's review":

1. Call `/review/next`.
2. Show the card title, content, and memory analysis.
3. Ask the user to answer or self-rate.
4. Only after the user clearly says remembered, fuzzy, or forgotten, call `/review/submit`.
5. Continue with `/review/next` until there are no due cards or the user stops.

For planning:

- Use `/review/summary` for due today, overdue, and 7-day pressure.
- Use `/review/schedule` with `days` for future buckets.
- Use `/review/today` to list due cards.
- Use `/review/preview` to explain how each review result changes the next review date.

## APIs

Examples:

```json
{"api_name":"/review/next","skill_version":"1.0.0"}
```

```json
{"api_name":"/review/submit","note_id":"abc123","status":"remembered","skill_version":"1.0.0"}
```

```json
{"api_name":"/review/schedule","days":14,"skill_version":"1.0.0"}
```

`/review/submit` requires a Key with `review:write`.

## Rules

- Do not submit a review result from your own guess.
- If authentication fails, ask the user to refresh their MindNotes API Key.
- If `review:write` is missing, explain that the current Key can view reviews but cannot submit results.
