# Verify narrowly

How a skill proves its own change, without paying for a whole-workspace run.
Pointed at, never copied.

- Lint the changed files by passing their paths to `eslint` directly. A
  whole-workspace lint script takes an age for no extra signal.
- Typecheck only the workspaces the change touched. Faster than linting, but the
  same rule applies.
- Run the suites covering the changed logic where the pass judges correctness. A
  change that held behaviour steady can stop at lint and typecheck.
- In propertywerks, check a front-end change in the browser yourself, once the
  user says yes to opening it, as the repo's browser-permission rule sets out.
  Where they say no, report the check as not done. The local apps sit behind
  sign-in, so expect to pause partway: ask the user to sign in, as the browser
  sign-in rule sets out, then finish the check once they confirm. The pause
  leaves the check with you rather than handing it over.
- To make a form field's validation error show in that check, dispatch
  `focusin` then `focusout` on its input through `Runtime.evaluate`. The
  browser tool's clicks and typing don't reach React as focus, so final-form
  never marks the field visited and the error stays hidden.
