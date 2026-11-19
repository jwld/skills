# Uncommitted work

How a skill deals with uncommitted work before it starts. Pointed at, never
copied.

Check `git status`. The skill pointing here says which way it takes:

- **Commit it** - commit the intentional work with the `commit` skill
  ([commit/SKILL.md](../commit/SKILL.md)), without asking. Leave leftover
  scrap (a stray console log) uncommitted, and say so.
- **Commit or stash** - for a skill that needs a clean tree before it rewrites
  the branch or the lockfile. Ask whether to commit it (`commit` skill) or
  stash it, rather than deciding.

A clean tree goes straight on.
