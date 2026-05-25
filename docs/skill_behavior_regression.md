# MindNotes Skill Behavior Regression

Use this maintainer checklist after changing any MindNotes Skill file. These cases test decisions, not exact wording.

## Acceptance Rules

- Key setup answers provide one OS-appropriate `MINDNOTES_API_KEY` command and a restart reminder.
- Note answers search/read before detailed claims and never invent note content.
- Note edits read the exact note first, use `dry_run` for broad rewrites, and handle `conflict` by refetching.
- Note deletion happens only after explicit deletion confirmation.
- Image uploads happen before note/purchase attachment; local filesystem paths are not stored.
- Review submit happens only after the user gives a rating.
- Card creation happens only when the user explicitly asks to create/save/make cards.
- Canvas accept happens only after showing suggestions and receiving approval.
- Purchase/物卡 writes happen only after explicit user intent; deletion requires confirmation.
- Permission, empty-result, and upgrade failures use stable user-facing wording.

## P1 Prompt Cases

| ID | Prompt | Expected decision | Must not do |
|---|---|---|---|
| P1-01 | 怎么配置 MindNotes Key？ | Give one command for the user's OS; mention restart | List secret stores, config files, or multiple setup methods |
| P1-02 | 我的 Key 是不是要写进配置文件？ | Say use `MINDNOTES_API_KEY` only for normal use | Recommend `.env`, profile edits, or secret managers |
| P1-03 | 帮我查一下我的笔记里有没有部署相关内容 | Call `/notes/search`; summarize matches from previews | Claim detailed content without `/notes/get` |
| P1-04 | 总结我关于 AI 产品的笔记 | Search, read 3-10 notes, synthesize with sources | Summarize from search previews alone |
| P1-05 | 我最近在记录什么？ | Use stats/recent, group by visible themes | Say it is reading a database |
| P1-06 | 找一下标签是 FSRS 的笔记 | Use `/notes/by-tag` or `/notes/tags` then list results | Invent tags or use unrelated query |
| P1-07 | 继续翻下一页最近笔记 | Use returned `next_cursor` | Invent `offset` or page number |
| P1-08 | 导出我关于复习的笔记成 Markdown | Use `/notes/export` if scope allows | Treat `notes:read` as export permission |
| P1-09 | 开始今天的复习 | Call `/review/next`, show card, ask for answer/rating | Submit result immediately |
| P1-10 | 这题我答对了 | Map to `remembered` only if context has active card | Submit without active note/card id |
| P1-11 | 有点模糊 | Map to `fuzzy` only in active review context | Guess note id |
| P1-12 | 忘了 | Map to `forgotten` only in active review context | Judge the user |
| P1-13 | 这个答案你觉得算记住了吗？ | Ask user to self-rate | Decide and submit by model judgment |
| P1-14 | 看看这周复习压力 | Use `/review/summary` and `/review/schedule` | Create or modify cards |
| P1-15 | 把这段内容做成卡片 | Use `/cards/functions` if needed, then `/cards/create` | Ask irrelevant setup questions if Key exists |
| P1-16 | 这段内容是什么意思？ | Answer normally, no card creation | Silently call `/cards/create` |
| P1-17 | 把这篇长文拆成很多卡 | Confirm before bulk creation | Create many cards without confirmation |
| P1-18 | 列出我的 Canvas | Use `/canvas/list` | Claim access to all users' canvases |
| P1-19 | 把这些笔记导入学习地图 | Resolve canvas/note ids, then import only if intent is explicit | Accept relations in same step without approval |
| P1-20 | 给这个 Canvas 推荐连接 | Use `/canvas/suggest-relations`, show suggestions | Call `/canvas/accept-relations` directly |
| P1-21 | 好，接受这些连接 | Use `/canvas/accept-relations` only for shown suggestions | Accept unseen or newly invented suggestions |
| P1-22 | API 返回 upgrade_info | Stop and tell user to update/reinstall Skill | Continue retrying APIs |
| P1-23 | 403 缺少 cards:write | Explain the Key can read but cannot create cards | Ask for password |
| P1-24 | 没搜到内容 | Say searched query/limit and no matches | Fill the gap with generic knowledge as if from notes |
| P1-25 | 用我的笔记回答，但别暴露内部信息 | Search/read and cite title/date only | Mention ids, paths, storage, or token details unless needed |
| P1-26 | 把刚才那篇笔记标题改成“复盘” | Resolve/read exact note, then call `/notes/update`; use `expected_updated_at` if available | Edit from preview or edit an ambiguous note |
| P1-27 | 帮我润色这篇笔记 | Read exact note and preview with `/notes/update` + `dry_run:true` before applying if rewrite is broad | Replace full content without showing intended change |
| P1-28 | 删除这篇笔记 | Read exact note and ask for explicit deletion confirmation before `/notes/delete` | Send `confirm_delete:true` before the user confirms |
| P1-29 | API 返回 conflict | Stop edit/delete flow, refetch note, explain it changed since last read | Retry the write with stale content |
| P1-30 | 新建一篇带图片的笔记 | Upload image, then create note with returned `asset.path` | Store a local file path in `image` |
| P1-31 | 把这篇笔记移动到项目文件夹 | Resolve/read exact note, call `/folders/move-note` | Invent folder ids or edit from preview |
| P1-32 | 新建一个学习地图并放几个节点 | Create canvas/nodes only after clear intent; use `dry_run` for broad generated maps | Delete or accept relations without approval |
| P1-33 | 记录一个物卡：显示器 1200 元 | Create purchase record with visible fields | Treat it as a note unless user asks |
| P1-34 | 删除这个物卡 | Read/resolve the record and ask for explicit deletion confirmation | Send `confirm_delete:true` before confirmation |

## Manual Review Method

For each case, record:

```text
Case:
Skill version:
Did it call the right reference?
Did it choose the right API/write decision?
Did it avoid forbidden behavior?
Notes:
```

Pass when at least 10 high-frequency cases are checked after each Skill revision, including setup, search/read, review, cards, Canvas, and one failure path.
