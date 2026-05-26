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

Review cleanup/topic tracking notes in one overview when the user wants to see ongoing task notes and reports:

```json
{"api_name":"/notes/tracking-overview","kinds":["cleanup-task","topic-task"],"count":10,"skill_version":"1.0.0"}
```

`/notes/tracking-overview` returns:

| Field | Meaning |
|---|---|
| `summary.by_kind` | Counts and latest note per tracking kind |
| `notes` | Recent tracking notes with `kind`, preview, folder tags, and optional checklist progress |
| `kinds` | The active kind filter, or all supported kinds when omitted |

Use it when the user asks for a centralized view of cleanup reports, cleanup tasks, or topic task notes before drilling into one note with `/notes/get`.

Use `/notes/tracking-status` when the user wants those same tracking notes grouped by execution state instead of only seeing a recent list:

```json
{"api_name":"/notes/tracking-status","kinds":["cleanup-task","topic-task"],"status":"pending","count":10,"stale_days":30,"skill_version":"1.0.0"}
```

`/notes/tracking-status` returns:

| Field | Meaning |
|---|---|
| `summary.by_status` | Counts for `pending`, `in_progress`, `done`, `needs_refresh`, and `snapshot` |
| `summary.focus_status` | The most urgent status bucket to look at first |
| `notes` | Tracking notes annotated with `status`, optional progress percentage, and stale flag |

Use it when the user asks “哪些整理任务还没做完”“哪些 tracking note 该刷新了” or wants a compact status board instead of a plain recent-notes view.

Use `/notes/tracking-dashboard` when the user wants one compact answer to “我现在先看什么”:

```json
{"api_name":"/notes/tracking-dashboard","kinds":["cleanup-task","topic-task","cleanup-report"],"count":5,"stale_days":30,"skill_version":"1.0.0"}
```

`/notes/tracking-dashboard` returns:

| Field | Meaning |
|---|---|
| `summary.focus_status` | The most urgent bucket to work from first |
| `top_priorities` | The first few tracking notes worth opening next, with reasons |
| `recently_updated` | A compact list of the newest tracking notes |
| `stale_notes` | Tracking notes that may need a refresh |
| `suggested_next_steps` | Short next-step guidance based on the current board |

Use it when the user wants a dashboard-like briefing instead of raw lists, for example “现在我应该先整理什么”“先看哪几篇 task note”.

Use `/notes/board-notes` when the user wants one list for saved `tracking-note`, `work-queue-note`, and `workboard-note` items:

```json
{"api_name":"/notes/board-notes","count":10,"stale_days":30,"include_archived":true,"skill_version":"1.0.0"}
```

`/notes/board-notes` returns:

| Field | Meaning |
|---|---|
| `summary.by_kind` | Counts for `tracking-note`, `work-queue-note`, and `workboard-note` |
| `summary.by_status` | Counts for `stale`, `active`, and `archived` saved board notes |
| `notes` | Saved board notes with `kind`, folder tags, stale flag, and archive flag |
| `suggested_next_steps` | Short guidance about refreshing stale board notes or reopening archived ones |

Use it when the user asks “我保存过哪些 dashboard/work queue/workboard note”“把这些 board notes 一起列出来” or wants one saved-note overview before refreshing or archiving them.

Use `/notes/board-notes-actions` when the user wants the next likely board-note maintenance actions spelled out as concrete API suggestions:

```json
{"api_name":"/notes/board-notes-actions","count":5,"stale_days":30,"include_archived":true,"skill_version":"1.0.0"}
```

Those actions can include `sync_stale_board_notes`, `unarchive_board_notes`, `refresh_active_board_notes`, and `save_board_notes_dashboard` when the reusable dashboard note does not exist yet.

Use `/notes/board-notes-dashboard` when the user wants the saved board notes summarized as a small action board instead of a plain list:

```json
{"api_name":"/notes/board-notes-dashboard","count":5,"stale_days":30,"include_archived":true,"skill_version":"1.0.0"}
```

`/notes/board-notes-dashboard` returns:

| Field | Meaning |
|---|---|
| `summary.focus_status` | The status bucket that deserves attention first |
| `top_priorities` | Saved board notes worth opening, syncing, or unarchiving next |
| `recently_updated` | The newest saved board notes across tracking/work queue/workboard |
| `stale_notes` | Saved board notes that are out of date |
| `archived_notes` | Saved board notes that are currently archived |
| `suggested_next_steps` | Short guidance about syncing stale notes or restoring archived ones |

Use `/notes/board-notes-preview` when the user wants one of those board-note actions expanded into a real preview before deciding to sync, unarchive, or save a dashboard note:

```json
{"api_name":"/notes/board-notes-preview","action_kind":"unarchive_board_notes","count":5,"stale_days":30,"include_archived":true,"skill_version":"1.0.0"}
```

Use `/notes/board-notes-runbook` when the user wants a small bundle of the top board-note actions plus their previews:

```json
{"api_name":"/notes/board-notes-runbook","count":5,"stale_days":30,"include_archived":true,"action_count":3,"skill_version":"1.0.0"}
```

When the user wants to actually run one of the write-capable board-note actions, preview `/notes/board-notes-apply` first:

```json
{"api_name":"/notes/board-notes-apply","action_kind":"unarchive_board_notes","count":5,"stale_days":30,"include_archived":true,"dry_run":true,"skill_version":"1.0.0"}
```

When the user wants several write-capable board-note actions applied together, preview `/notes/board-notes-batch-apply` first:

```json
{"api_name":"/notes/board-notes-batch-apply","count":5,"stale_days":30,"include_archived":true,"action_kinds":["unarchive_board_notes","sync_stale_board_notes"],"dry_run":true,"skill_version":"1.0.0"}
```

When the user wants that saved-board dashboard preserved as one reusable note, preview `/notes/board-notes-dashboard-note` first:

