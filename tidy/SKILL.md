---
name: tidy
description: >-
  Tidies a chunk of code for human readability - naming, shape, file length,
  and comments only where the code can't carry it. Use whenever the user asks
  for something to be tidied, cleaned up or made readable, or runs /tidy.
---

# Tidy

One pass over a chunk of code, leaving it readable at a glance to someone
meeting it for the first time. Readability beats brevity: a longer version that
reads straight through wins over a compact one that has to be decoded.

## Workflow

```
- [ ] 1. Pin down the chunk
- [ ] 2. Read what surrounds it
- [ ] 3. Tidy it
- [ ] 4. Split and co-locate
- [ ] 5. Verify
- [ ] 6. Report
```

### 1. Pin down the chunk

The chunk is whatever the user named: a selection, a function, a file, a
directory. Ask when that isn't clear rather than picking a scope for them.

Then stay inside it. A change that belongs to a caller or a sibling is a line in
the report, not an edit. The exceptions are the ones the tidy itself forces:
splitting a file creates new files, and moving or renaming something updates
every reference to it.

Hold behaviour steady. Every edit here is a refactor, and the only changes that
alter what the code does are the clear bugs and dead code of step 3.

### 2. Read what surrounds it

Tidy towards the house style rather than a generic one, so read before editing:
the neighbouring files, the module the chunk sits in, and the `AGENTS.md` that
cover it.

- The word this codebase already uses for the concept, so the tidy adopts it
  instead of coining a synonym.
- The helpers that already exist, so tidying doesn't add a second one.
- How long its files run, and how it splits them when they grow.

House style settles vocabulary and naming. A layout rule written into an
`AGENTS.md` settles layout too: follow it over step 4's defaults, and put the
disagreement in the report rather than tidying against it.

### 3. Tidy it

**Naming**

- Spell names out. `remainingBalance` over `rem`, and the extra characters are
  the point.
- Name for what the thing is in the domain, not for its type or its shape.
- A name that needs a comment to explain it is the wrong name. Rename it.
- Name a compound condition after the decision it drives rather than after one
  of its clauses, and give each clause a name of its own.
- One term per concept across the chunk, and the codebase's term where it has
  one.

**Shape**

- One job per function, short enough to read top to bottom without scrolling
  back to hold it in your head.
- Give intermediate steps names. A condition or an expression pulled out into a
  named constant reads better than the same logic nested inline, even though it
  costs a line.
- Extract parallel checks together or leave them all inline. One of three
  pulled out into a variable reads worse than either.
- Take the edge cases first and return early, so the main path reads unindented
  at the bottom of the function.
- Flatten nesting ahead of shortening code. Depth costs a reader more than
  length does.
- Write the explicit loop or branch where the clever chain has to be decoded.
  Cleverness is only worth it when it reads more plainly than the long way.
- Leave a chain alone when each step fits on one line and only returns a plain
  comparison or value (`.filter(visit => visit.tier === tier)` then
  `.map(visit => visit.id)`). Splitting it into a kept list and a later `.map`,
  or folding it into a `reduce`, reads worse.
- Keep one level of abstraction per function, so a domain step and byte fiddling
  never share a body.

**Comments**

Follow the `review-comments` skill
([review-comments/SKILL.md](../review-comments/SKILL.md)), scoped to the chunk. `AGENTS.md`
and the glossary stay with the `documentation` pass.

**Bugs and dead code**

- Fix a bug that's clear-cut, and delete code nothing reaches or uses.
- An exported option, prop or return field with no caller is interface rather
  than dead code. Leave it and report it.
- Leave anything that hinges on judgement, or on behaviour outside the chunk,
  and put it in the report instead.

### 4. Split and co-locate

Where each piece of logic lives, in two directions: a file too long to scan
comes apart, and logic with a single consumer moves in beside it.

**Split what's too long to scan**

A file is the right length when you can scan it and know what's in it. Once
answering that needs a search or a scroll, split it.

- Split on concept rather than at a line count. Two concepts in a short file
  still split; one concept in a long file may not.
- Name each new file after the concept it holds, and put it where the codebase
  puts files of that kind.

**Co-locate what has one consumer**

Componentise: a unit owns what only it uses, so reading it never means opening
its neighbours. The case to fix is a helper file imported by exactly one other
file, and how far it moves depends on its size.

- Confirm the single consumer by searching the repo for imports of the file.
  The chunk on its own can't tell you nothing else uses it.
- Make the move whatever the surrounding files do. A helper sitting flat beside
  its only consumer is the thing to fix, however many of its neighbours sit the
  same way.
- Small enough that its consumer still scans: move it inside, and delete the
  file.
- Too large for that: turn the consumer into a directory of its own name, its
  contents becoming the `index.ts`, with the helper alongside as a sibling
  module.
- Imported by more than one file: it's shared, so it belongs in a `helpers/` or
  `hooks/` folder at the lowest level covering every file that uses it. One
  file each behind the folder's barrel, however few the folder ends up holding.
- A spec follows its subject, into a `tests/` folder inside the new directory.
  Having one is never a reason to leave the helper where it is.

Carry the imports, exports and barrel files with every move, so nothing outside
has to change to keep working.

### 5. Verify

Verify as [shared/verify.md](../shared/verify.md) sets out. A tidy holds behaviour
steady, so lint and typecheck carry it unless step 3's bugs and dead code
touched logic a suite covers.

### 6. Report

Succinct, as [shared/succinct.md](../shared/succinct.md) sets out:

- What changed, by file, one line each.
- Bugs fixed and dead code deleted, listed on their own. They're the only edits
  that changed behaviour, so they're the ones to review as changes.
- What was deliberately left, and why: the caller that needs the real fix, the
  duplication that isn't safe to merge here, the split that belongs in its own
  piece of work.
