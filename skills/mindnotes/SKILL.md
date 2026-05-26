---
name: mindnotes
description: "MindNotes/my notes: search, summarize, edit, review/抖卡, cards/制卡, Canvas/知识图谱, 物卡, stats, export."
---

# MindNotes

Use this skill to work with the user's own MindNotes knowledge base. Speak about "your notes", "your cards", and "MindNotes"; do not mention databases, SQLite, storage paths, or other users.

## Capability Map

| User intent | Primary workflow | Main APIs |
|---|---|---|
| Search my notes or answer from my notes | Search, hybrid-search, or find similar notes, then read details before making claims | `/notes/search`, `/notes/hybrid-search`, `/notes/similar`, `/notes/get` |
| Explain what changed in a note | Read the note history, then summarize changed fields and previews | `/notes/history`, `/notes/get` |
| Review my cleanup/topic trackers | Summarize cleanup reports, cleanup tasks, topic summary notes, topic task notes, and cluster-level topic notes/tasks in one place, sort them by pending/done/snapshot status, open a small priority dashboard, turn those tracking notes into one unified action list, preview/apply/runbook flow, capture tracking execution as a report or checklist task note, merge them with cleanup actions into one work queue, turn that work queue into one unified action list, preview/apply/runbook flow, save the work queue as a reusable note or capture its execution as a report or checklist task note, open one unified workboard that includes recent saved notes, turn that workboard into one unified action list, preview/apply/runbook flow, save the workboard as a reusable note, capture workboard execution as a report or checklist task note, inspect all saved board notes in one overview or one saved-board dashboard, turn those board notes into one unified action list, preview/apply/runbook flow, save that dashboard as a reusable note, capture board-note execution as a report or checklist task note, or step back and manage all saved views through one unified saved-views overview, action list, preview bridge, runbook, apply bridge, report note, report note archive/sync, task note, task note archive/sync, saved-views dashboard, reusable saved-views dashboard note, and archive/sync bridge | `/notes/tracking-overview`, `/notes/tracking-status`, `/notes/tracking-dashboard`, `/notes/tracking-actions`, `/notes/tracking-preview`, `/notes/tracking-runbook`, `/notes/tracking-apply`, `/notes/tracking-batch-apply`, `/notes/tracking-report`, `/notes/tracking-report-refresh`, `/notes/tracking-report-archive`, `/notes/tracking-report-sync`, `/notes/tracking-task`, `/notes/tracking-task-refresh`, `/notes/tracking-task-archive`, `/notes/tracking-task-sync`, `/notes/board-notes`, `/notes/board-notes-actions`, `/notes/board-notes-preview`, `/notes/board-notes-runbook`, `/notes/board-notes-apply`, `/notes/board-notes-batch-apply`, `/notes/board-notes-dashboard`, `/notes/board-notes-dashboard-note`, `/notes/board-notes-dashboard-note-refresh`, `/notes/board-notes-dashboard-note-archive`, `/notes/board-notes-dashboard-note-sync`, `/notes/board-notes-report`, `/notes/board-notes-report-refresh`, `/notes/board-notes-report-archive`, `/notes/board-notes-report-sync`, `/notes/board-notes-task`, `/notes/board-notes-task-refresh`, `/notes/board-notes-task-archive`, `/notes/board-notes-task-sync`, `/notes/board-notes-archive`, `/notes/board-notes-sync`, `/notes/saved-views`, `/notes/saved-views-actions`, `/notes/saved-views-preview`, `/notes/saved-views-runbook`, `/notes/saved-views-apply`, `/notes/saved-views-batch-apply`, `/notes/saved-views-report`, `/notes/saved-views-report-refresh`, `/notes/saved-views-report-archive`, `/notes/saved-views-report-sync`, `/notes/saved-views-task`, `/notes/saved-views-task-refresh`, `/notes/saved-views-task-archive`, `/notes/saved-views-task-sync`, `/notes/saved-views-dashboard`, `/notes/saved-views-dashboard-note`, `/notes/saved-views-dashboard-note-refresh`, `/notes/saved-views-dashboard-note-archive`, `/notes/saved-views-dashboard-note-sync`, `/notes/saved-views-archive`, `/notes/saved-views-sync`, `/notes/work-queue`, `/notes/work-queue-actions`, `/notes/work-queue-preview`, `/notes/work-queue-runbook`, `/notes/work-queue-apply`, `/notes/work-queue-batch-apply`, `/notes/work-queue-report`, `/notes/work-queue-report-refresh`, `/notes/work-queue-report-archive`, `/notes/work-queue-report-sync`, `/notes/work-queue-task`, `/notes/work-queue-task-refresh`, `/notes/work-queue-task-archive`, `/notes/work-queue-task-sync`, `/notes/workboard`, `/notes/workboard-actions`, `/notes/workboard-preview`, `/notes/workboard-runbook`, `/notes/workboard-apply`, `/notes/workboard-batch-apply`, `/notes/workboard-report`, `/notes/workboard-report-refresh`, `/notes/workboard-report-archive`, `/notes/workboard-report-sync`, `/notes/workboard-task`, `/notes/workboard-task-refresh`, `/notes/workboard-task-archive`, `/notes/workboard-task-sync`, `/notes/workboard-note`, `/notes/workboard-note-refresh`, `/notes/workboard-note-archive`, `/notes/workboard-note-sync`, `/notes/work-queue-note`, `/notes/work-queue-note-refresh`, `/notes/work-queue-note-archive`, `/notes/work-queue-note-sync`, `/notes/tracking-note`, `/notes/tracking-note-refresh`, `/notes/tracking-note-archive`, `/notes/tracking-note-sync`, `/notes/get` |
| Summarize a topic from my notes | Collect enough notes, group by themes, generate a topic brief, split notes into subtopic clusters, preview a summary draft, turn the current topic state into one unified action list with preview/apply/runbook flow, capture that action execution as a reusable report note, prepare a topic note for saving, archive or sync a saved topic note, create a checklist-style topic task note, refresh/archive/sync an existing topic task note, batch-create one note or one task per cluster, archive one saved cluster note or cluster task note, or sync cluster notes and cluster tasks with the latest source material | `/notes/search`, `/notes/get`, `/notes/collect`, `/notes/topic-brief`, `/notes/topic-clusters`, `/notes/topic-draft`, `/notes/topic-actions`, `/notes/topic-preview`, `/notes/topic-runbook`, `/notes/topic-apply`, `/notes/topic-batch-apply`, `/notes/topic-report`, `/notes/topic-report-refresh`, `/notes/topic-report-archive`, `/notes/topic-report-sync`, `/notes/topic-note`, `/notes/topic-note-archive`, `/notes/topic-note-sync`, `/notes/topic-task`, `/notes/topic-task-archive`, `/notes/topic-refresh`, `/notes/topic-task-refresh`, `/notes/topic-task-sync`, `/notes/topic-cluster-notes`, `/notes/topic-cluster-note-archive`, `/notes/topic-cluster-tasks`, `/notes/topic-cluster-task-archive`, `/notes/topic-cluster-sync`, `/notes/topic-cluster-task-sync` |
| Show recent notes or tags | List recent activity or tag distribution | `/notes/recent`, `/notes/tags`, `/notes/by-tag` |
| Create, edit, or delete a note | Upload image if needed, read exact existing note first, preview broad rewrites, then write only after clear intent | `/assets/upload-image`, `/notes/create`, `/notes/get`, `/notes/update`, `/notes/batch-rewrite`, `/notes/delete` |
| Restore a note version | Read history first, preview the rollback, then restore only after explicit approval | `/notes/history`, `/notes/restore-version` |
| Import external notes | Preview Markdown/JSON imports first, then create only after explicit approval | `/notes/import` |
| Organize folders or tags | Inspect folder tags, hierarchy, move one note, generate a cleanup plan, turn it into action suggestions, preview or apply the next cleanup step, batch-apply the first few write-capable cleanup actions, save a cleanup runbook, save a cleanup execution report, refresh, archive, or sync a cleanup report note, create a checklist-style cleanup task note, refresh, archive, or sync that task note, or review all tracking notes in one overview, status board, priority dashboard, tracking action list, tracking preview/apply/runbook flow, tracking report note, tracking task note, unified work queue, work-queue action list, work-queue preview/apply/runbook flow, unified work queue report note, unified work queue task note, unified workboard, workboard action list, workboard preview/apply/runbook flow, saved board-note overview, saved board-note action list, saved board-note preview/apply/runbook flow, saved board-note dashboard, saved board-note dashboard note, saved board-note report note, saved board-note task note, unified board-note archive/sync bridge, unified saved-view overview, saved-view action list, saved-view preview bridge, saved-view runbook, saved-view apply bridge, saved-view report note, saved-view report archive/sync, saved-view task note, saved-view task archive/sync, saved-view dashboard, saved-view dashboard note, saved workboard note, saved work queue note, saved tracking note, archived note, or batch-sync flow | `/folders/list`, `/folders/stats`, `/folders/relations`, `/folders/move-note`, `/notes/cleanup-plan`, `/notes/cleanup-actions`, `/notes/cleanup-preview`, `/notes/cleanup-runbook`, `/notes/cleanup-note`, `/notes/cleanup-apply`, `/notes/cleanup-batch-apply`, `/notes/cleanup-report`, `/notes/cleanup-report-refresh`, `/notes/cleanup-report-archive`, `/notes/cleanup-report-sync`, `/notes/cleanup-task`, `/notes/cleanup-task-refresh`, `/notes/cleanup-task-archive`, `/notes/cleanup-task-sync`, `/notes/tracking-overview`, `/notes/tracking-status`, `/notes/tracking-dashboard`, `/notes/tracking-actions`, `/notes/tracking-preview`, `/notes/tracking-runbook`, `/notes/tracking-apply`, `/notes/tracking-batch-apply`, `/notes/tracking-report`, `/notes/tracking-report-refresh`, `/notes/tracking-report-archive`, `/notes/tracking-report-sync`, `/notes/tracking-task`, `/notes/tracking-task-refresh`, `/notes/tracking-task-archive`, `/notes/tracking-task-sync`, `/notes/board-notes`, `/notes/board-notes-actions`, `/notes/board-notes-preview`, `/notes/board-notes-runbook`, `/notes/board-notes-apply`, `/notes/board-notes-batch-apply`, `/notes/board-notes-dashboard`, `/notes/board-notes-dashboard-note`, `/notes/board-notes-dashboard-note-refresh`, `/notes/board-notes-dashboard-note-archive`, `/notes/board-notes-dashboard-note-sync`, `/notes/board-notes-report`, `/notes/board-notes-report-refresh`, `/notes/board-notes-report-archive`, `/notes/board-notes-report-sync`, `/notes/board-notes-task`, `/notes/board-notes-task-refresh`, `/notes/board-notes-task-archive`, `/notes/board-notes-task-sync`, `/notes/board-notes-archive`, `/notes/board-notes-sync`, `/notes/saved-views`, `/notes/saved-views-actions`, `/notes/saved-views-preview`, `/notes/saved-views-runbook`, `/notes/saved-views-apply`, `/notes/saved-views-batch-apply`, `/notes/saved-views-report`, `/notes/saved-views-report-refresh`, `/notes/saved-views-report-archive`, `/notes/saved-views-report-sync`, `/notes/saved-views-task`, `/notes/saved-views-task-refresh`, `/notes/saved-views-task-archive`, `/notes/saved-views-task-sync`, `/notes/saved-views-dashboard`, `/notes/saved-views-dashboard-note`, `/notes/saved-views-dashboard-note-refresh`, `/notes/saved-views-dashboard-note-archive`, `/notes/saved-views-dashboard-note-sync`, `/notes/saved-views-archive`, `/notes/saved-views-sync`, `/notes/work-queue`, `/notes/work-queue-actions`, `/notes/work-queue-preview`, `/notes/work-queue-runbook`, `/notes/work-queue-apply`, `/notes/work-queue-batch-apply`, `/notes/work-queue-report`, `/notes/work-queue-report-refresh`, `/notes/work-queue-report-archive`, `/notes/work-queue-report-sync`, `/notes/work-queue-task`, `/notes/work-queue-task-refresh`, `/notes/work-queue-task-archive`, `/notes/work-queue-task-sync`, `/notes/workboard`, `/notes/workboard-actions`, `/notes/workboard-preview`, `/notes/workboard-runbook`, `/notes/workboard-apply`, `/notes/workboard-batch-apply`, `/notes/workboard-note`, `/notes/workboard-note-refresh`, `/notes/workboard-note-archive`, `/notes/workboard-note-sync`, `/notes/work-queue-note`, `/notes/work-queue-note-refresh`, `/notes/work-queue-note-archive`, `/notes/work-queue-note-sync`, `/notes/tracking-note`, `/notes/tracking-note-refresh`, `/notes/tracking-note-archive`, `/notes/tracking-note-sync`, `/notes/batch-update` |
| Review today / start TikCard | Show one due card, wait for self-rating, then submit | `/review/next`, `/review/submit` |
| Plan review pressure or dashboard | Summarize due, overdue, activity, and dashboard buckets | `/dashboard/summary`, `/dashboard/activity`, `/review/summary`, `/review/schedule`, `/stats/review` |
| Evaluate, create, or save cards | Evaluate candidate cards first when helpful, choose card maker, then create only on explicit intent | `/cards/evaluate`, `/cards/functions`, `/cards/create` |
| Find related, isolated, or duplicate ideas | Resolve source notes, explain relation cues, suggest duplicate candidates, and preview safe merge plans only | `/graph/related`, `/graph/duplicates`, `/graph/merge-preview`, `/graph/tag-relations`, `/graph/orphans` |
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
- Create/edit/delete/batch organize: use `/assets/upload-image` before attaching images; resolve exact existing notes with `/notes/get`; prefer `/notes/update` or `/notes/batch-update` with `dry_run:true` for non-trivial changes; never call delete APIs without explicit deletion confirmation.
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
- `references/notes.md`: note search, reading, collection, export, tags, and field meanings.
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