```json
{"api_name":"/notes/board-notes-dashboard-note","count":5,"stale_days":30,"include_archived":true,"title":"Board notes dashboard","tags":["整理"],"folder":"📁Summary","dry_run":true,"skill_version":"1.0.0"}
```

Then create it only after explicit intent:

```json
{"api_name":"/notes/board-notes-dashboard-note","count":5,"stale_days":30,"include_archived":true,"title":"Board notes dashboard","confirm_create":true,"skill_version":"1.0.0"}
```

When the user already has that saved board-notes dashboard note and wants it refreshed from the latest state, preview `/notes/board-notes-dashboard-note-refresh` first:

```json
{"api_name":"/notes/board-notes-dashboard-note-refresh","note_id":"abc123","count":5,"stale_days":30,"include_archived":true,"title":"Board notes dashboard refreshed","dry_run":true,"skill_version":"1.0.0"}
```

When the user wants to archive one of those saved dashboard notes, preview `/notes/board-notes-dashboard-note-archive` first:

```json
{"api_name":"/notes/board-notes-dashboard-note-archive","note_id":"abc123","dry_run":true,"skill_version":"1.0.0"}
```

When the user wants several saved dashboard notes refreshed to the same latest saved-board state, preview `/notes/board-notes-dashboard-note-sync` first:

```json
{"api_name":"/notes/board-notes-dashboard-note-sync","note_ids":["abc123","def456"],"count":5,"stale_days":30,"include_archived":true,"title":"Board notes dashboard synced","dry_run":true,"skill_version":"1.0.0"}
```

Use `/notes/saved-views` when the user wants one unified list across saved `tracking-note`, `work-queue-note`, `workboard-note`, `board-notes-dashboard-note`, `saved-views-report`, `saved-views-task`, and `saved-views-dashboard-note` items:

```json
{"api_name":"/notes/saved-views","count":12,"stale_days":30,"include_archived":true,"skill_version":"1.0.0"}
```

Use `/notes/saved-views-actions` when the user wants the next likely saved-view maintenance actions spelled out as concrete API suggestions:

```json
{"api_name":"/notes/saved-views-actions","count":5,"stale_days":30,"include_archived":true,"skill_version":"1.0.0"}
```

Those actions can now include sync/unarchive steps plus save-a-note suggestions such as `save_saved_views_report`, `save_saved_views_task`, and `save_saved_views_dashboard` when the corresponding reusable notes do not exist yet.

Use `/notes/saved-views-preview` when the user wants one of those saved-view actions expanded into a real preview before deciding to sync, unarchive, or save a report/task/dashboard note:

```json
{"api_name":"/notes/saved-views-preview","action_kind":"unarchive_saved_views","count":5,"stale_days":30,"include_archived":true,"skill_version":"1.0.0"}
```

Use `/notes/saved-views-runbook` when the user wants a small bundle of the top saved-view actions plus their previews:

```json
{"api_name":"/notes/saved-views-runbook","count":5,"stale_days":30,"include_archived":true,"action_count":3,"skill_version":"1.0.0"}
```

When the user wants to actually run one of the write-capable saved-view actions, preview `/notes/saved-views-apply` first:

```json
{"api_name":"/notes/saved-views-apply","action_kind":"unarchive_saved_views","count":5,"stale_days":30,"include_archived":true,"dry_run":true,"skill_version":"1.0.0"}
```

When the user wants several write-capable saved-view actions applied together, preview `/notes/saved-views-batch-apply` first:

```json
{"api_name":"/notes/saved-views-batch-apply","count":5,"stale_days":30,"include_archived":true,"action_count":3,"dry_run":true,"skill_version":"1.0.0"}
```

When the user wants a durable receipt of what the saved-view batch actually did, preview `/notes/saved-views-report` first:

```json
{"api_name":"/notes/saved-views-report","count":5,"stale_days":30,"include_archived":true,"action_count":3,"title":"Saved views report","tags":["整理"],"folder":"📁Summary","dry_run":true,"skill_version":"1.0.0"}
```

When the user already has a saved-views report note and wants it refreshed from the latest saved-view batch result, preview `/notes/saved-views-report-refresh` first:

```json
{"api_name":"/notes/saved-views-report-refresh","note_id":"abc123","count":5,"stale_days":30,"include_archived":true,"action_kinds":["unarchive_saved_views"],"title":"Saved views report refreshed","dry_run":true,"skill_version":"1.0.0"}
```

When the user wants to archive one of those saved-views report notes, preview `/notes/saved-views-report-archive` first:

```json
{"api_name":"/notes/saved-views-report-archive","note_id":"abc123","dry_run":true,"skill_version":"1.0.0"}
```

When the user wants several saved-views report notes refreshed to the same latest saved-view batch result, preview `/notes/saved-views-report-sync` first:

```json
{"api_name":"/notes/saved-views-report-sync","note_ids":["abc123","def456"],"count":5,"stale_days":30,"include_archived":true,"action_kinds":["unarchive_saved_views"],"title":"Saved views report synced","dry_run":true,"skill_version":"1.0.0"}
```

When the user wants the saved-view work tracked as a checklist-style task note rather than only a report, preview `/notes/saved-views-task` first:

```json
{"api_name":"/notes/saved-views-task","count":5,"stale_days":30,"include_archived":true,"action_count":3,"title":"Saved views task","tags":["整理"],"folder":"📁Summary","dry_run":true,"skill_version":"1.0.0"}
```

When the user already has a saved-views task note and wants it refreshed with the latest saved-view plan and execution state, preview `/notes/saved-views-task-refresh` first:

```json
{"api_name":"/notes/saved-views-task-refresh","note_id":"abc123","count":5,"stale_days":30,"include_archived":true,"action_kinds":["unarchive_saved_views"],"title":"Saved views task refreshed","dry_run":true,"skill_version":"1.0.0"}
```

