# Passing a body to gh

How a skill hands an issue, PR or comment body to `gh`. Pointed at, never
copied.

- Build the body inside the command, never as a file written with the
  file-writing tool. Every file that tool writes lands in the user's review,
  and deleting it afterwards doesn't take it out.
- Feed it on stdin from a quoted heredoc, which keeps backticks, apostrophes
  and `$` intact:

```
gh pr create --base master --title "..." --body-file - <<'EOF'
The body, as markdown.
EOF
```

- `--body-file -` reads stdin for `gh issue create`, `gh issue edit`,
  `gh issue comment`, `gh pr create`, `gh pr edit` and `gh pr comment`.
  `gh api` takes `-F body=@-` instead.
- Run the heredoc directly, never inside `$(...)`. Nested there, the shell
  misparses an apostrophe in the body and the command fails.
- A body built from the current one (an edit that keeps everything else) reads
  it with `gh issue view --json body` first, then writes the whole body back the
  same way.
