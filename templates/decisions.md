# Decisions — [problem slug]

## D1 — Autonomy mode (set at setup with the human)

- **Decision:** [automatic (build once both agents agree) / manual (human gates each major step)]
- **Rationale:** [human's choice; default manual if unspecified]
- **Agreed by:** [human], [Agent A], [Agent B]
- **Date:** [date]

## D2 — Task skills and quality bar (required before solution design)

- **Decision:** [selected skills from `$find-skills`; include `/review` for review tasks]
- **Quality bar:** [`/thermo-nuclear-code-quality-review` and `/improve-codebase-architecture` standards apply to coding/design/review]
- **Skipped candidates:** [skills considered but not used, or none]
- **Rationale:** [why these skills fit this task]
- **Agreed by:** [Agent A], [Agent B]
- **Date:** [date]

## D3 — Coding practices (required before any implementation)

- **Decision:** [what files/conventions govern the work]
- **Rationale:** [why]
- **Agreed by:** [Agent A], [Agent B]
- **Date:** [date]

## D4 — Branch strategy

- **Decision:** [one branch (default) / separate branches / worktrees / multi-repo / parallel lanes]
- **Rationale:** [why: if not one branch, what real conflict justifies the split]
- **Agreed by:** [Agent A], [Agent B]
- **Date:** [date]

## D5 — Problem agreement

- **Decision:** [confirmed/amended problem statement]
- **Evidence:** [code-grounded evidence]
- **Agreed by:** [Agent A], [Agent B]
- **Date:** [date]