When the user wants to archive one of those saved-views task notes, preview `/notes/saved-views-task-archive` first:

```json
{"api_name":"/notes/saved-views-task-archive","note_id":"abc123","dry_run":true,"skill_version":"1.0.0"}
```

When the user wants several saved-views task notes refreshed to the same latest saved-view plan and status, preview `/notes/saved-views-task-sync` first:

```json
{"api_name":"/notes/saved-views-task-sync","note_ids":["abc123","def456"],"count":5,"stale_days":30,"include_archived":true,"action_kinds":["unarchive_saved_views"],"title":"Saved views task synced","dry_run":true,"skill_version":"1.0.0"}
```

Use `/notes/saved-views-dashboard` when the user wants those saved views summarized as a small action board instead of a flat list:

```json
{"api_name":"/notes/saved-views-dashboard","count":5,"stale_days":30,"include_archived":true,"skill_version":"1.0.0"}
```

When the user wants that saved-views dashboard preserved as one reusable note, preview `/notes/saved-views-dashboard-note` first:

```json
{"api_name":"/notes/saved-views-dashboard-note","count":5,"stale_days":30,"include_archived":true,"title":"Saved views dashboard","tags":["整理"],"folder":"📁Summary","dry_run":true,"skill_version":"1.0.0"}
```

When the user already has that saved-views dashboard note and wants it refreshed from the latest state, preview `/notes/saved-views-dashboard-note-refresh` first:

```json
{"api_name":"/notes/saved-views-dashboard-note-refresh","note_id":"abc123","count":5,"stale_days":30,"include_archived":true,"title":"Saved views dashboard refreshed","dry_run":true,"skill_version":"1.0.0"}
```

When the user wants to archive one of those saved-views dashboard notes, preview `/notes/saved-views-dashboard-note-archive` first:

```json
{"api_name":"/notes/saved-views-dashboard-note-archive","note_id":"abc123","dry_run":true,"skill_version":"1.0.0"}
```

When the user wants several saved-views dashboard notes refreshed to the same latest saved-view state, preview `/notes/saved-views-dashboard-note-sync` first:

```json
{"api_name":"/notes/saved-views-dashboard-note-sync","note_ids":["abc123","def456"],"count":5,"stale_days":30,"include_archived":true,"title":"Saved views dashboard synced","dry_run":true,"skill_version":"1.0.0"}
```

Use `/notes/saved-views-archive` when the user wants one archive or unarchive pass across all those saved-view note families:

```json
{"api_name":"/notes/saved-views-archive","note_ids":["note-1","note-2"],"archived":true,"dry_run":true,"skill_version":"1.0.0"}
```

Use `/notes/saved-views-sync` when the user wants one refresh pass across the same saved-view families:

```json
{"api_name":"/notes/saved-views-sync","kind":"workboard-note","count":5,"title":"Saved view synced","dry_run":true,"skill_version":"1.0.0"}
```

`/notes/saved-views-sync` and `/notes/saved-views-archive` accept `note_id` or `note_ids` directly, or they can select notes through the same `kind` / `kinds` / `query` / `count` filters used by `/notes/saved-views`.

Use `/notes/board-notes-archive` when the user wants one archive or unarchive pass across saved board notes without remembering each note family separately:

```json
{"api_name":"/notes/board-notes-archive","note_ids":["note-1","note-2"],"archived":true,"dry_run":true,"skill_version":"1.0.0"}
```

Use `/notes/board-notes-sync` when the user wants one refresh pass across saved `tracking-note`, `work-queue-note`, and `workboard-note` items:

```json
{"api_name":"/notes/board-notes-sync","kind":"workboard-note","count":5,"title":"Board note synced","dry_run":true,"skill_version":"1.0.0"}
```

`/notes/board-notes-sync` and `/notes/board-notes-archive` accept `note_id` or `note_ids` directly, or they can select notes through the same `kind` / `kinds` / `query` / `count` filters used by `/notes/board-notes`.

Use `/notes/work-queue` when the user wants one queue that mixes tracking priorities with cleanup action suggestions:

```json
{"api_name":"/notes/work-queue","count":8,"stale_days":30,"threshold":0.88,"skill_version":"1.0.0"}
```

`/notes/work-queue` returns:

| Field | Meaning |
|---|---|
| `queue` | A single ordered list mixing tracking items and proposed cleanup actions |
| `tracking_dashboard` | Compact context copied from the tracking dashboard layer |
| `cleanup_actions` | Compact context copied from the cleanup action planner |
| `suggested_next_steps` | Short guidance about what to open or preview first |

Use it when the user says “给我一个统一待办”“topic 和 cleanup 一起排一下先做什么”.

When the user wants that queue turned into concrete maintenance suggestions, call `/notes/work-queue-actions`:

```json
{"api_name":"/notes/work-queue-actions","count":8,"stale_days":30,"threshold":0.88,"skill_version":"1.0.0"}
```

`/notes/work-queue-actions` returns:

| Field | Meaning |
|---|---|
| `summary` | High-level counts for saved work queue notes, stale/archived note counts, queue size, and current focus status |
| `actions[]` | Ordered action suggestions such as syncing stale work queue notes, unarchiving old ones, refreshing active ones, or saving the current queue as a reusable note |
| `work_queue` | The underlying unified work queue payload that produced those actions |

Use it when the user asks “这个队列下一步该怎么处理”“把 work queue 变成动作建议”.

When the user wants one of those queue actions expanded into the real downstream dry-run, call `/notes/work-queue-preview`:

```json
{"api_name":"/notes/work-queue-preview","action_kind":"save_work_queue_note","count":8,"stale_days":30,"threshold":0.88,"skill_version":"1.0.0"}
```

When they want the first few queue actions packaged together with their previews, call `/notes/work-queue-runbook`:

