# The base branch

How a skill picks the branch it branches off, rebases onto or compares
against. Pointed at, never copied.

- **The default branch** is `master`, else `main`, else the remote default
  (`git rev-parse --abbrev-ref origin/HEAD`, with `origin/` stripped).
- **A branch's base** is its open PR's base (`gh pr view --json baseRefName`),
  else the default branch.
- A branch the user names wins over both.
- Fetch the base and work from `origin/<base>`, never the local branch. A
  local base that hasn't been pulled since something merged puts that merged
  work in the diff as if the branch made it.
