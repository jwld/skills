# Reading order

The order a branch is read in, whether file by file or as the sections of a
review. Pointed at, never copied.

## Order

Where the PR description has a `## Suggested review order` (the
`write-up-branch` skill writes one), follow it, since it's the path the author
chose through the change. A section or file comes in at the first step that
reaches it. The steps name modules rather than files, so order the files within
a step, and place anything no step reaches, by the rules below.

Otherwise, work it out so each piece can be understood without forward
references:

- The shared primitive or lowest-level change first, especially when it has
  consumers outside the branch and so the widest blast radius.
- Then the state, then the pieces built on it, then the code that assembles
  them.
- The assembly last, because that's where the interactions collide and where
  the decisions worth arguing about tend to live.
- Never by layer or by commit, and never by the order the diff happens to print
  them in.

## Sections

How a review of a branch splits it up and moves through it.

- The sections are the areas from the `orient` skill
  ([orient/SKILL.md](../orient/SKILL.md)). Every path in the diff belongs to
  exactly one, including the trivial ones. Generated and mechanical churn makes
  one section that gets acknowledged rather than reviewed.
- Track the sections with the todo tool. The review is done when every section
  is ticked, not when the findings dry up.
- Before the first, ask which section to start on with the question tool, as
  [choices.md](choices.md) sets out, offering the sections in the order above
  with the reason for your suggestion in a clause.
- After each, tick it off and carry straight on with the next one in order,
  saying which you're moving to. No need to wait for a prompt, and the user can
  redirect.
