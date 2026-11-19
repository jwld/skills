---
name: test
description: >-
  Writes or extends the spec for a subject so every branch it takes has a case
  named after the condition it checks, and reviews an existing spec against
  the repo's testing docs. Use whenever writing or extending any test,
  including as part of other work, and when the user runs /test or asks to
  review an existing test file against the latest conventions.
---

# Test

Write the spec for one subject so that every branch it can take has a case, and
each case reads as the condition it proves.

Running `/test` is the go-ahead for a spec on the subject the user named, and
work that calls for tests is the go-ahead for a spec on the code it changes.
Ask before adding one for anything else.

This skill is the procedure only. How a spec is laid out, named, grouped and
asserted comes from the repo's testing docs, so hold every case to those rather
than to anything here.

Two ways in:

- **Writing.** The user ran `/test`, or other work calls for a test. Follow
  the workflow below.
- **Reviewing.** The user named an existing spec, or asked for one to be
  checked against the latest conventions. Ask for the file when that isn't
  clear. Read the testing docs as step 1 sets out, judge the spec's layout,
  naming, grouping and assertions against them, and edit it to match. A missing
  branch stays a writing pass: name it in the report rather than adding the
  case. Then run the spec as step 4, and report as step 5.

## Workflow

```
- [ ] 1. Pin down the subject and read the rules
- [ ] 2. Map the branches
- [ ] 3. Write the spec
- [ ] 4. Verify, coverage included
- [ ] 5. Report
```

### 1. Pin down the subject and read the rules

- The subject is the file or function the user named, or the one the work's
  test is for. Ask when that isn't clear.
- Read the testing docs the root `AGENTS.md` points at, and every nested
  `AGENTS.md` on the subject's path. In propertywerks that is
  `docs/conventions/testing.md`, plus `projects/server/test/AGENTS.md` for a
  server spec.
- Read an existing spec beside the subject, so the harness seeds and calls
  things the way the repo already does.

### 2. Map the branches

List every outcome a caller can drive the subject to before writing a case:
each early return, each throw, each arm of an if-else chain, and the
fall-through at the end. Check each against its callers (the middleware in
front of it, the code that writes or deletes the rows it reads), since a
condition no caller can produce gets no case. The list is done when each
reachable one has a case against it:

- Every clause of a compound condition, flipped on its own while the others hold
  the value that lets it decide. `isOwner && isApproved && !hasSigned` is three
  cases, not one. Where two checks always fail together in practice (an
  unauthenticated request never carries an app), one realistic case covers
  both, rather than a faked state that isolates either.
- Every parallel branch, with the same set of cases where each is reachable.
  When one app's lookup has not-found, archived and test-account cases, so does
  every other branch that runs those checks, except a not-found case for a
  model nothing deletes.
- The pass path of each branch, as well as its rejections.
- A check passed as an option rather than written as a branch (an allow-list
  handed to a library), which coverage won't flag as missing.

A branch no caller can reach (a request the middleware never passes on, a row
nothing in the code deletes) gets no case and stays uncovered. Where it passes
rather than rejects, ask whether the subject should fail closed there, and leave
its behaviour alone until the user answers.

### 3. Write the spec

One case per outcome on the list, built and written as the testing docs set
out. Two cases that run the same fixture down the same path are one, so keep
the one in the group that owns the branch.

### 4. Verify, coverage included

- Run the spec, then lint and typecheck as [shared/verify.md](../shared/verify.md)
  sets out.
- Measure branch coverage of the subject alone, with the command the testing
  docs give for it.
- An uncovered branch is a missing case, an unreachable one from step 2, or a
  step 2 question.
- For a check passed as an option, remove the check, run the spec, confirm its
  case fails, then restore it. Run those steps one after another, never in
  parallel, or the run can see the restored file and read as a pass.

### 5. Report

- The subject's branch coverage, and any branch left uncovered with the reason.
- Each gap raised in step 2, and what the user chose.
