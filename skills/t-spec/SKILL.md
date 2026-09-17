---
name: t-spec
description: Use only when the user explicitly invokes t-spec to write or update a complete implementation specification at a slug-specific path under docs/t-specs, preserving decisions, progress, and explicit unknowns.
---

# t-spec

This skill is user-invoked only. Do not run it as an automatic follow-up to
brainstorming, and do not invoke a build phase when it finishes. It works as a
standalone skill: use the local template at `assets/spec-template.md` when it
helps, but do not depend on another skill, a repository-root file, a particular
tool, or a particular model. Host syntax varies, so express repository and
editing actions in the host's available terms.

## Inputs and scope

Inspect the relevant conversation, repository instructions, existing code and
tests, and any existing spec at the requested slug. Capture all agreed
product and technical decisions; do not impose an arbitrary token limit or
drop requirements for brevity. Write only the requested specification path
and its containing directories.

Use a supplied brainstorming handoff as an input and preserve its distinctions
between agreed decisions, agent-chosen defaults, observed facts, unverified
assumptions, and unresolved blockers. In a fresh conversation, do not pretend
prior decisions are available: if a missing handoff contains information
needed for the specification, ask the user to supply it. Continue without one
when the current request and repository evidence are sufficient.

If the user has not supplied a slug, ask for one or use a clearly stated,
stable slug derived from the agreed feature. If the existing spec contains
useful progress, preserve it. When a decision invalidates prior work, reopen
the affected tasks explicitly rather than blindly resetting every checkbox.

## Required document shape

Create or update `docs/t-specs/<slug>/spec.md` with these sections when
applicable, in this order:

1. `Progress` at the top: `State` (`Draft`, `Ready`, `Building`, `Blocked`,
   or `Complete`), `Active task`, `Resume notes`, and the base commit captured
   at build start for the final diff. Use `Not captured` before a build starts.
2. `Tasks` at the top, using stable identifiers such as `T1`, with checkboxes
   and outcome-sized descriptions.
3. `Goal and context`.
4. `Requirements`, distinguishing binding requirements from implementation
   suggestions.
5. `Non-goals`.
6. `Architecture and decisions`, using brief labels or subheadings for the
   applicable decision statuses. Distinguish a binding agent-chosen design
   choice from a flexible implementation detail when material; omit empty
   categories.
7. `Contracts` for externally visible behavior, data, interfaces, or formats.
8. `Edge cases`.
9. `Acceptance criteria`.
10. `Verification`.
11. `Task details` at the bottom. Define every task using a heading such as
    `### T1 — title`, then include scope, dependencies, acceptance, and
    verification. Keep identifiers stable across edits.

Optional sections are welcome when they clarify the work; do not add empty
boilerplate. Make the shared spec format concrete enough for a separate build
phase to execute without rereading the brainstorm, while keeping suggestions
visibly distinct from requirements.

## Completeness pass and finish

Before stopping, perform one consistency and completeness pass: check that
requirements, decisions, contracts, edge cases, acceptance criteria, task
details, and verification agree; remove placeholders and contradictions; and
mark material unknowns as unresolved. A `Draft` or `Blocked` state is required
when an unresolved high-impact fact or choice could invalidate the design;
do not invent an answer to make the document `Ready`. Routine reversible
defaults and non-blocking unknowns do not prevent readiness when contracts and
acceptance remain complete.

Stop after the pass. Report the path, state, active task, and material
unresolved decisions. If the document is ready, suggest that the user
explicitly invoke `t-build`; do not invoke it automatically.