```json
{"api_name":"/notes/work-queue-runbook","count":8,"stale_days":30,"threshold":0.88,"action_count":3,"skill_version":"1.0.0"}
```

When they want one suggested queue action executed after review, call `/notes/work-queue-apply`:

```json
{"api_name":"/notes/work-queue-apply","action_kind":"save_work_queue_note","count":8,"stale_days":30,"threshold":0.88,"dry_run":true,"skill_version":"1.0.0"}
```

Set `confirm_apply:true` to execute the underlying write-capable action.

When they want several write-capable queue actions executed together after review, call `/notes/work-queue-batch-apply`:

```json
{"api_name":"/notes/work-queue-batch-apply","action_kinds":["save_work_queue_note"],"count":8,"stale_days":30,"threshold":0.88,"dry_run":true,"skill_version":"1.0.0"}
```

Set `confirm_apply:true` to execute the selected downstream queue note actions.

Use `/notes/workboard` when the user wants that same unified queue plus recent saved tracking/work-queue notes in one response:

```json
{"api_name":"/notes/workboard","count":8,"note_count":5,"stale_days":30,"threshold":0.88,"skill_version":"1.0.0"}
```

`/notes/workboard` returns:

| Field | Meaning |
|---|---|
| `summary` | Combined counts for tracking notes, saved work queue notes, queue items, and the top priority source |
| `tracking_dashboard` | The same detailed tracking dashboard payload |
| `work_queue` | The same ordered unified queue payload |
| `saved_notes.tracking_notes` | Recent tracking notes that belong to the active board context |
| `saved_notes.work_queue_notes` | Recent saved work queue notes, optionally excluding archived ones |
| `saved_notes.workboard_notes` | Recent saved workboard notes, optionally excluding archived ones |
| `suggested_next_steps` | Short next-step guidance synthesized across queue state and saved notes |

Use it when the user asks “给我一个统一工作台”“把 tracking note 和 work queue note 一起看” or wants one response that includes both active priorities and the notes already saved from them.

When the user wants the workboard turned into a concrete maintenance queue, call `/notes/workboard-actions`:

```json
{"api_name":"/notes/workboard-actions","count":8,"note_count":5,"stale_days":30,"threshold":0.88,"skill_version":"1.0.0"}
```

`/notes/workboard-actions` returns:

| Field | Meaning |
| --- | --- |
| `summary` | The same top-level workboard summary so you can explain the current state |
| `actions[]` | Ordered action suggestions such as syncing stale workboard notes, syncing stale work queue notes, unarchiving older notes, or saving the current board/queue as reusable notes |
| `workboard` | The underlying unified workboard payload that produced those actions |

Use it when the user asks “这个工作台下一步该做什么”“帮我把 workboard 变成操作清单”.

When the user wants one of those workboard actions expanded into the real downstream dry-run, call `/notes/workboard-preview`:

```json
{"api_name":"/notes/workboard-preview","action_kind":"sync_stale_workboard_notes","count":8,"note_count":5,"stale_days":30,"threshold":0.88,"skill_version":"1.0.0"}
```

When they want the first few workboard actions packaged together with their previews, call `/notes/workboard-runbook`:

```json
{"api_name":"/notes/workboard-runbook","count":8,"note_count":5,"stale_days":30,"threshold":0.88,"action_count":3,"skill_version":"1.0.0"}
```

When they want one suggested workboard action executed after review, call `/notes/workboard-apply`:

```json
{"api_name":"/notes/workboard-apply","action_kind":"save_workboard_note","count":8,"note_count":5,"stale_days":30,"threshold":0.88,"dry_run":true,"skill_version":"1.0.0"}
```

Set `confirm_apply:true` to execute the underlying write-capable action.

When they want several write-capable workboard actions executed together after review, call `/notes/workboard-batch-apply`:

```json
{"api_name":"/notes/workboard-batch-apply","action_kinds":["save_workboard_note","save_work_queue_note"],"count":8,"note_count":5,"stale_days":30,"threshold":0.88,"dry_run":true,"skill_version":"1.0.0"}
```

Set `confirm_apply:true` to execute the selected downstream note sync/create/archive actions.

When the user wants that unified workboard saved as one reusable note, preview `/notes/workboard-note` first:

```json
{"api_name":"/notes/workboard-note","count":8,"note_count":5,"stale_days":30,"threshold":0.88,"title":"Workboard","tags":["整理"],"folder":"📁Summary","dry_run":true,"skill_version":"1.0.0"}
```

Then create it:

```json
{"api_name":"/notes/workboard-note","count":8,"note_count":5,"stale_days":30,"threshold":0.88,"title":"Workboard","confirm_create":true,"skill_version":"1.0.0"}
```

`/notes/workboard-note` returns:

| Field | Meaning |
|---|---|
| `preview.title` | The note title that will be created |
| `preview.tags` | Tags/folder attached to the saved workboard note |
| `preview.workboard` | The underlying unified workboard payload |
| `preview.content_preview` | The start of the generated workboard markdown |

Use it when the user wants one saved note that captures the current dashboard, queue, and recent saved-note context inside MindNotes.

When the user already has that saved workboard note and wants it refreshed from the latest board state, preview `/notes/workboard-note-refresh` first:

```json
{"api_name":"/notes/workboard-note-refresh","note_id":"abc123","count":8,"note_count":5,"stale_days":30,"threshold":0.88,"title":"Workboard refreshed","dry_run":true,"skill_version":"1.0.0"}
```

Then refresh it:

```json
{"api_name":"/notes/workboard-note-refresh","note_id":"abc123","count":8,"note_count":5,"stale_days":30,"threshold":0.88,"title":"Workboard refreshed","confirm_refresh":true,"expected_updated_at":"2026-05-26T12:00:00","skill_version":"1.0.0"}
```

