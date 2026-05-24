# MindNotes Workflows

Use this reference for common user requests.

## "查一下我的笔记里有没有..."

1. Call `/notes/search` with the user's keyword.
2. If results are enough, summarize matches with title, date, and preview.
3. If the user asks for details or an answer, call `/notes/get` for the strongest matches.

Output:

```text
我找到了 5 条相关笔记，最相关的是：
1. 《标题》：一句话说明
2. 《标题》：一句话说明

可以继续整理成大纲/文章/复盘。
```

## "总结我关于某主题的笔记"

1. Call `/notes/search` with the topic.
2. Read 3-10 relevant notes with `/notes/get`.
3. Produce:
   - short answer
   - themes
   - useful details
   - open questions or missing pieces
   - sources

Avoid repeating every note one by one unless the user asks.

## "我最近在记录什么"

1. Call `/stats/overview`.
2. Call `/notes/recent` with count 20-50.
3. Group recent notes by visible tags and recurring title/content themes.
4. Mention due review count if present.

Output should feel like a personal learning review, not analytics jargon.

## "开始今天的复习"

1. Call `/review/next`.
2. Show the card title, core content, and why it is due.
3. Ask the user to answer or self-rate.
4. After the user says remembered/fuzzy/forgotten, call `/review/submit`.
5. Continue with `/review/next` until the user stops or there are no due cards.

Do not submit a review result from your own guess.

## "明天/这周复习压力怎么样"

1. Call `/review/summary`.
2. Call `/review/schedule` with 7-14 days.
3. Summarize due today, overdue, and peak days.

## "把这段内容做成卡"

1. Call `/cards/functions` if the card type is unclear.
2. Choose the closest `function_id`.
3. Call `/cards/create` only after the user asks to create/save cards.
4. Tell the user what was created and which tags were used.

## "把这些笔记放进知识图谱"

1. Find or confirm the target canvas with `/canvas/list`.
2. Resolve note ids using search/collect.
3. Call `/canvas/import-notes`.
4. Optionally call `/canvas/suggest-relations` and ask before accepting.

## "帮我整理成文章/方案/手册"

1. Search and read relevant notes.
2. Ask a short clarifying question only if the desired format is truly ambiguous.
3. Otherwise choose a practical structure:
   - article: title, thesis, sections, conclusion
   - project plan: goal, current state, tasks, risks, next actions
   - manual: prerequisites, steps, checks, troubleshooting
4. Include sources at the end.

## "找相关笔记"

1. If the user gives a note title or topic, search first.
2. If multiple candidates match, ask the user to choose unless one is clearly dominant.
3. Call `/graph/related` for the selected note.
4. Explain the relation using shared tags and short content cues.

## "导出"

1. Confirm the selection when broad export could be large, such as "all notes".
2. Use `/notes/export`.
3. Return the exported content directly if reasonable.
4. If content is very large, summarize what was exported and provide the main result in chunks.

## Source Style

Use this compact source block:

```text
来源：
- 《标题》 updated 2026-05-23
- 《标题》 updated 2026-05-21
```

Do not expose internal storage details.
