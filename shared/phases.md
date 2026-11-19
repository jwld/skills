# Phases

How a local review takes its findings one fix at a time, every fix and every
commit waiting on the user's go-ahead. Pointed at, never copied.

## Before the first fix

Note any uncommitted changes already in the files in scope
(`git diff -- <paths>`), so no commit later sweeps them in with a phase's fix.

## Lay out the phases

- One phase per problem, most consequential first. Track them with the todo
  tool: the phases are done when every one is ticked, not when the user stops
  asking.
- Simple stylistic fixes (a rename, a reordered import, a stale comment) share
  one phase rather than taking one each.
- List the phases in a line each, so the user sees the shape of what's coming
  before the first one opens. Then carry straight on to phase one.

## Present the phase

- The problem: where (the file and function, with line numbers), what's wrong,
  and what it causes.
- The fix. Offer more than one only where they differ in something the user
  would weigh (behaviour, scope, a trade-off), with your recommendation first
  and why in a clause.
- Your questions, if you have any. Ask them with the phase and wait for the
  answers.
- Once there's nothing left to ask, confirm the fix you'll make and ask for the
  go-ahead. Wait for it, even where the fix looks obvious.

## Fix it on the go-ahead

- Make the agreed fix and nothing beyond it. Something else you spot while
  fixing becomes a new phase, not part of this one. The edits the fix forces
  elsewhere (updating the references to a rename, say) are part of it.
- Verify as `~/.cursor/skills/shared/verify.md` sets out, running the suites
  that cover the logic when the fix changed behaviour.

## Offer the commit

- A fix that changed behaviour, or one large enough to review on its own: offer
  a commit for it now, with the title you'd use. Wait for the answer.
- A simple stylistic fix: hold it, and offer the held fixes as one commit at the
  end of the review.
- On a yes, commit only this phase's change, following the `commit` skill
  (`~/.cursor/skills/commit/SKILL.md`). Where a file had changes before the
  review, stage the phase's hunks alone.
- On a no, leave it in the working tree and move on.

## Next phase

Tick the phase off and open the next one, saying which it is. The user can
reorder, merge or drop the phases still to come.