`/notes/workboard-note-refresh` returns the usual `current_note`, `refreshed_note`, `changes`, and the latest `workboard` payload behind the refresh.

When the user wants to archive one of those saved workboard notes, preview `/notes/workboard-note-archive` first:

```json
{"api_name":"/notes/workboard-note-archive","note_id":"abc123","dry_run":true,"skill_version":"1.0.0"}
```

Then apply it:

```json
{"api_name":"/notes/workboard-note-archive","note_id":"abc123","confirm_archive":true,"skill_version":"1.0.0"}
```

Set `archived:false` to remove the archive tags again. The archive flow only changes tags; it does not delete content.

When the user wants several saved workboard notes refreshed to the same current board state, preview `/notes/workboard-note-sync` first:

```json
{"api_name":"/notes/workboard-note-sync","note_ids":["abc123","def456"],"count":8,"note_count":5,"stale_days":30,"threshold":0.88,"title":"Workboard synced","dry_run":true,"skill_version":"1.0.0"}
```

Then sync them:

```json
{"api_name":"/notes/workboard-note-sync","note_ids":["abc123","def456"],"count":8,"note_count":5,"stale_days":30,"threshold":0.88,"title":"Workboard synced","confirm_sync":true,"skill_version":"1.0.0"}
```

`/notes/workboard-note-sync` returns one preview or result per target note, including `current_note`, `refreshed_note`, `changes`, and the shared `workboard` payload behind the sync.

When the user wants that unified queue saved as one reusable note, preview `/notes/work-queue-note` first:

```json
{"api_name":"/notes/work-queue-note","count":8,"stale_days":30,"threshold":0.88,"title":"Work queue","tags":["整理"],"folder":"📁Summary","dry_run":true,"skill_version":"1.0.0"}
```

Then create it:

```json
{"api_name":"/notes/work-queue-note","count":8,"stale_days":30,"threshold":0.88,"title":"Work queue","confirm_create":true,"skill_version":"1.0.0"}
```

`/notes/work-queue-note` returns:

| Field | Meaning |
|---|---|
| `preview.title` | The note title that will be created |
| `preview.tags` | Tags/folder attached to the saved work queue note |
| `preview.work_queue` | The underlying unified work queue payload |
| `preview.content_preview` | The start of the generated queue markdown |

Use it when the user wants one saved note that combines tracking priorities with cleanup action suggestions inside MindNotes.

When the user already has that saved work queue note and wants it refreshed from the latest queue state, preview `/notes/work-queue-note-refresh` first:

```json
{"api_name":"/notes/work-queue-note-refresh","note_id":"abc123","count":8,"stale_days":30,"threshold":0.88,"title":"Work queue refreshed","dry_run":true,"skill_version":"1.0.0"}
```

Then refresh it:

```json
{"api_name":"/notes/work-queue-note-refresh","note_id":"abc123","count":8,"stale_days":30,"threshold":0.88,"title":"Work queue refreshed","confirm_refresh":true,"expected_updated_at":"2026-05-26T12:00:00","skill_version":"1.0.0"}
```

`/notes/work-queue-note-refresh` returns the usual `current_note`, `refreshed_note`, `changes`, and the latest `work_queue` payload behind the refresh.

When the user wants to archive one of those saved work queue notes, preview `/notes/work-queue-note-archive` first:

```json
{"api_name":"/notes/work-queue-note-archive","note_id":"abc123","dry_run":true,"skill_version":"1.0.0"}
```

Then apply it:

```json
{"api_name":"/notes/work-queue-note-archive","note_id":"abc123","confirm_archive":true,"skill_version":"1.0.0"}
```

Set `archived:false` to remove the archive tags again. The archive flow only changes tags; it does not delete content.

When the user wants several saved work queue notes refreshed to the same current queue state, preview `/notes/work-queue-note-sync` first:

```json
{"api_name":"/notes/work-queue-note-sync","note_ids":["abc123","def456"],"count":8,"stale_days":30,"threshold":0.88,"title":"Work queue synced","dry_run":true,"skill_version":"1.0.0"}
```

Then sync them:

```json
{"api_name":"/notes/work-queue-note-sync","note_ids":["abc123","def456"],"count":8,"stale_days":30,"threshold":0.88,"title":"Work queue synced","confirm_sync":true,"skill_version":"1.0.0"}
```

`/notes/work-queue-note-sync` returns one preview or result per target note, including `current_note`, `refreshed_note`, `changes`, and the shared `work_queue` payload behind the sync.

Use `/notes/tracking-actions` when the user wants the next likely tracking maintenance actions spelled out as concrete API suggestions:

```json
{"api_name":"/notes/tracking-actions","kinds":["cleanup-task","topic-task","cleanup-report"],"count":5,"stale_days":30,"skill_version":"1.0.0"}
```

Those actions can include `sync_stale_tracking_notes`, `archive_done_tracking_notes`, `refresh_active_tracking_notes`, and `save_tracking_dashboard` when a reusable tracking note does not exist yet.

Use `/notes/tracking-preview` when the user wants one of those tracking actions expanded into a real preview before deciding to sync, archive, or save a tracking dashboard note:

```json
{"api_name":"/notes/tracking-preview","action_kind":"archive_done_tracking_notes","kinds":["cleanup-task","topic-task","cleanup-report"],"count":5,"stale_days":30,"skill_version":"1.0.0"}
```

Use `/notes/tracking-runbook` when the user wants a small bundle of the top tracking actions plus their previews:

```json
{"api_name":"/notes/tracking-runbook","kinds":["cleanup-task","topic-task","cleanup-report"],"count":5,"stale_days":30,"action_count":3,"skill_version":"1.0.0"}
```

When the user wants to actually run one of the write-capable tracking actions, preview `/notes/tracking-apply` first:

