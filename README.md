# Skills

jwld's personal agent skills, loaded by Cursor from `~/.cursor/skills`. This
file is the map: what lives where, how the pieces reach each other, and how to
change one safely.

## Requires mattpocock/skills

Some of these skills build on [mattpocock/skills](https://github.com/mattpocock/skills),
installed locally into `~/.agents/skills`. Where that install is missing, read
each one from its upstream link instead.

| Here | Uses from mattpocock/skills |
| --- | --- |
| `phased-implementation` | [`grill-with-docs`](https://github.com/mattpocock/skills/blob/main/skills/engineering/grill-with-docs/SKILL.md) to plan, overriding the question format of [`grilling`](https://github.com/mattpocock/skills/blob/main/skills/productivity/grilling/SKILL.md) |
| Changing a skill (below) | [`writing-for-agents`](https://github.com/mattpocock/skills/blob/main/skills/productivity/writing-for-agents/SKILL.md) |

## Layout

- `<name>/SKILL.md` is one skill. Its frontmatter `description` is the
  trigger and the source of truth for when it runs; the index below only
  groups them.
- A skill may carry sibling files it discloses behind a pointer (for example
  `packages-major/take.md`, read only once the user says to proceed).
- `shared/` holds fragments several skills follow. They are not skills: no
  frontmatter, never triggered on their own, reached only through a pointer
  in a skill. Each opens with "Pointed at, never copied."

## Invocation

- **Model-invoked**: the agent reads the skill whenever its description
  matches the request, including from inside another skill.
- **Slash-only**: `disable-model-invocation: true` in the frontmatter. Run it
  only when the user types `/<name>`, or another skill explicitly hands off
  to it.

## Index

Slash-only skills are marked `/`.

| Area | Skills |
| --- | --- |
| Git and branches | `commit`, `push`, `backup-work`, `rebase`, `merged-branches`, `worktree-window`, `/land-first`, `/squash` |
| Issues and PRs | `open-issue`, `update-issue`, `open-pr`, `update-pr`, `write-up-branch`, `meeting-notes`, `address-review`, `/review-pr`, `/changes` |
| Reading a finished branch | `/orient`, `/walkthrough`, `/deep-dive`, `/final-pass`, `/review-file` |
| Code and docs quality | `tidy`, `test`, `review-comments`, `review-docs`, `adr`, `/documentation`, `/pattern` |
| Planning a piece of work | `/phased-implementation` |
| Dependencies | `packages-minor`, `packages-major` |
| The skills themselves | `refine-skill` |

## Shared fragments

| File | Covers |
| --- | --- |
| `base-branch.md` | The default branch, and a branch's base |
| `branch-name.md` | Where work needs its own branch (protected `master`), and naming it, including the `jack/` prefix rule |
| `choices.md` | Putting a choice to the user: a numbered choice in the chat, or the question tool |
| `doc-report.md` | The report after a pass over agent docs, comments or ADRs |
| `findings.md` | Taking review findings one fix at a time, each on the user's go-ahead |
| `gh-body.md` | Passing an issue, PR or comment body to `gh` |
| `issue.md` | Deciding whether the work gets a GitHub issue |
| `net-diff.md` | Gathering the net branch diff against its parent |
| `output.md` | Laying out what a skill writes (no hard wraps, short hyphens) |
| `pending-review.md` | Reading your pending review on a PR |
| `pr-labels.md` | The `[PR]` labels, their write-up audiences, and moving a PR between review stages |
| `public-text.md` | Showing text that posts under the user's name before it goes up |
| `reading-order.md` | The order a branch is read in, file by file or by section |
| `review-passes.md` | What a review looks for, whatever it does with the findings |
| `succinct.md` | Keeping chat messages to the user short (not GitHub write-ups) |
| `uncommitted.md` | Uncommitted work before a skill starts: commit it, or ask to commit or stash |
| `verify.md` | Proving a change with narrow lint, typecheck and test runs |

## How the pieces reach each other

- Every pointer is a Markdown link relative to the file it sits in, so it
  resolves the same from `~/.cursor/skills`, a clone, or GitHub.
- A skill points at a fragment by link and phrasing, for example "as
  [shared/verify.md](shared/verify.md) sets out". Read the fragment at that
  point and follow it as part of the step.
- A skill hands off to another by name and link, for example "with the
  `commit` skill ([commit/SKILL.md](commit/SKILL.md))". Read that skill and
  run it in full; its own rules apply, including any pause for the user.
- Chains run deep. `open-pr` reaches `write-up-branch`, `commit`, `push`,
  `open-issue` and `update-issue`; `update-pr` reaches most of the same.
  Follow each hand-off as you reach it, not up front.

## Changing a skill

- Find everything that depends on a file before editing it:
  `rg "shared/verify.md"` or `rg "commit/SKILL.md"`. A change to
  `commit`, `verify.md`, `net-diff.md`, `base-branch.md` or `uncommitted.md`
  lands in many skills at once.
- Keep each rule in one place. When two skills need the same rule, move it to
  `shared/` and link it from both rather than copying it.
- Write in the style of `writing-for-agents` from mattpocock/skills
  (`~/.agents/skills/writing-for-agents/SKILL.md`); use `refine-skill` when
  the change comes from what a run taught.
- Commit with the `commit` skill, and only when the user asks. The remote is
  `jwld/skills` on GitHub.
