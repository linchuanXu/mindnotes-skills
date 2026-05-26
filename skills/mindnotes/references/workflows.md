# Workflow Reference

Use this when a request spans multiple MindNotes APIs. Keep the workflow narrow: load only the domain references needed for the user's request.

## Search And Answer From Notes

1. Call `/notes/search` with the user's query.
2. Call `/notes/get` for every note you rely on.
3. Answer from note facts first, then add synthesis separately.
4. Mention the search terms and count limit when results may be incomplete.

Do not make detailed claims from search previews alone.

## Topic Summary Or Research Pack

1. Search with `/notes/search` or collect with `/notes/collect`.
2. Read 3-10 relevant notes when possible.
3. Group findings by theme.
4. Separate "from your notes" from "my synthesis".
5. Cite titles and updated dates.

Use `/notes/export` only when the user asks to export, compile, package, or transform a note set.

## Create A Note

1. Confirm the user clearly wants to save a normal note.
2. If an image is involved, call `/assets/upload-image` first.
3. Call `/notes/create`; use `dry_run:true` if intent or content is still ambiguous.
4. Report the title and tags that were saved.

## Edit A Note

1. Resolve the exact note with search if needed.
2. Read it with `/notes/get`.
3. For broad rewrites, call `/notes/update` with `dry_run:true` and show the preview.
4. Apply only after the user confirms or the requested edit is already precise.
5. Use `expected_updated_at` when available to avoid overwriting newer changes.

## Delete A Note

1. Resolve and read the exact note.
2. Ask for explicit deletion confirmation.
3. Call `/notes/delete` with `confirm_delete:true` only after confirmation.
4. Do not batch delete from vague instructions.

## TikCard Review

1. Call `/review/next` or `/review/today`.
2. Show one card and let the user answer.
3. Ask the user to self-rate: remembered, fuzzy, or forgotten.
4. Call `/review/submit` only after the rating.
5. Summarize the next review time.

Do not infer the rating from the user's answer quality.

## AI Card Creation

1. Call `/cards/functions` if the card maker is unknown.
2. Choose the smallest function that matches the content.
3. Create cards only when the user explicitly asks to save/create/make cards.
4. Confirm broad batch creation before `/cards/create`.

## Canvas Map Work

1. Call `/canvas/list` if the canvas is unknown.
2. Call `/canvas/get` before analyzing or editing a canvas.
3. Use `/canvas/importable-notes` before importing notes when the source set is unclear.
4. Use `/canvas/suggest-relations` for suggestions.
5. Show suggestions before `/canvas/accept-relations`.
6. Use `dry_run:true` for ambiguous create/update/delete operations where supported.

## Purchases And Assets

1. Use purchase APIs only for the current user's personal purchase/asset records.
2. Call `/assets/upload-image` before attaching a user-provided image.
3. Use `/purchases/create` or `/purchases/update` only after clear intent.
4. Read/list enough context before updating an existing record.
5. Delete only with explicit confirmation and `confirm_delete:true`.

## Diagnostics

Use `/me` to check the connected account and scopes. Use `/_list` to inspect currently supported APIs and parameters.

