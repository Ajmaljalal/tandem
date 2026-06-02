# Tandem Example

This example shows the expected rhythm, not a required transcript.

## Setup

The human creates `docs/fix-upload/problem.md` and starts two agents.

- Agent 1 name: `Iris`
- Agent 2 name: `Mika`
- Shared folder: `docs/fix-upload/`
- Branch: `feat/fix-upload`

## First mover

Iris starts first:

1. Reads `problem.md`.
2. Creates `handoff.md`, `status.md`, `review.md`, `decisions.md`, `solution.md`,
   `plan.md`, and `Iris.md` as empty stubs from templates. `solution.md` and `plan.md`
   are filled in during their protocol phases.
3. Claims the tree lock in `handoff.md`.
4. Reads repo practices from `AGENTS.md` and package scripts.
5. Inspects the upload code and records evidence in `solution.md`.
6. Hands off:

```markdown
@Mika please review:
1. Whether the problem is real based on `src/upload.ts:120`.
2. Whether the proposed solution should preserve upload sessions or restart them.
3. Whether one branch is safe for this work.
```

## Joining agent

Mika joins:

1. Reads `handoff.md` and `status.md` before editing.
2. Creates `Mika.md`.
3. Adds a `status.md` section so Iris knows Mika is active.
4. Reviews Iris's claims against the code.
5. Adds review notes in `review.md`.
6. Updates `decisions.md` with the agreed problem and coding-practice decisions.

## Solution before plan

Iris and Mika iterate in `review.md` until `solution.md` is agreed.

Only then do they create `plan.md`:

| Slice | Owner | Files | Verification | Handoff trigger |
| --- | --- | --- | --- | --- |
| Preserve sessions | Iris | `src/upload.ts`, upload tests | focused tests | Mika review |
| UX copy | Mika | `src/UploadPage.tsx`, page tests | focused tests | Iris review |

Because the files are mostly disjoint but the branch is shared, they serialize edits on one
branch using the tree lock.

## Build and review

Iris implements the first slice, commits it, releases the lock, and asks Mika to review
specific failure cases. Mika reviews, approves, claims the lock, implements UX copy, commits,
and asks Iris to review.

## Completion

Both agents record final verification in `status.md` and mutual sign-off in `review.md`.
`handoff.md` ends with:

```markdown
Current owner: Human / complete
Tree/edit lock holder: FREE
Current action: work complete; awaiting human merge/review
```
