# tandem

**Two AI engineers (any LLMs) collaborate on a software problem — coordinating only through shared files.**

`tandem` is an [Agent Skill](https://github.com/vercel-labs/skills). It turns two separate
AI agents into a disciplined engineering pair that goes **problem → agree → solution → plan
→ build** with explicit, automatic handoffs, a tree-lock to stay out of each other's way, and
mutual code-grounded review. The two agents can be different models; they never assume shared
chat context — everything happens through a small set of tracking files.

## Install

```bash
npx skills add <owner>/<repo>
```

Then invoke it in your agent: `/tandem` (or say "work with the other AI on this").

## How it runs

1. **Setup handshake** — asks the human for the agent's *name*, requires a `problem.md`
   (stops and asks if missing), and settles coding practices (your file → `docs/` →
   `CLAUDE.md`/`AGENTS.md`/`.cursor` → infer from the codebase).
2. **Orient** — reads `handoff.md` first: first mover starts and hands off; a joiner
   announces itself so the partner can coordinate.
3. **Work in order** — understand & *agree* on the problem against the real code → write the
   **solution** together → then the **plan** → then **build** (prefer **one branch**) → mutual
   sign-off.
4. **Handoffs** — every handoff is an explicit "@partner: do X, then Y" with specifics. A
   ~1-minute loop (or manual re-invocation) drives the back-and-forth until done.

## Files it uses (in a shared folder)

| File | Purpose |
| --- | --- |
| `handoff.md` | Live state: lock holder, lanes, parallel-mode, append-only log. Read first. |
| `status.md` | Narrative progress per agent. |
| `review.md` | Peer review, both directions. |
| `decisions.md` | Key decisions + rationale. |
| `solution.md` / `plan.md` | The agreed design, then the agreed plan. |
| `<name>.md` | Each agent's **private** scratchpad (the other never edits it). |

Copy-paste stubs are in [`templates/`](templates/); a worked two-agent walkthrough is in
[`EXAMPLE.md`](EXAMPLE.md).

## Why it works

Hard-won rules are baked in: don't implement ahead of your partner's acknowledgment; only the
tree-lock holder has uncommitted changes; never `git add -A`; independently verify the
partner's claims; small committed slices; idle and stop cleanly.

## License

MIT — see [LICENSE](LICENSE).
