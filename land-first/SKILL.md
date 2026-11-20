---
name: land-first
description: >-
  Moves one piece of the current branch's work onto its own branch off master,
  then opens a PR for it so it merges ahead of the branch it came from. Use
  only when the user runs /land-first.
disable-model-invocation: true
---

# Land first

Take one piece of the work on the current branch (the **source branch**), put it
on its own branch off `master`, and open a PR for it so it merges first. The
source branch keeps every commit it has. Once the new PR merges, the user
rebases the source branch onto `master` themselves.

The work runs through the `phased-implementation` skill
([phased-implementation/SKILL.md](../phased-implementation/SKILL.md)): planned, then carried over
a phase at a time, each phase committed only once the user has reviewed it.
Invoking this skill is the go-ahead for the rest: the cherry-picks, and pushing
the new branch for its PR.

## Workflow

```
- [ ] 1. Record the source branch
- [ ] 2. Pin down the piece
- [ ] 3. Branch off master
- [ ] 4. Plan it
- [ ] 5. Open the issue
- [ ] 6. Carry the piece over
- [ ] 7. Verify it stands alone
- [ ] 8. Wrap up and open the PR
- [ ] 9. Stay on the new branch and report
```

### 1. Record the source branch

- Note its name, its PR (`gh pr view --json number,closingIssuesReferences`),
  and its issue, found as the `update-issue` skill's **Find the issue** sets
  out ([update-issue/SKILL.md](../update-issue/SKILL.md)). Either may not exist.
- Before switching branches, deal with uncommitted work on the source branch
  as [shared/uncommitted.md](../shared/uncommitted.md)'s **Commit it** sets
  out.

### 2. Pin down the piece

Gather `git log --oneline origin/<default>..HEAD` and the net diff against
`origin/<default>`, as [shared/net-diff.md](../shared/net-diff.md) sets out. The piece
ends up as either whole commits or specific hunks.

- **Named** - the piece is the work the user named. Ask when it could cover
  more than one cut. Otherwise carry on.
- **Not named** - scan for candidates, below, and ask which one to land.

#### Scan for candidates

Go through every commit and every file in the net diff. Skip documentation
unless the user named it as the piece: the candidates are logic. A candidate
is work a reviewer could merge on its own, ahead of the rest:

- **Groundwork** the rest builds on: a refactor, a rename, a helper or type
  with its tests, a migration.
- **Work caught in passing**: a fix, a cleanup, a dependency bump.

The branch's core change stays behind; the candidates are what lands ahead of
it. Put every candidate in one `AskQuestion`, an option each, labelled with
what it does, whether it's whole commits or hunks, and anything it would bring
along under the check below. One lands per run. When nothing qualifies, say
why and stop.

#### Stand alone

The piece has to stand on `master` alone. Check what it uses that the rest of
the source branch adds: a helper, a type, a migration, a renamed symbol. Name
each one it leans on. Whether to bring it along, rework the piece around it, or
stop is the first thing the plan settles.

### 3. Branch off master

Switch to the new branch before planning, so the rest of the run happens on
it:

- `git fetch origin <default>`, then
  `git switch --no-track -c <name> origin/<default>`, the default branch as
  [shared/base-branch.md](../shared/base-branch.md) sets out. Without
  `--no-track` the new branch tracks `origin/<default>`, so a bare push or pull
  aims at the default branch.
- Name it after the piece, as [shared/branch-name.md](../shared/branch-name.md) sets out.

### 4. Plan it

Follow `phased-implementation`'s **Plan** step with the piece as the work, its
model gate, plan mode and grill included. The plan settles what the piece
brings along and what has to change for it to stand on `master` alone, then
splits that into phases:

- Whole commits are cherry-picked as they are, ahead of the phases. The plan
  names them, but they aren't phases.
- The phases cover the hunks and any rework the piece needs to stand alone.
  Where the piece is whole commits and nothing needs reworking, the plan has no
  phases: say so.

### 5. Open the issue

Open it as `phased-implementation`'s **Ask whether to open an issue** step
sets out, before anything is carried over. It's work under way, so assign
`@me` when one opens. Its problem is the one the piece solves, not the source
branch's.

When one opens, title it by the kind of work and what it lands ahead of, rather
than listing what it touches. TypeScript conversions for the source branch, say,
are `Convert to TypeScript ahead of <source work>`, with no list of what's
converted. Where only one file or concept is converted, name it instead:
`Convert <it> to TypeScript ahead of <source work>`. `<source work>` is the
source branch's work in a few words.

When the source branch builds on the piece, write this line under the issue's
lead, or on the PR as step 8 sets out when there's no issue:

```
In advance of #<number>
```

Point at the source PR, else its issue, else the source branch name in
backticks. Leave the line out when the piece is unrelated work that was only
written on the source branch, such as a fix caught in passing.

### 6. Carry the piece over

Leave plan mode with `SwitchMode`, staying on the branch from step 3 rather
than opening one as `phased-implementation` would.

**Whole commits** first: `git cherry-pick` them, oldest first. Resolve a
conflict only where the intent is unambiguous: one side changed, import
ordering, formatting. Stop and ask when `master` changed the same logic. Never
`git cherry-pick --skip`.

**Then the phases**, through `phased-implementation`'s **Implement phase**,
**Stop for review** and **Commit on go ahead** steps. A phase carrying hunks
writes them with the file-editing tools, reading the source side with
`git show <source branch>:<path>`, and takes only the
piece's lines from a file that also holds other work.

### 7. Verify it stands alone

Once the last phase is committed, verify the branch as
[shared/verify.md](../shared/verify.md) sets out. A phase can leave the branch
broken, but the piece has to land working. The typecheck is what catches
something the piece still needs from the source branch. A failure from that
goes back to the user: a further phase that brings it along or reworks around
it, or stop.

### 8. Wrap up and open the PR

Run `phased-implementation`'s **Wrap up** step once step 7 passes, opening the
PR before its `update-issue` call, so the issue's plan can link it.

Open the PR with the `open-pr` skill ([open-pr/SKILL.md](../open-pr/SKILL.md)).
When step 5 wrote the `In advance of` line for the PR, put it on its own line
above `## AI Summary`.

### 9. Stay on the new branch and report

Finish on the new branch. Leave the source branch's commits as they are, and
don't rebase it.

Reply succinctly, as [shared/succinct.md](../shared/succinct.md) sets out, with
the PR URL, and the issue URL when step 5 opened one, plus anything the plan
brought along from the source branch. When step 2 scanned, list the
candidates left behind. When step 1 committed, list those commits.
