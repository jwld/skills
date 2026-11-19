---
name: walkthrough
description: >-
  Walks the user through the code on a finished branch, explaining how it works
  and flagging where the PR description has drifted from it. Use when the user
  runs /walkthrough or asks to be walked through a branch or PR.
disable-model-invocation: true
---

# Walkthrough

An explanation of the branch as it now stands, for someone about to read or
review it. It describes working code, not a review, so don't hunt for faults,
though say when something is worth a second look.

## Scope

- Work from the net diff vs the parent, gathered as
  [shared/net-diff.md](../shared/net-diff.md) sets out.
- Fetch the connected PR for reference:
  `gh pr view --json number,title,body,url,baseRefName,state`.
- Read the current files around the diff, not only the diff itself. A
  walkthrough explains the code as it now reads, so the surrounding context and
  the call sites matter.
- Treat the PR description as a claim to check, never as the source of truth.

## Output

Prose in complete sentences, with a bold run-in heading per file. No bullet list
of paths.

**Lead** and **Areas** - follow the `orient` skill
([orient/SKILL.md](../orient/SKILL.md)). Its output opens the walkthrough as
written, and the file sections below do the explaining it leaves out.

**File sections** - the rest of the walkthrough, one section per file, already
in reading order. Don't propose an order or justify it, and don't caveat that
another order would work; just start at the first file and keep going.

The user reads top to bottom and marks each file as reviewed on GitHub as they
go, so the sections have to line up with that flow: every path in the diff
appears exactly once, nothing is skipped however trivial, and the walkthrough
ends when the last file is ticked off. Where a file needs no real review, say so
in a sentence rather than leaving it out.

Work the order out silently, as the **Order** in
[shared/reading-order.md](../shared/reading-order.md) sets out, then:

- Where the details make no sense without the shape of the feature, open the
  lead with a skim of the assembly file, say explicitly that it's a skim and
  what to look for, and leave the file's own section at the end where it gets
  ticked off.
- Group a file with its satellites (a `style.ts`, a barrel) into the section for
  the file they serve rather than giving them one of their own, but name and
  link each satellite there so it can still be ticked off.

**Closing section** - where the PR description no longer matches the code, if
anywhere.

Under each file, explain what the code does and why it was done that way,
drawing the reasoning from the description where it gives one. Quote only
load-bearing code (the lines whose absence would change behaviour) in a
`startLine:endLine:path` reference block with a repo-relative path, and check
the line numbers against the current file rather than the diff hunk.

## Linking

Hyperlink every file mentioned, using its absolute path:

<!-- prettier-ignore -->
```md
The shared [`Marker`](/Users/me/repo/packages/components/src/Marker/index.tsx) gained an optional `onClick`.
```

- Link the identifier or the bare filename, not the full path.
- Folders are plain text (folder hyperlinks don't work).
- Backticks inside the link text.

## Reconciling the PR description

Check each claim in the description against the diff, and close the walkthrough
with anything that has drifted. Two things it's usually worth checking:

- Work the description claims which isn't in the diff, because it landed
  separately in the meantime.
- Details of the UI or the behaviour that changed after the description was
  written.

Say plainly that the description needs updating. Don't repeat its narration back
where it's accurate; link to it and add what it doesn't say.

## Rules

- Claim only what the code shows.
- Point out anything a reader would want flagged: a guard that lives in the
  render rather than the logic it protects, behaviour that's deliberate but
  reads like a bug, a comment carrying a constraint that isn't obvious.
- Explain a decision once, in the file where it bites.
- No hard-wrapped lines, and short hyphens rather than em dashes.
