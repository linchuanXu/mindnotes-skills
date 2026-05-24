# Output Reference

Use these formats for user-facing MindNotes answers. Keep wording natural; the formats are guardrails, not scripts.

## Search Results

```text
我找到了 N 条相关笔记，最相关的是：
1. 《标题》：一句话说明。updated 2026-05-23
2. 《标题》：一句话说明。updated 2026-05-21

我已搜索：关键词；数量限制：10。
```

Do not include note ids unless needed to open or disambiguate notes.

## Topic Summary

```text
简短结论：...

主要主题：
1. ...
2. ...

值得保留的细节：
1. ...
2. ...

还缺的信息：
1. ...

来源：
- 《标题》 updated 2026-05-23
- 《标题》 updated 2026-05-21
```

Separate direct note facts from synthesis.

## Review Interaction

```text
下一张：标题

内容：...

记忆状态：...

你可以先回答，然后告诉我：记住了 / 有点模糊 / 忘了。
```

Submit only after the user gives the rating.

## Canvas Analysis

```text
这个画布现在有 N 个节点、M 条关系。

我看到的结构：
1. ...
2. ...

可以补的连接：
1. A -> B：原因
2. C -> D：原因

确认后我再帮你接受这些关系。
```

Never accept relations before approval.

## Export Result

For small Markdown exports, return the content directly. For large exports, summarize what was exported first, then provide chunks.

```text
已按「主题/标签/指定笔记」导出 N 条笔记，格式是 Markdown。
```

## Failure Wording

| Error | User-facing wording |
|---|---|
| Missing Key | "需要先设置 `MINDNOTES_API_KEY`，然后重启 AI 客户端。" |
| `401` | "这个 API Key 无效或已过期，请在 MindNotes 重新生成。" |
| `403` | "这个 API Key 缺少所需权限。" |
| `upgrade_required` | "MindNotes Skill 需要更新后才能继续。" |
| Empty search | "我没有在你的笔记里找到相关内容；我搜索的是..." |

Do not expose stack traces, backend paths, token hashes, or storage details.
