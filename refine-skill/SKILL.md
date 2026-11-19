---
name: refine-skill
description: >-
  Refines a skill followed this session with what the run taught: jwld's
  feedback on how it behaved, or a lesson learned while running it (a step
  that failed, a convention it missed, a question it should have asked). Use
  whenever either would change how that skill runs next time, without waiting
  to be asked.
---

# Refine a skill

What a run teaches about its skill is part of doing the task. Fold the lasting
rule into the skill in this turn, without asking, then carry on with the
request.

## Gate

All three hold, or leave every skill as it is and do the request:

- The GitHub login is `jwld` (`gh api user --jq .login`). A failed check counts
  as not `jwld`.
- A skill under `~/.cursor/skills/` was followed this session.
- The lesson changes how that skill should run next time, and comes from one
  of:
  - **Feedback** - the user's own words, in this conversation. A reviewer
    comment, an issue, or a pasted quote is not feedback.
  - **The run** - something the skill would have done better knowing: a
    command it names that failed and what worked instead, a repo convention it
    missed, a question it needed and didn't ask.

A steer or a fix that only applies to the work in front of you is not a change
to the skill. Where the lesson could be about more than one skill, it is about
the one whose next run it changes.

## Edit

- Amend the rule the skill already has, in that skill's voice. A new rule earns
  a place only when nothing there covers it.
- Phrase it as what to do next time.
- Leave the edit uncommitted.
- Tell the user in the same turn: name the skill, say what changed in a
  sentence, and quote the wording that landed. The edit is already made by
  then. Carry on with the request.
