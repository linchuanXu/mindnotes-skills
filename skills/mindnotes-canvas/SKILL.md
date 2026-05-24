---
name: mindnotes-canvas
description: Work with MindNotes Canvas knowledge maps through a user's authorized MindNotes account. Use when the user asks to list canvases, read a knowledge map, import notes into a canvas, suggest relationships, connect notes, accept relation suggestions, or organize notes visually.
---

# MindNotes Canvas

Use this skill for MindNotes knowledge maps. Use it when the user wants notes organized into a graph or wants help connecting ideas.

## Connection

Read the API Key from `MINDNOTES_API_KEY`. The key starts with `mn_sk_`.

Use `https://app.mindnotes.cn` by default. Use `MINDNOTES_BASE_URL` only when the user explicitly says self-hosted, staging, or test deployment.

If the Key is missing, give one concise setup instruction:

- Windows PowerShell: `setx MINDNOTES_API_KEY "mn_sk_..."`
- macOS/Linux: `export MINDNOTES_API_KEY="mn_sk_..."`

Tell the user to restart the AI client. Do not list alternative secret stores, config files, shell profiles, or multiple setup methods unless asked.

Call `POST /api/agent/gateway` with `Authorization: Bearer $MINDNOTES_API_KEY`. Put all fields at the top level and always include `"skill_version":"1.0.0"`.

If a response contains `upgrade_info`, stop and tell the user to update/reinstall MindNotes Skill before continuing.

## Concepts

| Term | Meaning |
|---|---|
| Canvas | A knowledge map owned by the authorized user |
| Node | A card on the map; may reference a note or a free concept |
| Edge | A saved relation between two nodes |
| Suggestion | A proposed relation; not saved until accepted |

## Read Flow

1. Call `/canvas/list`.
2. If needed, ask the user to choose a canvas.
3. Call `/canvas/get` with `canvas_id`.
4. Summarize nodes, edges, gaps, and possible next connections.

## Write Flow

1. Resolve note ids with search or user-provided ids.
2. Call `/canvas/import-notes` only when the user asks to add notes.
3. Call `/canvas/suggest-relations` to propose links.
4. Show suggestions to the user.
5. Call `/canvas/accept-relations` only after user approval.

## APIs

```json
{"api_name":"/canvas/list","skill_version":"1.0.0"}
```

```json
{"api_name":"/canvas/get","canvas_id":"canvas-1","skill_version":"1.0.0"}
```

```json
{"api_name":"/canvas/import-notes","canvas_id":"canvas-1","note_ids":["abc123"],"skill_version":"1.0.0"}
```

```json
{"api_name":"/canvas/suggest-relations","canvas_id":"canvas-1","skill_version":"1.0.0"}
```

```json
{"api_name":"/canvas/accept-relations","canvas_id":"canvas-1","suggestions":[{"source_node_id":"node-1","target_node_id":"node-2","type":"related"}],"skill_version":"1.0.0"}
```

Writes require `canvas:write`; reads require `canvas:read`. If `canvas:write` is missing, offer read-only analysis.
