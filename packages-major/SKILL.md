---
name: packages-major
description: >-
  Lists every major dependency update available in the repo, ordered easiest to
  hardest, with what each one actually breaks here, then takes them one PR at a
  time when asked to proceed. Use when the user asks which major updates are
  available, what the major bumps would take, what to upgrade next, or to
  proceed with / take a major update.
---

# Major package updates

Every major version on offer, ordered easiest first, each with what it would
break in this repo.

The first pass is a report: no branch, no edits, no install. Taking is a later
pass they ask for, in that order, one PR each. Follow [take.md](take.md) once
they proceed and the report is in hand.

## Workflow

```
- [ ] 1. List the majors
- [ ] 2. Size each one
- [ ] 3. Read what breaks
- [ ] 4. Order and report
```

### 1. List the majors

```
yarn updates --format group
```

The repo's own `updates` script already carries its ncu flags, and
`--format group` splits the results into patch, minor, major and
major-version-zero per `package.json`. Take the major group. From
major-version-zero, take only a bump that changes the 0.x minor or
graduates to 1+ (0.5.6 → 0.6.0 or 1.0.0).

Leave the `updates:major` script alone. It's `-i`, which implies `-u`, so it
both waits on a prompt that never comes and rewrites files while doing it.

One item per package, not per workspace (the same package shows up in every
`package.json` that declares it and has to move in all of them at once). Record
the version jump and the list of files.

### 2. Size each one

Cheap signals first, before any reading:

- **Blast radius** - how much code touches it: `git grep -l "<package>"` across
  the repo's own source, and how many workspaces declare it. No source hits at
  all means it's tooling, and its config is the surface instead.
- **Majors spanned** - one is a migration guide, four is four of them stacked,
  and a jump from `0.x` is unbounded.
- **Group** - packages that can only move together: a runtime with its
  `@types/*`, `eslint` with its plugins and configs, `jest` with `ts-jest` and
  `babel-jest`, `react` with `react-dom`. Report a group as one item at the
  difficulty of its hardest member, naming what it holds.
- **Held back** - a version capped in `resolutions`, or by a peer dependency of
  something that isn't moving. That's a blocker to state, not a difficulty.

### 3. Read what breaks

Every item gets its breaking changes checked against this repo rather than
reported in general:

- `node_modules/<package>/CHANGELOG.md` first, the project's release notes or
  migration guide second.
- A jump that spans more than one major includes every hop's breaking changes
  (15→16, 16→17, …), not only the latest notes.
- Grep the repo for each breaking change's API. A break in something this repo
  never calls costs nothing and doesn't count towards the difficulty.
- Name the call sites you find (the file, and what's there now).
- An official codemod moves an item down the order. Substantiate or drop, no
  hedging.

Every major from step 1 gets accounted for before the report (including the ones
the answer is "nothing" for).

### 4. Order and report

Easiest first. Between two of the same difficulty, the smaller blast radius goes
first.

Open with a single line batching the ones with nothing to say: the single-major
bumps whose changed APIs this repo doesn't touch, named with their version
jumps. Then an entry each for the rest:

- The package or group, and the version jump.
- One line on what makes it that hard.
- The call sites that have to change.
- Any blocker holding it.

Keep an entry to the few lines it takes to decide with. No changelog tours, no
restating a break the repo doesn't hit, no summary of what you checked and found
fine.

Close by offering to proceed, starting with the top item.