```json
{"api_name":"/notes/tracking-apply","action_kind":"archive_done_tracking_notes","kinds":["cleanup-task","topic-task","cleanup-report"],"count":5,"stale_days":30,"dry_run":true,"skill_version":"1.0.0"}
```

When the user wants several write-capable tracking actions applied together, preview `/notes/tracking-batch-apply` first:

```json
{"api_name":"/notes/tracking-batch-apply","kinds":["cleanup-task","topic-task","cleanup-report"],"count":5,"stale_days":30,"action_kinds":["archive_done_tracking_notes","sync_stale_tracking_notes"],"dry_run":true,"skill_version":"1.0.0"}
```

When the user wants that dashboard briefing saved as one reusable note, preview `/notes/tracking-note` first:

```json
{"api_name":"/notes/tracking-note","kinds":["cleanup-task","topic-task","cleanup-report"],"count":5,"stale_days":30,"title":"Tracking dashboard","tags":["整理"],"folder":"📁Summary","dry_run":true,"skill_version":"1.0.0"}
```

Then create it:

```json
{"api_name":"/notes/tracking-note","kinds":["cleanup-task","topic-task","cleanup-report"],"count":5,"stale_days":30,"title":"Tracking dashboard","confirm_create":true,"skill_version":"1.0.0"}
```

`/notes/tracking-note` returns:

| Field | Meaning |
|---|---|
| `preview.title` | The note title that will be created |
| `preview.tags` | Tags/folder attached to the saved tracking note |
| `preview.dashboard` | The underlying tracking dashboard payload |
| `preview.content_preview` | The start of the generated dashboard markdown |

Use it when the user wants one note that captures the current tracking board inside MindNotes.

When the user already has that saved tracking note and wants it refreshed from the latest dashboard state, preview `/notes/tracking-note-refresh` first:

```json
{"api_name":"/notes/tracking-note-refresh","note_id":"abc123","kinds":["cleanup-task","topic-task","cleanup-report"],"count":5,"stale_days":30,"title":"Tracking dashboard refreshed","dry_run":true,"skill_version":"1.0.0"}
```

Then refresh it:

```json
{"api_name":"/notes/tracking-note-refresh","note_id":"abc123","kinds":["cleanup-task","topic-task","cleanup-report"],"count":5,"stale_days":30,"title":"Tracking dashboard refreshed","confirm_refresh":true,"expected_updated_at":"2026-05-26T12:00:00","skill_version":"1.0.0"}
```

`/notes/tracking-note-refresh` returns the usual `current_note`, `refreshed_note`, `changes`, and the latest `dashboard` payload behind the refresh.

When the user wants to archive one of those saved tracking notes, preview `/notes/tracking-note-archive` first:

```json
{"api_name":"/notes/tracking-note-archive","note_id":"abc123","dry_run":true,"skill_version":"1.0.0"}
```

Then apply it:

```json
{"api_name":"/notes/tracking-note-archive","note_id":"abc123","confirm_archive":true,"skill_version":"1.0.0"}
```

Set `archived:false` to remove the archive tags again. The archive flow only changes tags; it does not delete content.

When the user wants several saved tracking notes refreshed to the same current dashboard state, preview `/notes/tracking-note-sync` first:

```json
{"api_name":"/notes/tracking-note-sync","note_ids":["abc123","def456"],"count":5,"stale_days":30,"title":"Tracking dashboard synced","dry_run":true,"skill_version":"1.0.0"}
```

Then sync them:

```json
{"api_name":"/notes/tracking-note-sync","note_ids":["abc123","def456"],"count":5,"stale_days":30,"title":"Tracking dashboard synced","confirm_sync":true,"skill_version":"1.0.0"}
```

`/notes/tracking-note-sync` returns one preview or result per target note, including `current_note`, `refreshed_note`, `changes`, and the shared `dashboard` payload behind the sync.

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

Use `/notes/cleanup-plan` when the user wants to know what to organize next across the note base:

```json
{"api_name":"/notes/cleanup-plan","count":8,"stale_days":90,"skill_version":"1.0.0"}
```

`/notes/cleanup-plan` returns:

| Field | Meaning |
|---|---|
| `duplicate_candidates` | Small groups worth reviewing with `/graph/duplicates` or `/graph/merge-preview` |
| `inbox_notes` | Notes still sitting in `📁Inbox` |
| `no_folder_notes` | Notes missing any folder tag |
| `no_tags_notes` | Notes with no tags at all |
| `orphan_notes` | Notes whose tags do not connect to any other note |
| `stale_notes` | Notes not updated within the requested stale window |
| `long_notes` | Long notes that may benefit from topic summaries or batch rewrites |
| `suggested_actions` | Ordered next steps that map to other Skill APIs |

Use it as a planning layer before choosing batch updates, batch rewrites, merge previews, or topic summaries.

Use `/notes/cleanup-actions` when the user wants that plan translated into concrete next API calls:

```json
{"api_name":"/notes/cleanup-actions","count":8,"stale_days":90,"skill_version":"1.0.0"}
```

`/notes/cleanup-actions` returns:

| Field | Meaning |
|---|---|
| `actions` | Ordered cleanup moves mapped to existing APIs |
| `api_name` | The next API to call for that move |
| `note_ids` | The notes that action should target |
| `params` | A ready-to-review starter payload for the next API |

Use it when the user wants to move directly from diagnosis to execution planning without manually translating the cleanup plan.

Use `/notes/cleanup-preview` when the user wants one of those suggested actions expanded into an actual next-step preview:

```json
{"api_name":"/notes/cleanup-preview","count":8,"stale_days":90,"action_kind":"review_duplicates","skill_version":"1.0.0"}
```

`/notes/cleanup-preview` returns:

| Field | Meaning |
|---|---|
| `action` | The selected cleanup action from `/notes/cleanup-actions` |
| `preview_api_name` | Which downstream API was previewed |
| `preview` | The actual preview payload from that downstream API |

