# Tn — [title]

Template for `docs/t-specs/<slug>/tasks/Tn.md`. The worker sees only this
file, so it must stand alone.

## Context

[One paragraph: what this task delivers and where it fits.]

## Constraints and contracts

[Copied verbatim from spec.md: only the requirements, contracts, and edge
cases this task must honor.]

## Dependencies

[Task IDs with the exact names and signatures they produce, or None.]

## Files

- Create: `exact/path`
- Modify: `exact/path:lines`
- Test: `exact/test/path`

## Interfaces

- Consumes: [exact names and signatures, or None]
- Produces: [exact names, parameters, return types later tasks rely on]

## Steps

1. [Concrete action; include code when the exact shape matters]
2. [Concrete action]

## Tests

- `test_name` — [behavior asserted]; run: `[command]`

## Acceptance

- [Observable task outcome]

## Verification

- `[command]` → [expected result]
