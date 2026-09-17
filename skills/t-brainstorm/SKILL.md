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
   change; do not scan unrelated projects or invent context. Investigate the
   available code before asking technical questions it can answer.
2. State what you understand and identify the decisions that materially affect
   the design. Separate facts observed in the context from proposals.
3. Ask about the highest-impact remaining gap, not the easiest detail. Ask one
   main question at a time when a response is needed; include a closely coupled
   subpart only when it is necessary to interpret the same decision. A brief
   answer alone does not mean the user wants less depth, and continuing the
   discussion does not require a consent question after every answer.
4. Follow the consequences of each substantive answer. Identify what it
   resolves and implies, check it against earlier answers and repository
   evidence, and follow up when the result could change user-visible behavior,
   contracts, feasibility, safety, failure handling, or acceptance. Prefer
   those questions over routine parameters.
5. Offer useful alternatives when there is a real choice. For each, explain
   the important trade-offs and technical feasibility, then make a
   recommendation where appropriate.
6. Walk through concrete success, long-running or interrupted, and failure
   cases when they expose different behavior. A mechanism does not establish a
   guarantee until those sequences support it.
7. Cover behavior, interfaces or data contracts, failure and edge cases,
   constraints, non-goals, and testable outcomes at the depth the change needs.
   Choose routine reversible implementation defaults transparently. A
   substantive product or architecture choice requires a user decision or
   explicitly delegated discretion; when delegated, choose a sensible
   conservative option within that scope and identify it as agent-chosen.

If the user requests speed or no more questions, respect that request. Stop
asking, fill only the gaps you are authorized to decide, and disclose the
choices. Record an unresolved high-impact fact or choice as a blocker instead
of inventing an answer. Unverified factual assumptions are not evidence or
agreement.

## Boundaries

- Do not implement code, edit application files, create a specification, or
  otherwise write project artifacts during this phase.
- Do not silently turn a proposal into an agreement. Record material unknowns
  instead of guessing over them.
- Do not require another installed skill, a particular tool, a particular
  model, a worktree, or a fixed project layout.

## Finish

The user may stop the conversation at any time; stopping does not make the
design ready. Say in normal prose whether it is ready. It is ready only when
the material behavior, contracts, feasibility constraints, failure handling,
and testable outcomes are supported by repository evidence, a user decision,
an authorized agent-chosen decision, or a routine reversible default, with no
unresolved high-impact fact or choice that could invalidate the design.

Stop with a concise, standalone handoff containing only the applicable items:

- the agreed goal and behavior;
- agreed decisions, binding constraints, and non-goals;
- agent-chosen defaults, distinguishing a binding design choice from a
  flexible implementation detail when material;
- observed facts and relevant evidence;
- unverified assumptions;
- the chosen approach and important rejected alternatives;
- edge cases and acceptance signals;
- unresolved blockers and any non-blocking unknowns.

Do not infer or label a rationale as agreed when the user did not state it.
Omit empty categories rather than forcing a fixed report shape. The handoff
must be sufficient for a separate specification phase without rereading the
discussion, while remaining concise.

If the outcome is sufficiently clear, suggest that the user explicitly invoke
`t-spec` next. A suggestion is not an invocation: stop and wait for the user.
