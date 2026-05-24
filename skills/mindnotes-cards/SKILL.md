---
name: mindnotes-cards
description: Create MindNotes cards through a user's authorized MindNotes account. Use when the user asks to turn text into cards, save a note, make vocabulary cards, mistake review cards, cloze cards, multiple choice cards, reading note cards, Feynman learning cards, or other AI-generated learning cards.
---

# MindNotes Cards

Use this skill to create cards in the user's MindNotes account. Create cards only when the user clearly asks to save, create, generate, make cards, or turn content into cards.

## Connection

Read the API Key from `MINDNOTES_API_KEY`. The key starts with `mn_sk_`.

Use `https://app.mindnotes.cn` by default. Use `MINDNOTES_BASE_URL` only when the user explicitly says self-hosted, staging, or test deployment.

If the Key is missing, give one concise setup instruction:

- Windows PowerShell: `setx MINDNOTES_API_KEY "mn_sk_..."`
- macOS/Linux: `export MINDNOTES_API_KEY="mn_sk_..."`

Tell the user to restart the AI client. Do not list alternative secret stores, config files, shell profiles, or multiple setup methods unless asked.

Call `POST /api/agent/gateway` with `Authorization: Bearer $MINDNOTES_API_KEY`. Put all fields at the top level and always include `"skill_version":"1.0.0"`.

If a response contains `upgrade_info`, stop and tell the user to update/reinstall MindNotes Skill before continuing.

## Workflow

1. If the best card type is unclear, call `/cards/functions`.
2. Choose a fitting `function_id`.
3. Confirm before creating many cards from broad or long content.
4. Call `/cards/create` with `function_id`, `text`, optional `title`, and optional `tags`.
5. Tell the user what was created and which card type was used.

Do not create cards silently while only answering a question.

## APIs

```json
{"api_name":"/cards/functions","skill_version":"1.0.0"}
```

```json
{"api_name":"/cards/create","function_id":"ai_save","text":"要保存的内容","tags":["学习"],"skill_version":"1.0.0"}
```

`/cards/create` requires `cards:write`. If missing, explain that the current Key can inspect notes but cannot create cards.

## Common Function Choices

| Need | Likely `function_id` |
|---|---|
| Save a normal note/card | `ai_save` |
| Split long text into cards | `ai_save_agent` |
| Vocabulary card | `English_word` |
| Cloze card | `blank_question_maker` |
| Multiple choice card | `multiple_choice_maker` |
| Mistake review card | `error_review` |
| Feynman learning card | `feynman` |
| Reading note card | `book_notes` |

Call `/cards/functions` if function ids may have changed.
