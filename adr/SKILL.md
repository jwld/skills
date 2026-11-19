---
name: adr
description: >-
  Rules for an architecture decision record (ADR): what earns one, where it
  goes, its MADR template, and how a later decision supersedes it. Use when
  writing or superseding an ADR, or when the user names one to check.
---

# ADR

An ADR records one architecturally significant decision and why it was made,
for a reader who arrives later and wonders why the code is the way it is. The
format is [MADR](https://adr.github.io/madr/) 4, the maintained successor to
Nygard's Context / Decision / Status / Consequences record.

Two ways in:

- **Writing.** A decision is being recorded or superseded. Apply the rules
  below to it.
- **Reviewing.** An ADR or the decision directory was named. Judge each record
  against the rules below. Edit a `proposed` one. An accepted one is immutable,
  so its shortfalls go in the report, and a decision that has since changed is
  offered as a superseding ADR (**Status and change**). Report as
  [shared/doc-report.md](../shared/doc-report.md) sets out.

## What earns one

- A decision that shapes structure, a quality attribute (security,
  availability, performance), a dependency with lock-in, a public interface, or
  how the system is built (framework, tooling, process).
- It is costly to reverse, there were real alternatives, and a reader of the
  code alone would guess wrong about why. A decision missing any of the three
  belongs in a commit message or a comment.
- Asked to write one for a decision that falls short, say so before writing:
  which test it fails, and where it belongs instead. Write it only if the user
  still wants it.
- One decision per ADR. A second decision that falls out of the first gets its
  own record, linked from the first.

## Where it goes

- Use the repo's existing decision directory, and match the template its ADRs
  already use even where it differs from the one below (a log that mixes
  formats is harder to scan than either format alone).
- With no ADRs yet, create `docs/decisions/` and point at it from the root
  `AGENTS.md` as the `review-docs` skill sets out, so later work finds it.
- Name the file `NNNN-title-with-dashes.md`: the next number after the highest
  in the directory, four digits, lowercase title. Numbers are never reused, not
  even a rejected ADR's.

## Template

```md
---
status: proposed
date: YYYY-MM-DD
decision-makers: {who made the call}
---

# {Imperative title naming the choice and the problem it solves}

## Context and Problem Statement

{Two or three sentences of fact: the forces at play and the question being
decided. Link the issue.}

## Decision Drivers

* {a quality, constraint or concern the options are judged against}

## Considered Options

* {option 1}
* {option 2}

## Decision Outcome

Chosen option: "{option 1}", because {justification traced to the drivers}.

### Consequences

* Good, because {…}
* Bad, because {…}

### Confirmation

{How conformance is checked: a test, a lint rule, a review step.}

## Pros and Cons of the Options

### {option 1}

* Good, because {…}
* Bad, because {…}

## More Information

{Related ADRs, confidence in the decision, and what would trigger a revisit.}
```

- Context, Considered Options, Decision Outcome and Consequences are the core
  of every ADR. The other sections go in when they carry something.
- Pros and Cons earns its place when a rejected option is likely to be proposed
  again, so the next person finds why it lost.
- `date` is when the decision was last updated. `date` and `decision-makers`
  are left out when unknown rather than guessed.

## How it reads

- The title names the chosen option and the problem ("Use Postgres for the
  order write model"), so the directory listing reads as the decision log.
- The rationale is the user's, or what the session established. Ask for a
  driver, option or consequence you don't have rather than supplying a
  plausible one.
- Context states facts in neutral language. The argument for the choice starts
  at Decision Outcome.
- Judge options on lasting qualities of the system. The effort to build one on
  the current branch, or how large that branch already is, is never a driver or
  a justification.
- At least two options, each one the team could actually have picked.
- Consequences include the costs: the long-term ones, and those that land on
  operations and maintenance as well as on development.
- Every claim can be backed by evidence or a link. An adjective that can't be
  backed comes out.
- Record why, not how. Component design, code and more than one diagram per
  option belong in a design doc the ADR links to. One or two pages at most.
- Each "Good, because" and "Bad, because" finishes as a full clause.
- A decision taken in doubt says so in More Information, with what would
  trigger a revisit.

## Status and change

- `proposed` until the user has made the call. `accepted` when writing up a
  decision they already made.
- `rejected` keeps the record, with the reason, so the question isn't reopened
  blind.
- An accepted ADR is immutable. A changed decision is a new ADR whose context
  says what changed and links back. The old one's status becomes
  `superseded by ADR-NNNN`, and nothing else in it is edited.
- `deprecated` marks a decision that no longer applies with nothing replacing
  it.

## Sources

- [Nygard, "Documenting Architecture Decisions"](https://cognitect.com/blog/2011/11/15/documenting-architecture-decisions)
- [MADR 4 templates](https://github.com/adr/madr/tree/4.0.0/template)
- [AWS Prescriptive Guidance, ADR process](https://docs.aws.amazon.com/prescriptive-guidance/latest/architectural-decision-records/adr-process.html)
- [Zimmermann, "How to create ADRs — and how not to"](https://ozimmer.ch/practices/2023/04/03/ADRCreation.html)
