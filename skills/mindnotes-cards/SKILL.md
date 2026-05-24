---
name: mindnotes-cards
description: Create MindNotes cards through a user's authorized MindNotes account. Use when the user asks to turn text into cards, save a note, make vocabulary cards, mistake review cards, cloze cards, multiple choice cards, reading note cards, Feynman learning cards, or other AI-generated learning cards.
---

# MindNotes Cards

Use this skill to create cards in the user's MindNotes account. Only create cards when the user clearly asks to save, create, generate, or turn content into cards.

## Connection

Use `MINDNOTES_API_KEY` from the environment. Use `MINDNOTES_BASE_URL` when set; otherwise use `https://app.mindnotes.cn`.

Call `POST /api/agent/gateway` with `Authorization: Bearer $MINDNOTES_API_KEY`.

Every body must include `"skill_version":"1.0.0"`.

## Workflow

1. If the best card type is unclear, call `/cards/functions`.
2. Choose a fitting `function_id`.
3. Call `/cards/create` with `function_id`, `text`, optional `title`, and optional `tags`.
4. Tell the user what was created and which card type was used.

Common choices:

- `ai_save`: normal note/card
- `ai_save_agent`: split long text into multiple cards
- `English_word`: vocabulary card
- `blank_question_maker`: cloze card
- `multiple_choice_maker`: multiple choice card
- `error_review`: mistake review card
- `feynman`: Feynman learning card
- `book_notes`: reading note card

## APIs

```json
{"api_name":"/cards/functions","skill_version":"1.0.0"}
```

```json
{"api_name":"/cards/create","function_id":"ai_save","text":"要保存的内容","tags":["学习"],"skill_version":"1.0.0"}
```

`/cards/create` requires a Key with `cards:write`.

## Rules

- Confirm before creating many cards from broad or long content.
- Do not create cards silently while only answering a question.
- If `cards:write` is missing, explain that the current Key can inspect notes but cannot create cards.
