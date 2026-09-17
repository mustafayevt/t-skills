---
name: t-brainstorm
description: Use only when the user explicitly invokes t-brainstorm to turn an idea into an agreed design before implementation; explores the codebase, asks one question at a time, compares approaches, and ends with a design handoff without writing code or a specification.
---

# t-brainstorm

User-invoked only. Do not start it because a task sounds complex, and do not
start another phase when it finishes. Host syntax, tools, and models vary;
use what the host provides and describe actions in portable terms.

## Purpose

Turn an idea into a design the user has approved and a later phase can
specify and build: goal, behavior, approach, contracts, edge cases, non-goals,
and acceptance signals. Explore through dialogue, not a questionnaire.

## Hard rule

Do not implement code, scaffold files, edit application files, or write a
specification during this phase. The only file this skill may write is the
handoff described under **Finish**.

## Step 1 — Explore before asking

1. Read what is in scope: the request, repository instructions, relevant
   files, tests, and recent history. Keep it proportional to the change.
2. Never ask a question the code can answer. Investigate first.
3. Verify before claiming. Any statement that something exists or is missing
   (a table, route, flag, dependency, helper) must be checked in the code;
   otherwise label it an unverified assumption.
4. If the user corrects your understanding of the code, verify the
   correction against the code before building on it.

## Step 2 — Size the work and say so

State the size out loud so the user can override it:

- **Small** — a well-scoped change to a flow that already exists in the repo
  (a flag, a small endpoint, a one-file fix). Ask only the questions that
  matter, present a short design in chat, get a yes. A spec is optional.
- **Full** — new subsystem, new project, or a change that alters interfaces
  others depend on. Run the full process below and hand off to `t-spec`.
- **Too big** — several independent subsystems. Do not refine details yet.
  Help the user split it into sub-projects, agree on order, and brainstorm
  the first one.

When in doubt, take the heavier path. Hidden complexity discovered mid-way
upgrades the size; nothing downgrades it.

## Step 3 — Understand the idea

- Open by stating what you understood and what you observed in the code,
  with file references, then ask what the user already has in mind before
  offering your own ideas.
- Ask **one question per message**. Prefer multiple-choice when the options
  are clear; open-ended when the answer needs the user's own observation.
- Ask the highest-impact gap first: purpose, users, constraints, success
  criteria. Leave routine parameters for later or decide them yourself.
- Do not re-ask a decision the conversation already settled.
- After each substantive answer, follow its consequences: what it resolves,
  what it implies, whether it conflicts with earlier answers or the code.
  Follow up only when the result could change behavior, contracts,
  feasibility, failure handling, or acceptance.
- A short answer is not a request for less depth. Keep going; do not ask
  permission to continue after every reply.
- Park unrelated ideas in a **Deferred** list instead of widening scope.

## Step 4 — Explore approaches

When there is a real choice, propose **2–3 approaches**:

- Describe each in two or three sentences with trade-offs, key risks, and
  when it fits best. Include at least one non-obvious angle.
- Present all options first, then give your recommendation and why.
  Leading with the recommendation anchors the conversation.
- Keep approaches at the mechanism level (what is built, how pieces relate),
  not implementation minutiae (column names, file paths). Those belong in
  the specification.
- Apply YAGNI: strip anything the goal does not need from every option.
- If one approach is clearly right, say so directly and skip the menu.

## Step 5 — Present the design

Once you can describe what will be built:

- Present it in sections scaled to their complexity: a few sentences when
  straightforward, a short paragraph or two when nuanced.
- Cover architecture and components, data flow, interfaces or contracts,
  error handling, edge cases, non-goals, and how success is verified.
- Ask after each section whether it looks right. Revise and continue.
- Design for isolation: each unit has one purpose, a clear interface, and
  can be understood without reading its internals.
- In existing code, follow established patterns. Include targeted
  improvements to code the change touches; do not propose unrelated
  refactors.
- Before the last section, run an **integration check**: combine everything
  agreed so far and raise any non-obvious consequence the dialogue has not
  covered (for example, "if X and Y, then Z silently loses state").

## Decisions and authority

- A substantive product or architecture choice needs a user decision or
  discretion the user explicitly delegated. When delegated, pick a sensible
  conservative option and label it agent-chosen.
- Choose routine reversible implementation defaults yourself and say so.
- Do not turn a proposal into an agreement silently. Do not label a
  rationale as agreed when the user did not state it.
- Record an unresolved high-impact fact or choice as a blocker instead of
  inventing an answer.
- If the user asks for speed or no more questions, stop asking, fill only
  the gaps you are authorized to decide, and disclose every choice.

## Finish

The design is ready only when behavior, contracts, feasibility, failure
handling, and acceptance signals rest on repository evidence, a user
decision, an authorized agent-chosen decision, or a routine default, with no
unresolved high-impact blocker. The user stopping the conversation does not
make it ready. Say plainly whether it is.

Write the handoff, then self-review it once: no placeholders or "TBD",
no contradictions between sections, no requirement that could be read two
ways, and a scope that fits one specification. Fix inline.

Handoff contents, omitting empty categories:

- agreed goal and behavior;
- agreed decisions, binding constraints, and non-goals;
- agent-chosen defaults, marking each as a binding design choice or a
  flexible implementation detail;
- observed facts with file references;
- unverified assumptions;
- chosen approach and important rejected alternatives with the reason;
- edge cases and acceptance signals;
- deferred ideas;
- unresolved blockers and non-blocking unknowns.

Delivery:

- **Small** work: put the handoff in chat.
- **Full** work: write it to `docs/t-specs/<slug>/handoff.md` using a short,
  stable slug you state, and summarize it in chat. Create only that file
  and its directories. The handoff must be sufficient for a separate
  specification phase without rereading the discussion.

If the design is ready, suggest that the user invoke `t-spec` next. A
suggestion is not an invocation: stop and wait.

### Handoff example (abbreviated)

```markdown
# Handoff — offline-sync

## Goal and behavior
Queue mutations while offline and replay them in order when connectivity
returns. Conflicts are resolved last-write-wins per record.

## Agreed decisions
- Queue persists in IndexedDB; in-memory only is rejected (data loss on reload).
- Replay is sequential; no batching in v1.

## Non-goals
- No server-side merge; no UI for conflict inspection.

## Agent-chosen defaults
- Retry with exponential backoff, max 5 attempts (flexible).
- Queue entry shape `{ id, op, payload, createdAt }` (binding: consumed by T2 and T3).

## Observed facts
- `src/api/client.ts:41` already wraps fetch; sync hooks in here.
- No existing offline detection; `navigator.onLine` is unused.

## Edge cases and acceptance
- App reloads mid-replay: unreplayed entries survive and resume.
- Two offline edits to one record: later timestamp wins, earlier is dropped.

## Unresolved
- None blocking. Unknown: whether the mobile wrapper exposes `navigator.onLine`.
```
