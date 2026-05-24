# MindNotes Skills

Public Codex skills for MindNotes. These skills let AI work with a user's authorized MindNotes account through a personal API Key.

MindNotes data stays on the MindNotes server. The skills contain instructions only; they do not include backend code, secrets, databases, or user notes.

## Skills

| Skill | Use for | Install |
|---|---|---|
| `mindnotes` | All-in-one notes, review, card creation, stats, export, and Canvas workflows | `npx skills add linchuanXu/mindnotes-skills/skills/mindnotes -g` |
| `mindnotes-review` | TikCard review sessions, review scheduling, and review result submission | `npx skills add linchuanXu/mindnotes-skills/skills/mindnotes-review -g` |
| `mindnotes-cards` | AI card creation from text, vocabulary, mistakes, cloze cards, choices, and reading notes | `npx skills add linchuanXu/mindnotes-skills/skills/mindnotes-cards -g` |
| `mindnotes-canvas` | Knowledge map and Canvas workflows: read canvases, import notes, suggest and accept relations | `npx skills add linchuanXu/mindnotes-skills/skills/mindnotes-canvas -g` |

Restart Codex after installing a skill.

## Configure

1. Open MindNotes.
2. Go to the Skill page.
3. Generate a personal API Key.
4. Set it as an environment variable.

Windows PowerShell:

```powershell
setx MINDNOTES_API_KEY "mn_sk_..."
```

macOS or Linux:

```bash
export MINDNOTES_API_KEY="mn_sk_..."
```

The default API base URL is:

```text
https://app.mindnotes.cn
```

For self-hosted or staging deployments, also set:

```powershell
setx MINDNOTES_BASE_URL "https://your-domain.example"
```

## Permissions

MindNotes API Keys are scoped:

| Scope | Allows |
|---|---|
| `notes:read` | Search, read, and list notes |
| `notes:export` | Collect or export note content |
| `stats:read` | Read learning and review stats |
| `graph:read` | Read related notes and graph suggestions |
| `canvas:read` | Read Canvas data |
| `review:write` | Submit TikCard review results |
| `cards:write` | Create AI cards |
| `canvas:write` | Import notes and accept Canvas relations |

Use the narrowest permission preset that fits your workflow.

## Safety

- Do not share your API Key.
- Revoke unused API Keys in MindNotes.
- The skills never ask for a MindNotes password.
- The skills never require a user id; the API Key identifies the current authorized user.
