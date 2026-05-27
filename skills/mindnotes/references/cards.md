# Cards Reference

Use this when the user wants to create or save MindNotes cards.

## When To Create

Create cards only when the user clearly asks to save, create, generate, make cards, or turn content into cards. If the user only asks a question, answer normally and do not call `/cards/create`.

If the content is broad or long, confirm before creating many cards.

## APIs

List card makers:

```json
{"api_name":"/cards/functions","skill_version":"1.0.0"}
```

Create cards:

```json
{"api_name":"/cards/create","function_id":"ai_save","text":"要保存的内容","tags":["学习"],"skill_version":"1.0.0"}
```

`/cards/create` requires `cards:write`.

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

Call `/cards/functions` if the available function ids may have changed or the best function is unclear.

## Output

After creation, say what was created, which function was used, and any tags/title used. Do not expose internal handler names unless the user needs debugging.
