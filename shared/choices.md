# Putting a choice to the user

How a skill asks the user to decide. Pointed at, never copied.

Either way, lead with your recommendation, marked `(Recommended)`, and make
every option an answer the user could actually pick. Then stop and wait for
the answer.

## A numbered choice in the chat

For a decision the user has to read a case or reasoning for first: a verdict
on a review point, the research behind a recommendation, why a guess leaned
the way it did.

- The case and the numbered options are one chat message, and the reply is a
  number. Thinking doesn't count, because the user never sees it.
- The options are the only numbered list in the message. Name anything else in
  prose, or the message reads as twice the options.
- Keep the case short. On Opus, which runs long, draft it, then cut anything
  the choice doesn't turn on.

## The question tool

`AskQuestion` for a quick pick that needs no case read first (which section to
start on, whether to open an issue), and for a round of several questions at
once, each needing a line of context at most.

- One question per decision, with any findings and the cost of each option in
  the message around the call.
- The tool adds "Other", so leave that hatch to it.
- Set `allow_multiple` where the answers genuinely combine.
