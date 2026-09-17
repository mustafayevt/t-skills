# JSON output for `todo list`

## Progress

- **State:** Ready
- **Base commit:** Not captured
- **Active:** None
- **Resume notes:** Spec approved by the user; no blockers.

## Tasks

| ID | Title | Depends on | Status | Commit |
|----|-------|------------|--------|--------|
| [T1](tasks/T1.md) | Add `formatTodosJson` serializer with tests | — | pending | |
| [T2](tasks/T2.md) | Wire `--json` into the `list` command | T1 | pending | |

## Rulings

None yet.

## Goal and context

`todo list` prints a human table. Scripts that consume the CLI need stable
machine-readable output. The CLI uses `commander` (`src/cli.ts:12`) and
commands live in `src/commands/*.ts`. Tests use `vitest`.

## Requirements

### Binding requirements

- `todo list --json` prints a JSON array to stdout, one object per todo.
- Each object has exactly `id` (number), `title` (string), `done` (boolean).
- An empty list prints `[]`.
- TypeScript strict mode; no `any`.
- Output to stdout only; errors to stderr with exit code 1.
- Existing table output stays byte-identical when `--json` is absent.

### Implementation suggestions

- `JSON.stringify(value, null, 2)` for readability; compact output is
  acceptable if consumers are scripts only.

## Non-goals

- No `--json` for other commands.
- No schema versioning field in v1.

## Architecture and decisions

**Agreed decisions**

- Serialization lives in its own module so other commands can reuse it.

**Agent-chosen defaults**

- Field order `id, title, done` (binding: consumers may diff output).
- Two-space indentation (flexible).

**Observed facts**

- `Todo` type at `src/model.ts:4` is
  `{ id: number; title: string; done: boolean; createdAt: Date }`.
- `createdAt` is not required by the consumer and is excluded.

## Contracts

- Output: `Array<{ id: number; title: string; done: boolean }>`.
- `createdAt` and any future internal fields are never emitted.
- Exit code 0 on success, including the empty list.

## Edge cases

- Empty list with `--json` → `[]` and exit 0.
- Titles containing quotes or newlines → escaped by `JSON.stringify`.

## Acceptance criteria

- `todo list --json` on a store with two todos prints a two-element array
  matching the contract.
- `todo list` output is unchanged (existing snapshot test passes).

## Verification

- `npm test` → all green.
- `npm run build` → no type errors.
