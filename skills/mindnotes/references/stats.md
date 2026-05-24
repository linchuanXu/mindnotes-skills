# Stats And Graph Reference

Use this for note statistics, timelines, review pressure, and related-note graph views.

## Stats APIs

Overview:

```json
{"api_name":"/stats/overview","skill_version":"1.0.0"}
```

Timeline:

```json
{"api_name":"/stats/timeline","days":30,"skill_version":"1.0.0"}
```

Review distribution:

```json
{"api_name":"/stats/review","days_ahead":7,"skill_version":"1.0.0"}
```

## Stats Field Meanings

| Field | Meaning |
|---|---|
| `total_notes` | Total notes accessible to the current API Key |
| `recent_30d_notes` | Notes created or updated recently |
| `with_memory_count` | Notes with review history |
| `due_review_count` | Notes currently due for review |
| `top_tags` | Most common tags in the current user's notes |
| `timeline` | Activity buckets by date |

Keep the language personal and useful. Prefer "you have 12 notes due" over analytics-heavy phrasing.

## Graph APIs

Related notes:

```json
{"api_name":"/graph/related","note_id":"abc123","count":10,"skill_version":"1.0.0"}
```

Tag relations:

```json
{"api_name":"/graph/tag-relations","tag":"AI","count":30,"skill_version":"1.0.0"}
```

Orphan notes:

```json
{"api_name":"/graph/orphans","mode":"no_shared_tags","count":20,"skill_version":"1.0.0"}
```

Explain relations using shared tags and visible content cues. Do not claim semantic similarity that was not returned or visible in retrieved notes.
