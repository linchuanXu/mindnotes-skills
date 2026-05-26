# Notes Reference

Use this when searching, reading, creating, editing, deleting, collecting, exporting, or organizing the user's notes.

## Search And Read

Search:

```json
{"api_name":"/notes/search","query":"部署","count":10,"skill_version":"1.0.0"}
```

Hybrid search when the user asks conceptually, combines tags/title/content signals, or normal search is too narrow:

```json
{"api_name":"/notes/hybrid-search","query":"agent skill","tags":["AI"],"count":10,"skill_version":"1.0.0"}
```

`/notes/hybrid-search` returns ranked previews with `signals` such as `full_text`, `title`, `tag`, `content`, and `terms`. Use those signals for ranking and disambiguation only. Read details with `/notes/get` before detailed claims.

Find similar notes from a note or a raw text fragment:

```json
{"api_name":"/notes/similar","note_id":"abc123","count":10,"threshold":0.45,"skill_version":"1.0.0"}
```

```json
{"api_name":"/notes/similar","title":"复盘","text":"hybrid search and skill cleanup","count":10,"skill_version":"1.0.0"}
```

`/notes/similar` compares title and content together and returns `score`, `signals`, and overlapping terms. Use it when the user asks "我之前还写过类似的吗" or when shared tags are too weak.

Read a note before making detailed claims:

```json
{"api_name":"/notes/get","note_id":"abc123","skill_version":"1.0.0"}
```

Read edit history when the user asks what changed or wants a quick audit trail:

```json
{"api_name":"/notes/history","note_id":"abc123","count":10,"skill_version":"1.0.0"}
```

`/notes/history` is read-only. It returns changed fields plus short previews of old/new content so you can explain the change without dumping large bodies by default.

To restore a note to a previous version, preview the rollback first:

```json
{"api_name":"/notes/restore-version","note_id":"abc123","history_index":0,"dry_run":true,"skill_version":"1.0.0"}
```

You can target a history entry by:

| Field | Meaning |
|---|---|
| `history_index` | `0` means the newest history record returned by `/notes/history` |
| `update_time` | Exact `update_time` from a history record |

`/notes/restore-version` restores only user-editable fields: `title`, `content`, `tags`, and `image`. It does not roll back review memory or other hidden state.

Apply only after reviewing the preview:

