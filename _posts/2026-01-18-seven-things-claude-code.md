---
layout: post
title: "Seven Things I've Learned Using Claude Code"
date: 2026-01-18
categories: [tools, ai, development]
tags: [claude, ai-assisted-development, productivity, workflow]
---

There's a difference between using a tool and learning to work with one.

A carpenter doesn't simply *use* a chisel—she develops a feel for it. The angle of approach, the pressure required for different grains, the subtle feedback through the handle that tells her when the wood is about to split. This knowledge doesn't come from reading the manual. It comes from hours of quiet attention, from failures that teach more than successes, from gradually understanding the tool's nature rather than fighting against it.

![Craftsman working with wood](https://www.westrivereagle.com/wp-content/uploads/2020/08/N1905P34006H-scaled.jpg)

I've spent the last several months working with Claude Code as part of my daily development workflow. What started as curiosity became habit, and habit became something I'd describe as collaboration. Along the way, I've accumulated some hard-won intuitions about what makes this particular relationship productive.

These aren't tricks or hacks. They're more like principles—patterns I return to when things aren't working, reminders of what I've learned works best.

## 1. Plan before you build

The single most valuable habit I've developed is using plan mode before writing any significant code. It's deceptively simple: before implementing, ask Claude Code to think through the approach.

What this does is surface problems early. Architectural questions that would have bitten me three hours into implementation get addressed in the first ten minutes. Dependencies I hadn't considered become visible. The scope of what I'm actually trying to accomplish becomes clearer—sometimes smaller, sometimes larger, but always more honest.

This mirrors good engineering practice generally. We don't skip design documents because we're eager to code. We don't merge without review because we're confident. The discipline of planning exists because our first instincts about implementation are often wrong in subtle ways.

With Claude Code, I've found that the quality of output correlates strongly with the quality of the plan that preceded it. A well-considered plan produces focused, coherent code. A vague plan produces code that wanders.

## 2. Context is perishable

Long conversations degrade. This is true between humans—think of any meeting that went an hour too long—and it's true when working with AI.

As context accumulates, earlier details get compressed or lost. The model's attention drifts. What started as a focused discussion about database schema becomes muddled with tangential questions about deployment and that one error message from twenty minutes ago.

The commands `/clear` and `/compact` exist for exactly this reason. Starting fresh isn't giving up; it's often the fastest path forward. When I notice Claude Code producing increasingly confused output, or when the conversation has genuinely moved to a different topic, I clear the slate.

This requires letting go of the sunk cost fallacy. Yes, you spent time building up that context. But if the context is now hurting more than helping, preserving it is false economy.

## 3. Write things down

Here's a pattern that transformed my workflow: maintain a small collection of markdown files that track state across sessions.

I keep three simple documents for any substantial project:
- **plan.md** — the current design and approach
- **context.md** — key architectural decisions and their rationale
- **tasks.md** — what's done, what's in progress, what's next

When I start a session, I point Claude Code at these files. When I end a session, I update them with anything important that emerged. The AI doesn't remember between sessions, but files do.

This externalizes the memory problem. Instead of trying to reconstruct context from scratch each time—or hoping the model somehow retained what matters—I maintain a living document that captures the actual state of the work.

It's also useful for me, separate from any AI involvement. Writing things down clarifies thinking. It catches contradictions. It creates accountability.

## 4. Ask, don't tell

There's a difference between giving Claude Code orders and collaborating with it on solutions.

"Write a function that does X" is fine for simple tasks. But for anything non-trivial, I've had better results with a different approach: explaining what I'm trying to accomplish, what constraints exist, and asking for its thoughts on how to proceed.

This isn't anthropomorphization. It's about leveraging the model's actual strengths. When you command a specific implementation, you're constraining the solution space to what you already imagined. When you describe the problem and ask for collaboration, you sometimes get approaches you wouldn't have considered.

The best prompts I've written read more like the opening of a design discussion than the specification for a code monkey. "I need to handle authentication for this service. The constraints are X, Y, and Z. We're already using library A for related functionality. What's a clean approach here?"

The better question isn't "what can Claude build for me?" It's "what does Claude need to know to help?"

## 5. Teach it your patterns

Every codebase has conventions—naming patterns, architectural preferences, testing approaches, tools that are in favor and tools that are deprecated. This knowledge lives in developers' heads and gets transmitted through code review and hallway conversations.

Claude Code doesn't have access to hallway conversations. But it can read files.

The `CLAUDE.md` file in a project root is an opportunity to encode project-specific knowledge. My own setup includes things like:
- Preferred testing frameworks and patterns
- Naming conventions for different types of modules
- Common pitfalls specific to this codebase
- Dependencies that should and shouldn't be used

This investment pays compound returns. Instead of correcting the same convention violations repeatedly, you teach once. The model learns your codebase's dialect.

## 6. Break the work into pieces

Ambitious one-shot attempts almost never work. "Implement the entire feature" is not a useful prompt, no matter how detailed your description.

What works is decomposition. Break the feature into components. Implement and verify each component. Build up incrementally, with tests or manual verification at each step.

This isn't different from how I'd approach the same work without AI assistance. Large changes are risky because they're hard to debug. When something goes wrong with a 500-line diff, the culprit could be anywhere. When something goes wrong with a 50-line diff, the possibilities are constrained.

The difference with AI assistance is that the temptation to skip decomposition is stronger. It feels like the model should be able to handle larger chunks. And sometimes it can. But the failure modes are catastrophic—you end up with a large amount of plausible-looking code that doesn't quite work, and debugging it takes longer than writing it correctly would have.

Smaller, testable increments. Every time.

## 7. Know when to go deeper (and when not to)

There's more under the hood—event-driven hooks, automatic skill triggers, elaborate workflows for power users. Worth exploring once you're comfortable with the basics.

But here's the counterpoint: sometimes the fastest path is your own two hands.

I've caught myself wrestling with a prompt for ten minutes when the edit would take thirty seconds. The cost of explaining, verifying, and course-correcting can exceed the cost of just doing it.

Some things resist delegation. A surgical one-line fix. Anything requiring eyeball judgment. Work where the setup explanation dwarfs the task itself.

Effectiveness isn't about maximizing how much you hand off. It's about choosing the right tool for each moment—and sometimes that tool is you.

---

The chisel doesn't replace the carpenter's skill. It changes the nature of the skill required.

What I've learned working with Claude Code isn't really about Claude Code specifically. It's about the discipline of clear thinking—planning before building, maintaining context, breaking problems down, knowing when to ask for help and when to proceed alone.

These practices made me more effective with AI assistance. They also made me more effective without it. The tool didn't replace anything. It revealed what was already there, waiting to be practiced.

---

*Some of these insights were informed by [excellent tips from ykdojo](https://github.com/ykdojo/claude-code-tips) and discussions in the developer community.*
