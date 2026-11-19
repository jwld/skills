---
name: review-file
description: >-
  Reviews one file, then works through the findings one phase at a time: the
  problem, the fix, the user's go-ahead, then a commit offer. Use only when the
  user runs /review-file.
disable-model-invocation: true
---

# Review file

A full review of one file, then its findings taken one at a time. Every fix
waits for the user's go-ahead, and every commit waits for theirs too.

## Workflow

```
- [ ] 1. Fix the file
- [ ] 2. Review it whole
- [ ] 3. Work the phases
- [ ] 4. Report
```

### 1. Fix the file

The file is the one the user named. Ask when that isn't clear.

### 2. Review it whole

Run every pass in `~/.cursor/skills/shared/review-passes.md` over the whole
file, reading the logic against the file as it stands rather than a diff. The
whole file is what's under review, however old the code in it, so only what
belongs to a caller or a sibling is an aside.

### 3. Work the phases

Take the findings one phase at a time, as `~/.cursor/skills/shared/phases.md`
sets out.

### 4. Report

- Offer the held stylistic fixes as one commit, if any are waiting.
- List what was fixed, each with whether it was committed.
- List the phases the user dropped, and the asides about callers and siblings.