Use it to go from “what should I do next” to “show me that exact dry-run or preview now”.

Use `/notes/cleanup-runbook` when the user wants a small bundle of the top cleanup actions plus their previews:

```json
{"api_name":"/notes/cleanup-runbook","count":8,"stale_days":90,"action_count":3,"skill_version":"1.0.0"}
```

`/notes/cleanup-runbook` returns:

| Field | Meaning |
|---|---|
| `actions` | The top cleanup actions selected from `/notes/cleanup-actions` |
| `previews` | Preview payloads for those selected actions |
| `skipped` | Actions whose previews could not be expanded |

Use it when the user wants a compact “show me the first few cleanup moves with previews” bundle instead of stepping through actions one by one.

When the user wants that cleanup runbook saved as a note, preview `/notes/cleanup-note` first:

```json
{"api_name":"/notes/cleanup-note","count":8,"stale_days":90,"action_count":3,"title":"Cleanup runbook","tags":["整理"],"folder":"📁Summary","dry_run":true,"skill_version":"1.0.0"}
```

Then create it only after approval:

```json
{"api_name":"/notes/cleanup-note","count":8,"stale_days":90,"action_count":3,"title":"Cleanup runbook","confirm_create":true,"skill_version":"1.0.0"}
```

`/notes/cleanup-note` returns:

| Field | Meaning |
|---|---|
| `preview.title` | The note title that will be created |
| `preview.tags` | Tags/folder to be attached to the runbook note |
| `preview.content_preview` | The beginning of the generated runbook markdown |
| `preview.runbook` | The full structured runbook that the markdown was built from |

Use it when the user wants a cleanup plan to become a durable task or planning note inside MindNotes.

When the user wants to actually run one of the write-capable cleanup actions, preview `/notes/cleanup-apply` first:

```json
{"api_name":"/notes/cleanup-apply","count":8,"stale_days":90,"action_kind":"organize_folders","dry_run":true,"skill_version":"1.0.0"}
```

Then execute it only after approval:

```json
{"api_name":"/notes/cleanup-apply","count":8,"stale_days":90,"action_kind":"organize_folders","confirm_apply":true,"skill_version":"1.0.0"}
```

`/notes/cleanup-apply` returns:

| Field | Meaning |
|---|---|
| `action` | The selected cleanup action from `/notes/cleanup-actions` |
| `executed_api_name` | The underlying write API that will run |
| `result` | The underlying batch update or batch rewrite result |
| `apply_requirement` | Present during dry-run mode to remind the caller to set `confirm_apply:true` |

Use it when the user wants the cleanup pipeline to continue from suggestion into a real write action without manually translating parameters.

When the user wants several write-capable cleanup actions applied together, preview `/notes/cleanup-batch-apply` first:

```json
{"api_name":"/notes/cleanup-batch-apply","count":8,"stale_days":90,"action_count":3,"dry_run":true,"skill_version":"1.0.0"}
```

Or target specific write-capable action kinds:

```json
{"api_name":"/notes/cleanup-batch-apply","count":8,"stale_days":90,"action_kinds":["organize_folders","refresh_stale_notes"],"confirm_apply":true,"skill_version":"1.0.0"}
```

`/notes/cleanup-batch-apply` returns:

| Field | Meaning |
|---|---|
| `selected_count` | How many cleanup actions were selected for this batch |
| `executed` | Successful write-capable cleanup executions |
| `skipped` | Actions skipped because they were preview-only or unsupported for direct execution |
| `apply_requirement` | Present during dry-run mode to remind the caller to set `confirm_apply:true` |

Use it when the user wants cleanup to move from a runbook into a small confirmed batch of real organizing actions.

When the user wants a durable receipt of what the cleanup batch actually did, preview `/notes/cleanup-report` first:

```json
{"api_name":"/notes/cleanup-report","count":8,"stale_days":90,"action_count":3,"title":"Cleanup report","tags":["整理"],"folder":"📁Summary","dry_run":true,"skill_version":"1.0.0"}
```

Then execute and save it:

```json
{"api_name":"/notes/cleanup-report","count":8,"stale_days":90,"action_kinds":["organize_folders"],"title":"Cleanup report","confirm_apply":true,"confirm_create":true,"skill_version":"1.0.0"}
```

`/notes/cleanup-report` returns:

| Field | Meaning |
|---|---|
| `preview.report` | The cleanup batch execution payload used to build the receipt |
| `preview.content_preview` | The beginning of the generated execution-report markdown |
| `apply_requirement` | Present when the cleanup actions have not been executed yet |
| `create_requirement` | Present until `confirm_create:true` is supplied |

Use it when the user wants the cleanup pipeline to leave behind a human-readable execution report inside MindNotes.

When the user already has a cleanup report note and wants it refreshed from the latest cleanup batch result, preview `/notes/cleanup-report-refresh` first:

```json
{"api_name":"/notes/cleanup-report-refresh","note_id":"abc123","count":8,"stale_days":90,"action_kinds":["organize_folders"],"title":"Cleanup report refreshed","dry_run":true,"skill_version":"1.0.0"}
```

Then refresh it:

```json
{"api_name":"/notes/cleanup-report-refresh","note_id":"abc123","count":8,"stale_days":90,"action_kinds":["organize_folders"],"title":"Cleanup report refreshed","confirm_refresh":true,"expected_updated_at":"2026-05-26T12:00:00","skill_version":"1.0.0"}
```

`/notes/cleanup-report-refresh` returns:

| Field | Meaning |
|---|---|
| `current_note` | The existing cleanup report before refresh |
| `refreshed_note` | The proposed updated cleanup report note |
| `changes` | Title/content/tag differences between current and refreshed note |
| `report` | The latest cleanup batch execution payload backing the refreshed report |

