---
layout: post
title: "Compact Before You Step Away"
date: 2026-10-30
categories: [tools, ai, development]
tags: [claude, claude-code, prompt-caching, compaction, workflow]
---

Before I get up from my desk I have a small routine. I save the file and write a quick commit message so I know where I left off. Then I check whether anything is still running in a terminal.

Lately I've added one more step. If a Claude Code session has gotten long, I type `/compact` before I leave.

It looks like housekeeping, but it saves real money. The reason has to do with something that's easy to forget: the tool is paying attention to time even when you aren't.

## Every turn re-reads everything

The model doesn't remember anything between turns. What looks like a conversation is really the whole transcript being sent again on every request. That means every message, every file it read, every tool output, and every long stack trace you pasted in at 11pm.

So once a session reaches 400,000 tokens, typing "thanks, that worked" makes the model process 400,000 tokens of history before it reads your four words.

In my head a chat feels like a phone call, with the context just sitting there in the room. In practice it's closer to sending a letter with the whole correspondence stapled to the front, every time.

## The cache is what makes this bearable

Reprocessing everything on every turn would be painfully expensive, so there's prompt caching. When a request starts with the same text as a recent one, the provider can reuse the work it already did on that shared prefix. Reading from the cache costs a small fraction of normal input.

Here are the numbers for Claude Opus 5.5, from the current pricing:

| | Price per million tokens |
|---|---|
| Normal input | $4.00 |
| Cache read | $0.20 (5% of input) |
| Cache write, 5-minute TTL | 1.25x input = $5.00 |
| Cache write, 1-hour TTL | 2x input = $8.00 |

The catch is in the last two rows. **The cache expires.** Entries have a time-to-live: 5 minutes by default, or an hour if the cache entry asks for the longer TTL. Each request that reads the cache resets its timer. As long as you keep talking, the cache stays warm and every turn costs very little.

When you stop talking, the timer keeps running.

## Doing the math

I like to see the numbers, so here's a throwaway script:

```python
INPUT = 4.00          # $ per million tokens, Opus 5.5
READ = 0.05 * INPUT   # cache read
WRITE_1H = 2 * INPUT  # cache write, 1-hour TTL
WRITE_5M = 1.25 * INPUT

context = 400_000     # tokens in a long-ish session

for label, rate in [("warm cache", READ),
                    ("cold, 5m TTL", WRITE_5M),
                    ("cold, 1h TTL", WRITE_1H)]:
    print(f"{label:>14}: ${context / 1e6 * rate:.2f}")
```

```
    warm cache: $0.08
  cold, 5m TTL: $2.00
  cold, 1h TTL: $3.20
```

So the same message can cost eight cents or three dollars twenty. The only difference is whether you went to make coffee in between.

That's about 40 times more for the identical message. Nothing about the work changed. The only thing that changed was the clock.

If you're on a subscription and not paying per token, the same thing happens in a different currency. The cold rebuild counts against your usage limits, so a few long breaks can eat a surprising share of your allowance.

## Why before, and not after

This is the part that took me a while to notice.

`/compact` isn't free. To summarize a conversation, the model has to read the whole conversation, and that is the same 400,000-token read we've been talking about.

If you compact before you leave, the cache is still warm. That big read is billed at cache-read prices, and what's left behind is a small summary, maybe 20,000 tokens. When you come back, the cache has expired, but rebuilding 20,000 tokens is cheap. For the cold 1-hour write in the table that's about sixteen cents, compared with $3.20.

If you compact after you come back, you get the worst of both. First you pay the cold price to reprocess all 400,000 tokens. Then you pay again to write the summary. That's full price to reread a long book only so you can write a one-page summary of it.

The same action costs very different amounts depending on when you do it. I like that about this tool, a little reluctantly. It rewards paying attention to things you can't see.

## The routine

Before I step away, I ask myself three questions:

1. **Will I be gone longer than the cache lasts?** A quick trip to refill water probably isn't. Lunch definitely is.
2. **Is the context actually big?** A short session costs almost nothing to rebuild, so compacting it is ceremony with no payoff.
3. **Is there detail I can't afford to lose?** Compaction is a summary, and summaries are lossy. If I'm in the middle of a delicate debugging session where the exact error text matters, I'd rather pay for the cold restart.

If the answers are yes, yes, and no, I compact. `/compact` also takes instructions, which helps when you know what matters:

```
/compact keep the failing test name, the migration plan, and the open questions about the schema
```

If I've actually finished the task, I skip `/compact` and use `/clear`. Summarizing work I'm done with is just a more expensive way to forget it.

## Noticing the gap

What I find interesting here isn't really the money. It's that the machine has no sense of my absence. It doesn't know I went for a walk or got pulled into a meeting. All it knows is that the clock ran out.

Most of the tools I've learned well taught me something about their shape this way. You learn where the edges are and when the easy path stops being easy. Prompt caching has a time dimension that's invisible until you go looking for it. Once you see it, you start paying attention to pauses, and to the moment just before you get up from the desk.

The cache doesn't care that you stepped away. It only knows how long you were gone.

So compact first, then go get the coffee.
