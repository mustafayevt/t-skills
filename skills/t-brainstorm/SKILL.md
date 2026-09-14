---
name: t-brainstorm
description: Use only when the user explicitly invokes t-brainstorm to clarify a product or technical change before implementation; inspect context, explore options, and finish with agreed decisions without writing code or a specification.
---

# t-brainstorm

This skill is user-invoked only. Do not run it merely because a task sounds
creative or complex, and do not invoke another phase automatically when it
finishes. Host syntax, available tools, and model names vary; use whatever the
host makes available and describe actions in portable terms.

## Purpose

Turn an intended change into a shared understanding that another phase can
specify and build. Explore the user's goal, relevant repository or code
context, behavior, alternatives, trade-offs, feasibility, constraints, edge
cases, and a useful definition of success.

## Working method

1. Read the relevant conversation, repository instructions, files, tests, and
   recent history that are in scope. Keep exploration proportional to the
   change; do not scan unrelated projects or invent context.
2. State what you understand and identify the decisions that materially affect
   the design. Separate facts observed in the context from proposals.
3. Ask only meaningful questions, one at a time when a response is needed.
   Choose routine defaults transparently when they do not materially change
   the outcome. Do not use a fixed question count or an exhaustive ceremony.
4. Offer useful alternatives when there is a real choice. For each, explain
   the important trade-offs and technical feasibility, then make a
   recommendation where appropriate.
5. Cover behavior, interfaces or data contracts, failure and edge cases,
   constraints, non-goals, and acceptance signals at the depth the change
   needs. Mark each item as observed, proposed, agreed, or unresolved.

## Boundaries

- Do not implement code, edit application files, create a specification, or
  otherwise write project artifacts during this phase.
- Do not silently turn a proposal into an agreement. Record material unknowns
  instead of guessing over them.
- Do not require another installed skill, a particular tool, a particular
  model, a worktree, or a fixed project layout.

## Finish

Stop with a concise decision record containing:

- the agreed goal and behavior;
- binding constraints and non-goals;
- the chosen approach and important rejected alternatives;
- edge cases and acceptance signals;
- unresolved questions that block or do not block the next phase.

If the outcome is sufficiently clear, suggest that the user explicitly invoke
`t-spec` next. A suggestion is not an invocation: stop and wait for the user.
