# MindNotes Skill Optimization Learning Loop

This document keeps future Skill work focused on behavior stability before API expansion.

## Current Baseline

- P0 is complete: capability map, split references, output formats, specialized Skills, and `upgrade_info` exist.
- Public user-facing Skill text should describe MindNotes capabilities only.
- Maintenance notes may discuss patterns and evaluation, but should not leak into user-facing responses.

## P1: Behavior Regression

Build changes around observed failures:

1. Add or update a prompt case in `docs/skill_behavior_regression.md`.
2. Tighten the smallest relevant Skill rule or reference.
3. Validate the Skill folders.
4. Run backend smoke tests when gateway behavior may be affected.
5. Manually replay at least 10 high-frequency prompt cases.

## P2: Document Refinement

After several P1 rounds:

- Move rules that repeatedly prevent mistakes closer to the top of `SKILL.md`.
- Remove low-value explanation from references.
- Keep one fact in one place whenever possible.
- Keep examples short and focused on fragile behavior.

## P3: Product Enhancements

Only consider new API/backend work after behavior is stable:

- Better full-text collection for synthesis.
- Cross-note topic clustering.
- Card preview before write.
- Canvas suggestion explanations.
- Skill call logging and failure analytics.

Do not bump `skill_version` unless older Skills must stop for safety or protocol compatibility.
