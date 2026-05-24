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

Import notes only after explicit user intent:

```json
{"api_name":"/canvas/import-notes","canvas_id":"canvas-1","note_ids":["abc123"],"skill_version":"1.0.0"}
```

Suggest relations:

```json
{"api_name":"/canvas/suggest-relations","canvas_id":"canvas-1","skill_version":"1.0.0"}
```

Show suggestions to the user first. Accept only after approval:

```json
{"api_name":"/canvas/accept-relations","canvas_id":"canvas-1","suggestions":[{"source_node_id":"node-1","target_node_id":"node-2","type":"related"}],"skill_version":"1.0.0"}
```

Writes require `canvas:write`; reads require `canvas:read`.

## Safety Rules

- Do not import notes without explicit intent.
- Do not accept suggestions before showing them.
- If there are multiple canvases, ask the user to choose unless one is clearly named in the request.
- If `canvas:write` is missing, offer read-only analysis instead.
