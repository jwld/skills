# Naming a branch

How a skill names a branch it opens. Pointed at, never copied.

- Name it after the work in kebab-case, unless the skill gives the name.
- Prefix it `jack/` only when the remote owner is `propertywerks`. Check
  `git remote get-url origin`, not the current branch, since a prefix on the
  branch you started from doesn't carry over.
- If the branch already exists, locally or on the remote, stop and ask what to
  do with it.
