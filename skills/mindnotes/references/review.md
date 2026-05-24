# Review Reference

Use this for TikCard review sessions, due cards, memory fields, and scheduling.

## Core Flow

Start or continue review:

```json
{"api_name":"/review/next","skill_version":"1.0.0"}
```

Show the card, let the user answer or self-rate, then submit only after the user clearly chooses a result:

```json
{"api_name":"/review/submit","note_id":"abc123","status":"remembered","skill_version":"1.0.0"}
```

Allowed statuses:

| User meaning | Send |
|---|---|
| remembered, I know it, correct, easy, good | `remembered` |
| fuzzy, half remembered, hard, unsure | `fuzzy` |
| forgotten, wrong, again, no memory | `forgotten` |

Do not infer `status` from your own judgment. If the user gives an answer but no self-rating, ask them to rate it.

## Due And Planning APIs

Today's due cards:

```json
{"api_name":"/review/today","count":20,"skill_version":"1.0.0"}
```

Preview scheduling outcomes:

```json
{"api_name":"/review/preview","note_id":"abc123","skill_version":"1.0.0"}
```

Review pressure:

```json
{"api_name":"/review/summary","skill_version":"1.0.0"}
```

```json
{"api_name":"/review/schedule","days":14,"skill_version":"1.0.0"}
```

Notes due before a time:

```json
{"api_name":"/notes/due","count":20,"skill_version":"1.0.0"}
```

## Field Meanings

| Field | Meaning |
|---|---|
| `memory.next_review` | When the note/card is due next |
| `memory.interval_days` | Current interval in days |
| `fsrs.d` | Difficulty; higher means harder |
| `fsrs.s` | Stability; higher means memory lasts longer |
| `fsrs.r` | Retrievability now; higher means easier to recall |
| `preview_memory` | What each result would do before submission |
| `review_info.original_review_time` | Due time before submitting |
| `review_info.next_review_time` | Due time after submitting |

## Safety Rules

- `/review/submit` requires `review:write`.
- Never submit a rating silently.
- Use practical, supportive wording; do not judge the user's habits.
- If `review:write` is missing, continue read-only with review planning and previews.
