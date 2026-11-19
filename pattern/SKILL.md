---
name: pattern
description: >-
  Discusses a new pattern for long-term maintainability, and how it would sit
  in this repo, without letting the patterns already in use decide it, then
  implements the one chosen. Use only when the user runs /pattern.
disable-model-invocation: true
---

# Pattern

A discussion of what pattern to use, judged on how it holds up over time, and
then how that pattern would sit in this repo. A pattern already in use doesn't
get a say in the choice. Nothing is implemented until the user picks an
option. The discussion happens in whatever mode is active, without switching.

## Workflow

```
- [ ] 1. Name the problem
- [ ] 2. Read where it has to live
- [ ] 3. Research, where a known pattern applies
- [ ] 4. Discuss the decision
- [ ] 5. Implement the chosen pattern
- [ ] 6. Restate the pattern and wait for review
```

### 1. Name the problem

The subject is the problem the user wants a pattern for, in their words. Where
they haven't said what has to stay easy to change, ask that, as a numbered
choice, and wait for the number.

### 2. Read where it has to live

Read the code the pattern has to work with: the callers, the data, and the
boundaries it can't cross. That reading is so a later option can be checked
against what this repo actually has to do.

A pattern, helper, or convention already used here is not a candidate and not
a reason to prefer one. When the better pattern cuts against a local habit,
name that as the cost of adopting it.

### 3. Research, where a known pattern applies

Where the problem is a known kind (how state is held, how a module hides its
work, how a change is extended), search for the patterns people actually keep.
Use primary sources: the author's own writing, the official docs, the spec. A
roundup post is a lead, not a source.

Skip the search when the choice is particular to this product and no general
pattern applies.

Bring back two or three candidates. For each, a few sentences: what it keeps
easy to change, and where it gets costly as the second and third case arrive.

### 4. Discuss the decision

One decision, in one message, and the message is the whole discussion. No
question tool. The user replies with a number.

- The decision, in a sentence.
- The recommendation, in the next sentence: the option to take, and the reason
  it holds up where the others get costly.
- The reasoning: what the research found, where a candidate ages badly, and
  what adopting it would take in this repo. The repo's current pattern is not
  part of that case. Short. On Opus, which runs long, draft it and cut
  anything the choice doesn't turn on.
- A numbered list, that option first and marked `(Recommended)`. It is the
  only numbered thing in the message: the reasoning names each candidate in
  prose, never as a numbered or bulleted run, or the message reads as twice
  the options.

Then stop and wait for the number.

### 5. Implement the chosen pattern

Once the user has replied, build the option they chose where step 2 found it
has to live, switching to Agent mode first when another mode is active.

Document it in the same step, as the `review-docs` skill's **Writing** route
sets out ([review-docs/SKILL.md](../review-docs/SKILL.md)): the pattern as a rule the
next author can follow, in the `AGENTS.md` nearest the code it governs. The
code and its doc reach review together.

### 6. Restate the pattern and wait for review

Restate the pattern in a few sentences: what it is, what it hides, how a new
case is added, where it now sits, and where it's documented. Then stop with
the work uncommitted, and commit only once the user has reviewed it and asks.