```json
{"api_name":"/notes/restore-version","note_id":"abc123","history_index":0,"confirm_restore":true,"expected_updated_at":"2026-05-23T12:00:00","skill_version":"1.0.0"}
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

Use `/notes/topic-brief` when the user wants a fast structured overview before a full synthesis:

```json
{"api_name":"/notes/topic-brief","query":"学习方法","topic":"学习方法","count":10,"skill_version":"1.0.0"}
```

`/notes/topic-brief` returns:

| Field | Meaning |
|---|---|
| `topic` | User-facing topic label |
| `key_notes` | A few notes worth reading first |
| `anchors` | Repeated terms that seem to hold the topic together |
| `coverage_gaps` | Signals that the topic is still sparse or loosely connected |
| `suggested_next_steps` | Safe follow-ups for organizing or summarizing the topic |

Use it to frame the conversation, then read the key notes in full before making strong claims.

Use `/notes/topic-clusters` when the user wants the topic split into subthemes:

```json
{"api_name":"/notes/topic-clusters","query":"学习方法","topic":"学习方法","count":10,"skill_version":"1.0.0"}
```

`/notes/topic-clusters` returns:

| Field | Meaning |
|---|---|
| `clusters` | Subtopic groups labeled by the strongest recurring tag |
| `orphan_notes` | Notes that did not fit into the top clusters |
| `suggested_next_steps` | Practical next actions for cleaning up the structure |

Use it when the note set feels broad and you need to decide how many separate summaries or folders the topic should become.

Use `/notes/topic-draft` when the user wants a first-pass written draft assembled from the topic structure:

```json
{"api_name":"/notes/topic-draft","query":"学习方法","topic":"学习方法","count":10,"skill_version":"1.0.0"}
```

`/notes/topic-draft` returns a `draft_markdown` preview plus the supporting `brief` and `clusters` it was built from.

Treat this as a working draft, not a final claim. Read the key notes in full before sending the draft back as a polished answer or saving it as a note.

When the user wants to turn that draft into a note, use `/notes/topic-note` first as a preview:

```json
{"api_name":"/notes/topic-note","query":"学习方法","topic":"学习方法","title":"学习方法整理","tags":["整理"],"folder":"📁Summary","dry_run":true,"skill_version":"1.0.0"}
```

Then create it only after approval:

```json
{"api_name":"/notes/topic-note","query":"学习方法","topic":"学习方法","title":"学习方法整理","tags":["整理"],"confirm_create":true,"skill_version":"1.0.0"}
```

`/notes/topic-note` is the bridge between topic analysis and a saved note. It reuses the generated topic draft and shows the exact title, tags, and content preview before writing.

When the user already has a summary note and wants it refreshed from the latest related notes, preview `/notes/topic-refresh` first:

```json
{"api_name":"/notes/topic-refresh","note_id":"abc123","query":"学习方法","topic":"学习方法","title":"学习方法整理","dry_run":true,"skill_version":"1.0.0"}
```

Then apply only after approval:

```json
{"api_name":"/notes/topic-refresh","note_id":"abc123","query":"学习方法","topic":"学习方法","confirm_refresh":true,"expected_updated_at":"2026-05-23T12:00:00","skill_version":"1.0.0"}
```

`/notes/topic-refresh` keeps the same note id and previews:

| Field | Meaning |
|---|---|
| `current_note` | The existing note before refresh |
| `refreshed_note` | The proposed updated note content |
| `changes` | Title/content/tag/image differences that would be written |
| `draft_markdown` | The regenerated topic draft backing the refresh |

Use it when a topic summary note already exists and should be updated instead of creating a second summary note.

When the user wants one summary note per subtopic cluster, preview `/notes/topic-cluster-notes` first:

```json
{"api_name":"/notes/topic-cluster-notes","query":"学习方法","topic":"学习方法","title_prefix":"学习方法整理","folder":"📁Summary","dry_run":true,"skill_version":"1.0.0"}
```

Then create the cluster notes only after approval:

```json
{"api_name":"/notes/topic-cluster-notes","query":"学习方法","topic":"学习方法","title_prefix":"学习方法整理","confirm_create":true,"skill_version":"1.0.0"}
```

`/notes/topic-cluster-notes` returns:

| Field | Meaning |
|---|---|
| `previews` | One proposed summary note per top cluster |
| `cluster_note_count` | How many cluster notes will be created |
| `brief` | Each preview includes its own topic-specific synthesis brief |
| `note_ids` | The source notes used for that cluster preview |

Use it when one large topic should become several smaller summary notes instead of one broad recap.

When the user already has cluster summary notes and wants to sync them with the latest source notes, preview `/notes/topic-cluster-sync` first:

```json
{"api_name":"/notes/topic-cluster-sync","query":"学习方法","topic":"学习方法","title_prefix":"学习方法整理","tags":["同步"],"dry_run":true,"skill_version":"1.0.0"}
```

Then apply only after approval:

```json
{"api_name":"/notes/topic-cluster-sync","query":"学习方法","topic":"学习方法","title_prefix":"学习方法整理","confirm_sync":true,"expected_updated_at":"2026-05-23T12:00:00","skill_version":"1.0.0"}
```

`/notes/topic-cluster-sync` previews one result per cluster:

| Field | Meaning |
|---|---|
| `action` | `update`, `create`, or `skip` for that cluster note |
| `existing_note` | Present when the cluster already has a matching summary note |
| `proposed_note` | The note state that would be written or created |
| `changes` | Field-level differences for update actions |

Use it when the cluster summaries already exist and should stay aligned with the latest topic structure without manually refreshing each one.

## Edit And Delete

Create a normal note only when the user asks to save or create one:

```json
{"api_name":"/notes/create","title":"会议记录","content":"完整正文","tags":["会议"],"image":"static/uploads/7-alice/cover.png","skill_version":"1.0.0"}
```

Use `dry_run:true` first when the title/content/tags were inferred from broad instructions.

For bulk import from Markdown or JSON, preview first:

```json
{"api_name":"/notes/import","markdown":"# 标题\n\n正文","default_tags":["导入"],"dry_run":true,"skill_version":"1.0.0"}
```

You can also send:

```json
{"api_name":"/notes/import","json_text":"[{\"title\":\"A\",\"content\":\"B\"}]","dry_run":true,"skill_version":"1.0.0"}
```

or:

```json
{"api_name":"/notes/import","notes":[{"title":"A","content":"B","tags":["学习"]}],"dry_run":true,"skill_version":"1.0.0"}
```

`/notes/import` supports:

| Field | Meaning |
|---|---|
| `notes` | Explicit note objects to import |
| `markdown` / `markdown_text` | Markdown source split by `# ` headings |
| `json_text` | JSON note object, array, or `{ "notes": [...] }` |
| `source_text` | Auto-detect JSON vs Markdown |
| `default_tags` | Tags applied to every imported note |
| `folder` | Folder tag applied to every imported note; must start with `📁` |

The preview returns title matches against existing notes so you can spot duplicates before writing.

