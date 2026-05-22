---
layout: post
title: "What Belongs in a CLAUDE.md (and What Doesn't)"
date: 2026-05-22
categories: [tools, ai, development]
tags: [claude, claude-code, claude-md, skills, workflow]
---

A `CLAUDE.md` file is read every single turn. Whatever you put in it, the model rereads it before answering, again and again, for as long as the project lives. That is the one fact that should shape everything you write there.

It is tempting to treat the file like a notepad. A place to jot down anything you might want the model to know. But a notepad is free and `CLAUDE.md` is not. Every line you add is a line that gets reread on the next request, and the one after that, whether or not it matters to the task at hand. So the question for each line is simple. Is this true often enough to be worth reading every time?

Most of the time the answer sorts itself into two piles. There are the things that are always true, and there are the things that are only sometimes needed. The first pile belongs in `CLAUDE.md`. The second pile belongs somewhere else, and I will get to where.

## What belongs

The good entries are the durable ones. Facts that hold across the whole project and rarely change.

Who you are and what you do is a fair start. The model works differently for a backend engineer than for a designer, and telling it once saves you from repeating yourself. Your role, the company you work for if that matters, the kind of work you tend to ask for. These are cheap to state and they color every answer.

Then there are the conventions that hold everywhere in the codebase. The testing approach. The naming patterns. The libraries that are in favor and the ones that are on their way out. The model cannot sit in on your code reviews or overhear the hallway conversations where this knowledge usually travels, but it can read a file. So a short, plain statement of how things are done here is one of the most useful things you can write.

A small example, kept generic:

```markdown
# Project conventions

- I'm a backend engineer. Most requests are API or data work in Python.
- Tests use pytest. Prefer table-driven cases over many small functions.
- Use the standard library before reaching for a dependency.
- HTTP handlers live in `api/`, business logic in `core/`. Keep them separate.
```

None of that will change next week. That is what makes it worth rereading.

## Link out, don't inline

Some of what you want the model to know is already written down. A style guide. A company engineering doc. An architecture decision record explaining why the database was chosen. The instinct is to copy the relevant parts into `CLAUDE.md` so the model has them on hand.

I would resist that instinct. Copying creates two problems. The file grows, and the copy drifts from the original the moment someone updates the source. Now you have two versions of the truth and no good way to tell which one is current.

A link solves both. Point to the document and let the model read it when the work calls for it.

```markdown
# References

- API design guide: <internal link>
- Database ADR: <internal link>
- Deployment runbook: <internal link>
```

The file stays small. The source of truth stays singular. And the model still knows where to look.

## What doesn't belong

The entries that quietly hurt you are the specific ones. The step-by-step procedure for one routine you run now and then.

Say you have a careful sequence for cutting a release. Bump the version, run the suite, build the artifact, tag it, push to the registry, write the notes. It feels responsible to write all of that into `CLAUDE.md` so the model gets it right. But you cut releases now and then, and the model reads those steps every time you ask it anything. You are paying for that procedure on every request and using it almost never.

The cost is real, and there is a quieter cost too. A file padded with rare procedures is a file the model has to wade through to find the parts that actually apply. The signal you care about gets buried in instructions for a moment that is not now.

A good test is to read each section and ask when it last mattered. If the honest answer is "only when I happen to be doing that one thing," it does not belong in the file that is read every time.

## Where that knowledge goes: skills

The procedures still need a home. That home is a skill.

A skill is a self-contained set of instructions that the model loads only when the task calls for it. You write it once, in its own file, and it sits quietly out of the way until it is needed. The official docs put it well: a skill's body loads only when it is used, so long reference material costs almost nothing until you reach for it. That is the exact property the release procedure wanted.

In practice a skill is a small directory with a `SKILL.md` inside. The release routine from earlier would move out of `CLAUDE.md` and become something like this, saved to `.claude/skills/release/SKILL.md`:

```markdown
---
description: Cut a new release. Use when asked to ship a version or publish a release.
disable-model-invocation: true
---

Cut a release:

1. Bump the version in the manifest.
2. Run the full test suite. Stop if anything fails.
3. Build the artifact.
4. Tag the commit and push the tag.
5. Publish to the registry.
6. Draft release notes from the commits since the last tag.
```

Now the steps live where they are used. When you ask to ship a release, the model loads them. The rest of the time they cost you nothing, and your `CLAUDE.md` is shorter and clearer for their absence.

There is more you can do with skills than this, and the [skills documentation](https://code.claude.com/docs/en/skills) is worth a read when you want to go further. But the basic move is the one that matters here. A procedure that grew up inside your `CLAUDE.md` can usually move out and become a skill, and both files get better for it.

## The simple version

It comes down to one distinction. `CLAUDE.md` is for what is always true. Skills are for what is sometimes needed.

If you keep the always-true file small and honest, the model spends its attention on what matters, and you spend less time wondering why it ignored the one line that counted. The discipline is not complicated. It is just the habit of asking, before each line goes in, whether it earns the cost of being read every single time.

Most lines do not. The good ones do.
