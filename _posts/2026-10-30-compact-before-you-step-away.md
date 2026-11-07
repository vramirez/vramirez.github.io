---
layout: post
title: "Compact Before You Step Away"
date: 2026-10-30
categories: [tools, ai, development]
tags: [claude, claude-code, prompt-caching, caching, compaction, workflow]
---

[_this post was written by me, then edited by Claude and myself_]

The other day I was reading [this post](https://claude.com/es/resources/articles/claude-opus-5-5-built-for-coding-sessions-that-use-more-context) about how Opus 5.5 was "_built with longer coding sessions and more context usage in mind_" when I read this recommendation right at the end of the article: 

>"Compact before you step away rather than after"

This phrase stopped me in my tracks, because it was something I did very often. This affected me directly. 

Usually when I'm coding and it's late in the evening (I'm more of an early bird than a night owl) I just leave my laptop working and then I wake up the next day and just ask Claude "_where are we now?_" and Claude starts `Booping...`, `Thinking...`, `Flabbergasting...` and takes a few seconds (or minutes) before giving me an answer. 

Turns out there's a good reason not to do this and I hadn't thought about it AT ALL.

## Why cache

The model doesn't remember anything between turns. What looks like a conversation is really the whole transcript being sent (back and forth) again on every request. The [prompt caching docs](https://code.claude.com/docs/en/prompt-caching) spell it out: system prompt, project context, every prior message and tool result, and then your new message.

So once a session reaches 300,000 tokens, typing "thanks, that worked" makes the model process 300,000 tokens of history before it reads your four words.

In my head a chat feels like a phone call, with the context just sitting there in the room. But underneath it's more like sending a letter with the whole correspondence stapled to the front, every time. Cache is just making this whole "conversation" faster.

## The cache is what makes this bearable

Reprocessing everything on every turn would be painfully expensive, so there's prompt caching. When a request starts with the same text as a recent one, the API reuses the work it already did on that shared prefix. Reading from the cache costs [a small fraction of normal input](https://platform.claude.com/docs/en/about-claude/pricing).

I intentionally don't want this article to become a pitch about Claude caching costs or how to tune or set up these parameters. You can do that using Claude Code itself. I'm more into how we sometimes do good or in this case harm to ourselves, and our context/money usage out of unknown concepts.

## Cache expiring
As you may know there's some caching going on while you go back and forth with Claude. Your (growing) conversation is being cached while you move forward, but this cache has an expiration. Then when you walk away for a long time that cache finally dies. At some point in time, your cache is released. So when you come back the next morning and say "hey, claude, what's up?", what Claude will do is read that massive conversation where you vibe-coded your whole new GUI all over again and store it AGAIN in cache. Only when it's done, Claude will finally give you a status.

## What this means for you

So, basically, on that very first "Hey, Claude!" morning message, you're not only sending the greetings/status report message but the whole conversation you had in this window, which burns lots of your precious tokens right from the beginning by reprocessing the whole conversation (unnecessarily). I realized that's why sometimes I'm only a few minutes into the conversation and I'm already at 8-10% of my 5-hour window (I'm on the Pro plan rn).

## What to do
So, lately (when I remember) I've added one more step. If a Claude Code session has gotten long, I type `/compact` before I leave.

It looks like housekeeping, but it saves a lot in context (and money if you're on the API).

## Why before, and not after

This is the part that took me a while to notice.

`/compact` isn't free. To write the summary, Claude Code sends a separate request containing your system prompt, your tools, and the entire history it's about to summarize. That's the same big read we've been talking about. The docs on [compacting the conversation](https://code.claude.com/docs/en/prompt-caching#compacting-the-conversation) explain the consequence:

> While the cache is warm, that request reads your prefix from the cache. [...] After a break longer than the cache lifetime, there is no cache left to read, so the summarization request reprocesses that history as uncached input.

So, compacting before leaving means the summary is built from the cached conversation (a cheaper read). If we do this later, with a cold cache, the summarizer reprocesses all those thousands of tokens as fresh input before it has written a word. That's full price to reread a long exchange of letters only so you can write a one-page summary of it.

The same action costs very different amounts depending on when you do it. I wondered if this could be tuned somehow...

## The tool already does this, sometimes

While researching this post I found that Claude Code has a setting called [`idleCompaction`](https://code.claude.com/docs/en/settings-reference#idlecompaction), and it's on by default. When you've stepped away from a long conversation, it compacts the conversation for you. The catch is the conditions. All of these have to be true:

- The conversation is on the one-hour TTL.
- The context has reached a threshold, 200,000 tokens by default.
- About an hour has passed since the last request.
- **Your computer stays awake until then.**

That last one made me laugh, because I'm on a laptop. Close the lid to go to lunch (or sleep) and the machine is asleep when the hour comes around, so idle compaction never runs. It also does nothing on the five-minute TTL. Not to mention that not all my long conversations go above 200K tokens.

## What's next

Currently, I'm working on building a skill which allows me to close the little open loops that sometimes happen in a conversation (like a bug Claude detects, or a new PR it recommends opening alongside current work, etc). I'm calling it `/close` but it still isn't giving me the expected results. It's way too picky. But the idea is there, to help free up some context before I leave.

## Nit

Not everyone knows that `/compact` also takes instructions, which helps when you know what matters:

```
/compact keep the failing test name, the migration plan, and the open benchmark report
```

If you've actually finished the task, skip `/compact` and use `/clear`. The docs put it plainly: "`/clear` costs nothing." Summarizing work I'm done with is just wasting tokens.

So, next time you know you'll walk away for a long time, or until tomorrow, compact first, then go get the coffee (or some sleep).