Apply only after reviewing the preview:

```json
{"api_name":"/notes/import","notes":[{"title":"A","content":"B"}],"confirm_import":true,"skill_version":"1.0.0"}
```

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

## Batch Organization

Use `/notes/batch-update` for explicit cleanup tasks such as batch tagging, removing stale tags, or moving selected notes to a folder. Always preview first:

```json
{"api_name":"/notes/batch-update","note_ids":["abc123","def456"],"add_tags":["复盘"],"dry_run":true,"skill_version":"1.0.0"}
```

Supported operations:

| Field | Meaning |
|---|---|
| `add_tags` | Add tags while preserving existing tags |
| `remove_tags` | Remove specific tags |
| `set_tags` | Replace all tags |
| `folder` / `folder_id` | Move notes to a folder tag that starts with `📁` |
| `folder_mode` | `set`, `add`, or `remove`; default is `set` |

Apply only after showing the dry-run result and getting explicit approval:

```json
{"api_name":"/notes/batch-update","note_ids":["abc123","def456"],"add_tags":["复盘"],"confirm_batch":true,"skill_version":"1.0.0"}
```

Do not use batch update for generated rewrites of note content. Batch update currently changes tags and folders only.

Use `/notes/batch-rewrite` when the user wants deterministic title/content rewrites across a resolved note set. Always preview first:

```json
{"api_name":"/notes/batch-rewrite","note_ids":["abc123","def456"],"title_prefix":"整理: ","find_text":"旧词","replace_text":"新词","content_suffix":"\n\n总结","dry_run":true,"skill_version":"1.0.0"}
```

Apply only after review:

```json
{"api_name":"/notes/batch-rewrite","note_ids":["abc123","def456"],"title_prefix":"整理: ","content_suffix":"\n\n总结","confirm_rewrite":true,"skill_version":"1.0.0"}
```

Supported rewrite operations:

| Field | Meaning |
|---|---|
| `title_prefix` / `title_suffix` | Add fixed text around the title |
| `content_prefix` / `content_suffix` | Add fixed text around the full content |
| `find_text` + `replace_text` | Replace exact text in both title and content |
| `trim_whitespace` | Strip leading/trailing whitespace from title and content |
| `collapse_blank_lines` | Compress runs of 3+ blank lines down to 2 |

Use batch rewrite for patterned cleanup, normalization, or summary markers after the user has already selected the target notes. Do not use it as a substitute for freeform AI rewriting.

## Duplicate Candidates

Use `/graph/duplicates` to find likely duplicate or near-duplicate notes. It is suggest-only:

```json
{"api_name":"/graph/duplicates","mode":"all","threshold":0.88,"count":20,"skill_version":"1.0.0"}
```

Modes:

| Value | Meaning |
|---|---|
| `all` | Exact content/title groups plus similar pairs |
| `exact` | Same title and content fingerprint |
| `title` | Same normalized title |
| `similar` | Similar title/content pairs above `threshold` |

Never merge or delete from duplicate candidates alone. Read the full notes, show the candidate group, propose a merge plan, and get explicit approval before any write.

When the user wants to see how duplicates could be consolidated, preview a merge plan first:

```json
{"api_name":"/graph/merge-preview","note_ids":["abc123","def456"],"skill_version":"1.0.0"}
```

`/graph/merge-preview` is read-only. It proposes:

| Field | Meaning |
|---|---|
| `primary_note` | Suggested note to keep editing, usually the most recently updated one |
| `duplicate_notes` | The other notes that would stay separate until the user confirms anything |
| `merged_note` | A draft title, tag set, and combined content preview |
| `content_sources` | Which duplicates contribute unique lines beyond the primary note |
| `warnings` | Reasons to slow down before merging or deleting |

Use merge preview to explain the plan and ask for approval. Do not apply updates or deletions from this preview alone.

Permission boundary:

| Scope | Allows |
|---|---|
| `notes:read` | Search, list, and read notes |
| `notes:write` | Edit or delete explicitly selected notes |
| `notes:export` | Collect or export full note sets |

Do not treat `notes:read` as permission to export large bodies of content.
Do not treat `notes:write` as permission to edit/delete without user intent.

## Folders

Folders are represented as tags that start with `📁`.

List folders:

```json
{"api_name":"/folders/list","skill_version":"1.0.0"}
```

Move one note to a folder after resolving the exact note:

```json
{"api_name":"/folders/move-note","note_id":"abc123","folder":"📁项目","mode":"set","expected_updated_at":"2026-05-23T12:00:00","skill_version":"1.0.0"}
```

Use `mode:"add"` to keep existing folder tags, `mode:"remove"` to remove one folder, and `mode:"set"` to replace existing folders.

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
