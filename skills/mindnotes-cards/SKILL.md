---
name: mindnotes-cards
description: Create MindNotes cards through a user's authorized MindNotes account. Use when the user asks to turn text into cards, save a note, make vocabulary cards, mistake review cards, cloze cards, multiple choice cards, reading note cards, Feynman learning cards, or other AI-generated learning cards.
---

# MindNotes Cards

Use this skill to create cards in the user's MindNotes account. Only create cards when the user clearly asks to save, create, generate, or turn content into cards.

## Connection

Read the API Key from the environment variable `MINDNOTES_API_KEY`. The key format starts with `mn_sk_`.

Use `https://app.mindnotes.cn` as the default base URL. Use `MINDNOTES_BASE_URL` only when the user explicitly says they are using a self-hosted, staging, or test deployment.

If the Key is missing, give one concise setup instruction:

- Windows PowerShell: `setx MINDNOTES_API_KEY "mn_sk_..."`
- macOS/Linux: `export MINDNOTES_API_KEY="mn_sk_..."`

Tell the user to restart the AI client after setting it. Do not list alternative secret stores, config files, shell profiles, or multiple setup methods unless the user specifically asks for them.

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
