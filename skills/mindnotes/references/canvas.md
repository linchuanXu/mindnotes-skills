# Canvas Reference

Use this for MindNotes Canvas knowledge maps.

## Concepts

| Term | Meaning |
|---|---|
| Canvas | A knowledge map owned by the authorized user |
| Node | A card on the map; may reference a note or a free concept |
| Edge | A saved relation between two nodes |
| Suggestion | A proposed relation; not saved until accepted |
| `source_id` | The original note id when `source_type` is `note` |

Do not tell the user you can see every user's canvases. The API Key only grants the current user's canvases.

## Read Flow

List canvases:

```json
{"api_name":"/canvas/list","skill_version":"1.0.0"}
```

Read one canvas:

```json
{"api_name":"/canvas/get","canvas_id":"canvas-1","skill_version":"1.0.0"}
```

Summarize visible nodes, edges, clusters, gaps, and possible next connections.

## Write Flow

Canvas write APIs support `dry_run:true` for previewing a change before applying it. Delete APIs require `confirm_delete:true`.

Create or update a canvas:

```json
{"api_name":"/canvas/create","title":"学习地图","skill_version":"1.0.0"}
```

```json
{"api_name":"/canvas/update","canvas_id":"canvas-1","title":"新标题","skill_version":"1.0.0"}
```

Create or edit nodes, edges, and groups only after the user clearly asks to change the map:

```json
{"api_name":"/canvas/node-create","canvas_id":"canvas-1","type":"text","title":"核心概念","content":"说明","skill_version":"1.0.0"}
```

```json
{"api_name":"/canvas/edge-create","canvas_id":"canvas-1","source_node_id":"node-1","target_node_id":"node-2","type":"supports","label":"支持","skill_version":"1.0.0"}
```

Delete canvas items only after explicit confirmation:

```json
{"api_name":"/canvas/node-delete","canvas_id":"canvas-1","node_id":"node-1","confirm_delete":true,"skill_version":"1.0.0"}
```

Import notes only after explicit user intent:

```json
{"api_name":"/canvas/import-notes","canvas_id":"canvas-1","note_ids":["abc123"],"skill_version":"1.0.0"}
```

Suggest relations:

```json
{"api_name":"/canvas/suggest-relations","canvas_id":"canvas-1","skill_version":"1.0.0"}
```

Each suggestion may include `source_title`, `target_title`, and `reason`. Use these fields to explain why a link is being proposed. Show suggestions to the user first. Accept only after approval:

```json
{"api_name":"/canvas/accept-relations","canvas_id":"canvas-1","suggestions":[{"source_node_id":"node-1","target_node_id":"node-2","type":"related"}],"skill_version":"1.0.0"}
```

Writes require `canvas:write`; reads require `canvas:read`.

## Safety Rules

- Do not import notes without explicit intent.
- Do not accept suggestions before showing them.
- Do not delete canvases, nodes, edges, groups, or batches without explicit confirmation.
- If there are multiple canvases, ask the user to choose unless one is clearly named in the request.
- If `canvas:write` is missing, offer read-only analysis instead.
