# Plan — [problem slug]

## Branch strategy

[Prefer one shared branch. If not, explain why separate branches/worktrees are required.]

## Slices

| Slice | Owner | Files | Verification | Handoff trigger |
| --- | --- | --- | --- | --- |
| 1. [name] | [agent] | [files] | [commands] | [specific trigger] |

## Execution rules

- Build only after problem + solution + plan are agreed (the agreement gate — applies per lane too).
- Update `handoff.md` before edits; claim the edit lock when the tree is clean.
- One reviewable commit per slice; never batch slices; typecheck/test before each commit.
- Stage only owned files (never `git add -A`); leave a clean tree before handoff.
- Ask for concrete peer review in `review.md`.