Use it when the cleanup work is ongoing and the user wants one standing report note to stay current.

When the user wants the cleanup work tracked as a checklist-style task note rather than only a report, preview `/notes/cleanup-task` first:

```json
{"api_name":"/notes/cleanup-task","count":8,"stale_days":90,"action_count":3,"title":"Cleanup task","tags":["整理"],"folder":"📁Summary","dry_run":true,"skill_version":"1.0.0"}
```

Then create it:

```json
{"api_name":"/notes/cleanup-task","count":8,"stale_days":90,"action_kinds":["organize_folders"],"title":"Cleanup task","confirm_apply":true,"confirm_create":true,"skill_version":"1.0.0"}
```

`/notes/cleanup-task` returns:

| Field | Meaning |
|---|---|
| `preview.runbook` | The planned cleanup actions behind the checklist |
| `preview.report` | The execution status used to mark checklist progress |
| `preview.content_preview` | The beginning of the generated task markdown |
| `create_requirement` | Present until `confirm_create:true` is supplied |

Use it when the user wants one note that behaves more like an ongoing cleanup to-do list than a plain execution receipt.

When the user already has a cleanup task note and wants it refreshed with the latest cleanup plan and execution state, preview `/notes/cleanup-task-refresh` first:

```json
{"api_name":"/notes/cleanup-task-refresh","note_id":"abc123","count":8,"stale_days":90,"action_kinds":["organize_folders"],"title":"Cleanup task refreshed","dry_run":true,"skill_version":"1.0.0"}
```

Then refresh it:

```json
{"api_name":"/notes/cleanup-task-refresh","note_id":"abc123","count":8,"stale_days":90,"action_kinds":["organize_folders"],"title":"Cleanup task refreshed","confirm_refresh":true,"expected_updated_at":"2026-05-26T12:00:00","skill_version":"1.0.0"}
```

`/notes/cleanup-task-refresh` returns:

| Field | Meaning |
|---|---|
| `current_note` | The existing cleanup task note before refresh |
| `refreshed_note` | The proposed updated cleanup task note |
| `changes` | Title/content/tag differences between current and refreshed task note |
| `runbook` | The latest cleanup plan behind the checklist |
| `report` | The latest execution state behind the checklist |

Use it when the task note should remain the living checklist for ongoing cleanup work.

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

When the user wants a checklist-style topic task note rather than a plain summary note, preview `/notes/topic-task` first:

```json
{"api_name":"/notes/topic-task","query":"学习方法","topic":"学习方法","title":"学习方法任务","tags":["整理"],"folder":"📁Summary","dry_run":true,"skill_version":"1.0.0"}
```

Then create it:

```json
{"api_name":"/notes/topic-task","query":"学习方法","topic":"学习方法","title":"学习方法任务","confirm_create":true,"skill_version":"1.0.0"}
```

`/notes/topic-task` returns a note preview built from the same `draft_markdown`, `brief`, and `clusters`, but organized as a checklist with progress and next-step items.

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

When the user already has a topic task note and wants that checklist refreshed, preview `/notes/topic-task-refresh` first:

```json
{"api_name":"/notes/topic-task-refresh","note_id":"abc123","query":"学习方法","topic":"学习方法","title":"学习方法任务","dry_run":true,"skill_version":"1.0.0"}
```

Then refresh it:

```json
{"api_name":"/notes/topic-task-refresh","note_id":"abc123","query":"学习方法","topic":"学习方法","confirm_refresh":true,"expected_updated_at":"2026-05-23T12:00:00","skill_version":"1.0.0"}
```

`/notes/topic-task-refresh` returns the same kind of `current_note`, `refreshed_note`, `changes`, `draft_markdown`, `brief`, and `clusters` preview, but updates the checklist-style task note instead of the summary note.

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

When the user wants one checklist-style task note per cluster instead of one summary note per cluster, preview `/notes/topic-cluster-tasks` first:

```json
{"api_name":"/notes/topic-cluster-tasks","query":"学习方法","topic":"学习方法","title_prefix":"学习方法任务","folder":"📁Summary","dry_run":true,"skill_version":"1.0.0"}
```

Then create them:

```json
{"api_name":"/notes/topic-cluster-tasks","query":"学习方法","topic":"学习方法","title_prefix":"学习方法任务","confirm_create":true,"skill_version":"1.0.0"}
```

`/notes/topic-cluster-tasks` returns:

| Field | Meaning |
|---|---|
| `previews` | One proposed task note per top cluster |
| `cluster_task_count` | How many cluster task notes will be created |
| `brief` | Each preview includes its own cluster-specific synthesis brief |
| `note_ids` | The source notes used for that cluster task |

Use it when the topic is big enough that each cluster should become its own tracked checklist instead of a plain summary note.

When the user already has cluster task notes and wants to sync them with the latest topic state, preview `/notes/topic-cluster-task-sync` first:

```json
{"api_name":"/notes/topic-cluster-task-sync","query":"学习方法","topic":"学习方法","title_prefix":"学习方法任务","tags":["同步"],"dry_run":true,"skill_version":"1.0.0"}
```

Then apply only after approval:

```json
{"api_name":"/notes/topic-cluster-task-sync","query":"学习方法","topic":"学习方法","title_prefix":"学习方法任务","confirm_sync":true,"expected_updated_at":"2026-05-23T12:00:00","skill_version":"1.0.0"}
```

`/notes/topic-cluster-task-sync` previews one result per cluster task:

| Field | Meaning |
|---|---|
| `action` | `update`, `create`, or `skip` for that cluster task |
| `existing_note` | Present when the cluster already has a matching task note |
| `proposed_note` | The task note state that would be written or created |
| `changes` | Field-level differences for update actions |

Use it when cluster tasks already exist and should stay aligned with the latest topic structure without manually refreshing each one.

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
