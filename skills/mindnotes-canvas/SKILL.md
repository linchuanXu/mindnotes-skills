---
name: mindnotes-canvas
description: Work with MindNotes Canvas knowledge maps through a user's authorized MindNotes account. Use when the user asks to list canvases, read a knowledge map, import notes into a canvas, suggest relationships, connect notes, accept relation suggestions, or organize notes visually.
---

# MindNotes Canvas

Use this skill for MindNotes knowledge map and Canvas workflows. Use it when the user wants notes organized into a graph or wants help connecting ideas.

## Connection

Use `MINDNOTES_API_KEY` from the environment. Use `MINDNOTES_BASE_URL` when set; otherwise use `https://app.mindnotes.cn`.

Call `POST /api/agent/gateway` with `Authorization: Bearer $MINDNOTES_API_KEY`.

Every body must include `"skill_version":"1.0.0"`.

## Workflow

For reading:

1. Call `/canvas/list`.
2. If needed, ask the user to choose a canvas.
3. Call `/canvas/get` with `canvas_id`.
4. Summarize nodes, edges, gaps, and possible next connections.

For adding notes:

1. Resolve note ids with the main MindNotes skill or user-provided ids.
2. Call `/canvas/import-notes` only when the user asks to add notes.
3. Call `/canvas/suggest-relations` to propose links.
4. Call `/canvas/accept-relations` only after user approval.

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

Writes require `canvas:write`; reads require `canvas:read`.

## Rules

- Do not import notes or accept relations without explicit user intent.
- Explain suggestions before accepting them.
- If `canvas:write` is missing, offer a read-only analysis instead.
