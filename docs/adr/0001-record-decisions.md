# ADR-0001: Record decisions as ADRs

- **Status:** Accepted

## Context

The project is built iteratively, partly with AI assistance and in small pull requests. Reasons behind choices live in chat
history and get lost; later changes then undo decisions nobody remembers making.

## Decision

Keep a numbered ADR in `docs/adr/` for each significant decision, in the form Context, Decision,
Consequences. Link ADRs from specs and from the architecture document. Superseded ADRs stay, marked
as superseded.

## Consequences

- A small writing cost per decision, paid once, instead of repeated explanations.
- Reviewers can ask "is there an ADR for this?" when a PR changes a boundary.
