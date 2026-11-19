# Review passes

What a review looks for, whatever it then does with the findings (a GitHub
comment, a fix, a report). Pointed at, never copied.

Read the current files around the code under review, not only the hunks: the
files it imports and the ones that import it, the untouched sibling doing the
same job, and the `AGENTS.md` that cover it. Finish every pass before
presenting anything, so the findings are ordered from the full picture rather
than the order you met them.

## Logic

Trace what each change actually does rather than skimming the diff. What tends
to be wrong:

- Props, params or attributes silently dropped when logic moved behind a
  narrower interface.
- Values whose type or nullability shifted at a boundary (`undefined` vs `null`
  vs `0`), and defaults that now reach the DOM, the API or the DB.
- Guards that no longer narrow, or narrow differently to what the author
  assumed.
- Behaviour changed by omission: a default that replaced an explicit value, a
  call site that lost an argument.
- Duplicated or dead code left behind by a move, plus now-unused exports, deps
  and barrel entries.
- Divergence from the untouched sibling (the component, helper or module the new
  code sits beside), and between call sites the change adds for the same job,
  each held against the others as well as against the sibling.
- An effect or memo whose dependency list leaves out values it reads, so when it
  runs rests on what was left out rather than on what it depends on.
- A payload sent to an outside API (an analytics event, a webhook, a
  third-party SDK) that breaks the provider's reference: a required field
  missing, the wrong unit, or a value holding what the reference says to leave
  out.

## Tidiness

Whether the code sits in the right place and reads like the rest of the repo:

- Placement: a helper, type, constant or hook belongs beside its only consumer,
  or at the lowest level covering all of them, as the `tidy` skill's **Split
  and co-locate** step sets out (`~/.cursor/skills/tidy/SKILL.md`). That holds
  even where the neighbouring files sit the other way.
- Splitting: a file doing two jobs, a component carrying unrelated logic inline,
  a module grown past what its name covers. Name what comes out and where it
  goes.
- Convention: naming, file layout, exports, state handling, error handling, and
  how the same job is done elsewhere. The untouched sibling and the nearest
  `AGENTS.md` are the authority, not your taste.
- Reinvention: a helper, type or constant the repo already has, written again
  locally.
- Scaffolding: an abstraction with a single caller, a wrapper that only
  forwards, an option nothing passes. Apply the deletion test (if removing it
  makes complexity vanish rather than reappear across callers, it was a
  pass-through).
- Readability: naming and shape, held to the **Naming** and **Shape** rules in
  the `tidy` skill's **Tidy it** step, and comments, held to the
  `review-comments` skill (`~/.cursor/skills/review-comments/SKILL.md`).

Name the precedent behind every tidiness finding: the sibling that does it the
other way, the `AGENTS.md` line, or the `tidy` or `review-comments` rule it
breaks. With no precedent behind it, it's preference, and preference isn't a
finding. Where the finding is about where a seam sits or what an interface
should hide, take the vocabulary from the repo's `CONTEXT.md` and its glossary
rather than inventing terms.

## Covering rules

Complete a rule-to-code inventory before calling the review finished. Account
for every construct in scope governed by a covering `AGENTS.md` rule, rather
than sampling the rules for likely findings. Treat API-choice rules as
mechanical checks: inventory every call to the governed API and compare its
exact method or variant with the rule. Every match is either compliant or a
finding; none remain unclassified.

## Verify before reporting

- Substantiate every finding against real source: the library in `node_modules`,
  the config or theme file, the sibling implementation, and for a payload sent
  to an outside API, the provider's published reference rather than memory of
  it.
- Check whether an apparent inconsistency is the convention elsewhere before
  calling it out.
- Run the checks in `~/.cursor/skills/shared/verify.md`, the suites covering the
  logic under review included: a review judges correctness.
- Drop anything you can't substantiate instead of hedging it.

## What counts

- Hold the code to what it introduced or made worse. Mess it merely sat next
  to, and a finding that belongs to a caller or sibling outside the scope, is an
  aside for the user, not a finding.
- Order findings by consequence. Most tidiness findings are nits; one that
  splits a source of truth in two, or that will force a circular import, is not.
- Say so when the passes found nothing. Findings don't get manufactured to fit
  the flow.
- Present the findings alone. Leave out what checked out, especially where it
  only confirms the PR description, since the user reads a section's results
  for what needs acting on.
