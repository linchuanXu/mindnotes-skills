---
name: mindnotes-review
description: Run MindNotes TikCard review sessions through a user's authorized MindNotes account. Use when the user asks to start today's review, continue reviewing cards, preview remembered/fuzzy/forgotten scheduling, submit review results, inspect due cards, overdue cards, future review pressure, or plan a review session.
---

# MindNotes Review

Use this skill for MindNotes TikCard review workflows. Speak about "your cards" and "your review", not internal APIs or storage.

## Connection

Read the API Key from `MINDNOTES_API_KEY`. The key starts with `mn_sk_`.

Use `https://app.mindnotes.cn` by default. Use `MINDNOTES_BASE_URL` only when the user explicitly says self-hosted, staging, or test deployment.

If the Key is missing, give one concise setup instruction:

- Windows PowerShell: `setx MINDNOTES_API_KEY "mn_sk_..."`
- macOS/Linux: `export MINDNOTES_API_KEY="mn_sk_..."`

Tell the user to restart the AI client. Do not list alternative secret stores, config files, shell profiles, or multiple setup methods unless asked.

Call `POST /api/agent/gateway` with `Authorization: Bearer $MINDNOTES_API_KEY`. Put all fields at the top level and always include `"skill_version":"1.0.0"`.

If a response contains `upgrade_info`, stop and tell the user to update/reinstall MindNotes Skill before continuing.

## Review Flow

1. Call `/review/next`.
2. Show the card title, content, and memory analysis.
3. Ask the user to answer or self-rate.
4. Submit only after the user clearly says `remembered`, `fuzzy`, or `forgotten`.
5. Continue with `/review/next` until no due cards remain or the user stops.

Do not infer a review result. If the user answers the card but does not rate it, ask them to choose remembered/fuzzy/forgotten.

## APIs

```json
{"api_name":"/review/next","skill_version":"1.0.0"}
```

```json
{"api_name":"/review/submit","note_id":"abc123","status":"remembered","skill_version":"1.0.0"}
```

```json
{"api_name":"/review/preview","note_id":"abc123","skill_version":"1.0.0"}
```

```json
{"api_name":"/review/summary","skill_version":"1.0.0"}
```

```json
{"api_name":"/review/schedule","days":14,"skill_version":"1.0.0"}
```

## Status Mapping

| User meaning | Send |
|---|---|
| remembered, I know it, correct, easy, good | `remembered` |
| fuzzy, half remembered, hard, unsure | `fuzzy` |
| forgotten, wrong, again, no memory | `forgotten` |

`/review/submit` requires `review:write`. If missing, continue with read-only previews and planning.

## Output

Use this shape:

```text
下一张：标题

内容：...

记忆状态：...

你可以先回答，然后告诉我：记住了 / 有点模糊 / 忘了。
```

Keep the tone practical and supportive.
