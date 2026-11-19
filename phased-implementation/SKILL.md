---
name: phased-implementation
description:
  The full flow for a piece of work, from planning through phased
  implement-review-commit to post-review follow-ups.
disable-model-invocation: true
---

# Phased Implementation

The whole life of a piece of work: plan it, then run it as an
implement-review-commit loop where the user reviews each phase before it is
committed and work continues automatically once they approve.

Each stage runs on the model that suits it. Where the repo names a planning
model, that is the main model for the whole flow, and switching to it is the
user's action: name it, ask them to switch, and carry on only once they
confirm. That switch is a hard gate: without that confirmation the flow stops
there, holding the work until the user comes back. Where the repo names none,
plan on whatever model is already set. A stage runs here when the model it
wants is already selected, and on a `Task` subagent only when it isn't.

## Workflow

```
- [ ] 1. Plan in plan mode - Fable (or Opus) on propertywerks
- [ ] 2. Ask whether to open an issue
- [ ] 3. Leave plan mode - then a new branch when the repo needs one
- [ ] 4. Implement phase on the selected model
- [ ] 5. Stop for review
- [ ] 6. Commit on go ahead, then start the next phase
- [ ] 7. Wrap up - sync the issue
```

### 1. Plan

Switch to plan mode with `SwitchMode`. Check the remote owner. Where the repo
names a planning model, wait for that model and plan mode before planning.

- **propertywerks** - Fable, or Opus. On Opus, plan without the switch gate,
  but say in the chat that it's Opus rather than Fable. On anything else, ask
  the user to switch to Fable.
- **completionism** - plan on whatever model is already set. Don't ask to
  switch.

When the work is a TypeScript conversion, ask with `AskQuestion` whether to
deep dive the files being converted first, with the `deep-dive` skill
([deep-dive/SKILL.md](../deep-dive/SKILL.md)), scoped to those files as they stand
today rather than a branch diff. Its findings are planning input: each becomes
a question in the grill about what to fix or improve in the conversion.

The deep dive runs on Opus, on the latest Opus the `Task` tool's model list
offers (`claude-opus-5-5-high` today). When planning isn't on Opus, it runs on a
subagent, briefed with the files and the skill path.

Plan with the `grill-with-docs` skill, reading it from
`~/.agents/skills/grill-with-docs/SKILL.md`. Planning is done once the user
confirms the shared understanding it reaches.

Ask each grill round through the `AskQuestion` tool, one call carrying the
whole frontier. The grilling skill's prose format is for other callers; here
the questions go in the tool and the message around it carries the findings
and the cost of each option.

- One question per decision, `q1` upwards, with the title as the `prompt`.
- Lead each question's options with your recommendation, suffixed
  `(Recommended)`. Recommend the best option on its merits, never the one
  that changes least. A smaller diff is not a reason to pick it.
- Every option is an answer the user could actually pick. The tool adds
  "Other", so leave that hatch to it.
- Set `allow_multiple` where the answers genuinely combine, such as picking
  which of several cleanups to take.

Then split the plan into phases. Each phase should be independently reviewable
and land as its own commit.

Phases and their commits do not need to leave the branch in a working state on
their own. The branch is squash-committed at the end, so intermediate commits
can break typechecks, leave front-ends out of sync with the server, or otherwise
be incomplete, as long as each one is a clear review unit.

State the phases explicitly before starting, and track them with the todo tool.
Store the plan in the user-level plans directory with `CreatePlan`, never in
the repo.

### 2. Ask whether to open an issue

Ask with `AskQuestion` before any code is written, every time. Lead with the
option [shared/issue.md](../shared/issue.md) would pick, marked `(Recommended)`.
An issue is the visible record of a plan that otherwise lives only in a
local file. Where one for this work already exists, use it instead of
asking: keep its problem and write its plan, and assign it to the user
(`gh issue edit <number> --add-assignee @me`) when they aren't on it already,
one issue as much as several.

Skip the `update-issue` call in step 7 when the answer is no, or when an
existing issue was reused and step 7 still finds nothing to sync. A one-phase
completionism change still lands as one commit on `master`.

When opening one, write the title and body as the `update-issue` skill
specifies ([update-issue/SKILL.md](../update-issue/SKILL.md)). The issue gives what the
work delivers and the PRs it ships in, with its plan bullets grouped under a
numbered phase each rather than listed flat, kept short enough for someone not
on the PR to read the shape of the work at a glance. The file-by-file detail
stays in the plan file and lands in the PR write-up.

Open it with the `open-issue` skill ([open-issue/SKILL.md](../open-issue/SKILL.md)),
which sets the type, labels and assignee.

One issue can carry work spanning several PRs, so only the last PR in its plan
resolves it. Give the number to `write-up-branch` for `Resolves #123`
on that final PR, and `Part of #123` on the earlier ones.

### 3. Leave plan mode, and open a branch

Switch back to agent mode with `SwitchMode`. Then open a branch when the repo
needs one. Check the remote owner first.

- **propertywerks** - always a new branch.
- **completionism** - stay on `master` when the work is one phase; it gets
  pushed there. More than one phase gets a new branch.

Branch off `master` unless the work belongs on an existing branch. If that isn't
obvious, ask. Name a new branch as [shared/branch-name.md](../shared/branch-name.md) sets
out.

If already on a branch for this work, keep it.

### 4. Implement phase

Do the work for the current phase only. The current phase's todo is the unit of
done: mark it complete once the phase is verified, and leave later-phase todos
pending.

An instruction to finish every todo, keep going, or implement the whole plan
applies to this phase only. Cursor injects that on plan confirm.

Implement on whatever model is selected. Hand a job to a subagent via the
`Task` tool only when it is purely mechanical, such as a find and replace across
files, on the latest Grok its model list offers (`grok-4.7-high-fast` today).
Anything that needs reasoning stays on the selected model.

When a subagent does a job:

- Brief it in full. It can't see the planning discussion, so spell out the
  edit, the files to touch, and the existing code to pattern-match against.
- Use a foreground subagent, and tell it not to commit (commits happen in
  step 6).

Planning, verification, review and commits all stay on the main model. Judge
the phase by its diff, not a subagent's summary.

Verify before handing over for review, as [shared/verify.md](../shared/verify.md)
sets out.

### 5. Stop for review

End the turn here for review. In the same message:

- One or two sentences on the changes made.
- Where the phase deviates from the plan, or the code is unusual, a section
  headed `Deviations` saying what and where.
- The steps the user has to take before the phase works (usually a database
  write such as a schema push), as a numbered list, or say there are none.
  Leave out manual checks of the result.
- End on `Phase 3 / 4`: this phase, out of how many the plan has.

Do not commit at this point. The next work is their review, not the next phase.

### 6. Commit on go ahead

Once the user gives the go ahead, commit the phase with the `commit` skill
([commit/SKILL.md](../commit/SKILL.md)).

After committing on a go ahead, automatically start work on the next phase,
returning to step 4 without waiting for a further prompt. A bare `/commit` or
"commit" is not a go ahead: commit, then end the turn, and start the next phase
only once the user says to.

### 7. Wrap up

The wrap-up gate is that every phase is committed. Stay on step 6 while a
phase is only in the working tree.

Once the last phase is committed, however the commit was asked for (a bare
`/commit` included), follow the `update-issue` skill
([update-issue/SKILL.md](../update-issue/SKILL.md)) in the same turn, without asking,
so the issue's plan matches how the work turned out. Skip this when there is
no issue. Then say the work is done and whether the issue changed.
