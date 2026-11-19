# Naming a branch

Where work needs a branch of its own, and how a skill names one it opens.
Pointed at, never copied.

- In the PropertyWerks repo (an `origin` remote of
  `propertywerks/propertywerks`), `master` is protected, so work needs its own
  branch and a PR. Check the remote, not the owner or the branch name alone.
- Name it after the change in kebab-case, at most four words, describing the
  change rather than the phase or ticket (`crew-app-imports`,
  `decouple-next-fonts`), unless the skill gives the name.
- Prefix every branch `jack/` when the remote owner is `propertywerks`, a name
  the skill gives included (`jack/minor-package-updates`). Check
  `git remote get-url origin`, not the current branch, since a prefix on the
  branch you started from doesn't carry over.
- If the branch already exists, locally or on the remote, stop and ask what to
  do with it.
