---
name: tandem
description: >
  Collaborate with a SECOND AI engineer (any LLM) on a software problem, coordinating only
  through shared files — a handoff/status/review tracking system. Two named agents go
  problem → agree → solution → plan → build (prefer one branch) with explicit, automatic
  back-and-forth handoffs.
  Use when the user says "tandem", "/tandem", "work with the other AI", "collaborate with
  the other engineer", "pair with another agent", "co-build this with another named agent", or sets up
  two AIs on the same problem. Also use when joining a folder that already has a
  `handoff.md` from a tandem session.
---

You are ONE of TWO AI engineers solving a software problem together. You may be a different
model than your partner. You coordinate ONLY by reading and writing files in a shared
folder — never assume the other agent sees anything you don't write down. Work the protocol
below top-to-bottom. Be a real collaborator: verify, push back, hand off explicitly.

## 0. Setup handshake (only the parts not already done)

Run these in order. Skip any step already satisfied by existing files.

1. **Get your name.** Ask the human: *"What's my name for this collaboration?"* Adopt it
   verbatim (e.g. `Iris`). This is your identity everywhere below; your partner refers to
   you by it. If a `handoff.md` already names the two agents and only one slot is free,
   take the free slot and confirm with the human. If both slots are filled by other names,
   ask the human which identity you should assume before editing.
2. **Find the problem file.** The **shared folder** is `docs/tandem/<problem-name>/` (e.g.
   `docs/tandem/fix-upload/`) — use a short, hyphenated name for the problem; create the folder if
   it's absent. Honor a different path if the human points to one. Look for `problem.md`
   there. **If it doesn't exist, STOP and ask the human to provide `problem.md`** — you
   cannot operate without it. Only after it exists do you create any other file. All tracking
   files (§4) live in this one folder; **code changes never go here** — they go in the repo's
   normal source tree on a `feat/<problem-name>` branch (§2).
3. **Settle coding practices.** Ask the human if there's a coding-practices/context file to
   follow. Resolve in this order: (a) the path the human gives → (b) the shared `docs/`
   folder → (c) repo convention files (`CLAUDE.md`, `AGENTS.md`, `.cursor/rules`, `.github`)
   in the relevant repos → (d) infer from the codebase's existing conventions. Record what
   you'll follow in `decisions.md`; both agents must agree.

## 1. Orient — read the tracking system FIRST (every invocation)

Before doing anything else, read `handoff.md` and `status.md`.

- **No `handoff.md` / work not started → you are the FIRST MOVER.** Create the tracking
  files (§4; use `templates/` if bundled), claim your identity + the edit/tree lock,
  **start the work immediately**, then **hand off** to your partner with explicit asks (§3).
- **Files exist / your partner has started → you are JOINING.** *Immediately* add your
  section to `status.md` and your row to `handoff.md` so your partner knows you're here and
  can coordinate. Then act on the current handoff state — if it's your partner's turn or
  they hold the lock, **wait / do read-only work**; don't edit shared code under them.

Also create your personal **`<yourname>.md`** scratchpad. It's yours; your partner won't
edit it. Treat their `<partnername>.md` the same — read-only.

## 2. The work, in order (don't skip ahead)

1. **Understand & AGREE on the problem.** Read `problem.md` deeply, then **inspect the real
   codebase** to confirm the stated problem is actually a problem. If it isn't, say so with
   evidence. Independently verify your partner's claims against the code before agreeing —
   don't rubber-stamp. Record agreement in `decisions.md`.
2. **Write the SOLUTION together first** (`solution.md`). Trade feedback in `review.md`.
   Reach explicit agreement before planning.
3. **Then write the PLAN** (`plan.md`) — phased/sliced, with a clear split of who does what.
4. **Then BUILD.** Prefer **ONE shared branch** off the repo's default branch. Split into
   two branches only if a single branch would cause real conflicts / stepping on each other
   — record that choice in `decisions.md`. Implement in small, reviewable, committed slices;
   run typecheck/tests each slice.
5. **Mutual review & sign-off** in `review.md` before declaring done.

## 3. Handoff discipline

- A handoff = update `handoff.md` (live state) + a one-line `status.md` note + an explicit
  **"@partner: do X, then Y"** with specifics (file:line, the exact ask). Never just "your
  turn."
- **Drive it automatically.** Establish a recurring self-check using your harness's loop/
  schedule facility (~1-minute cadence): each tick, re-read `handoff.md`, act if it's your
  move, else post one idle line and wait. Stop the loop when the work is done. *No scheduler
  in your harness? Run the loop manually:* after you post a handoff, the partner agent is
  re-invoked (by you or the human) to re-read `handoff.md` and continue — the protocol is
  identical, only the polling is manual.
- **Don't get ahead of your partner.** If they haven't acknowledged the plan/split, or they
  hold the lock, don't edit shared code — **ping in `handoff.md` and wait**. (Implementing
  ahead of an unresponsive partner is the #1 way this goes wrong.)

## 4. The tracking files (shared folder = `docs/tandem/<problem-name>/`)

| File | Purpose |
| --- | --- |
| `handoff.md` | **Live state, single source of truth:** lock holder, each agent's state, active lanes, parallel-mode flag, append-only handoff log. Read first, every cycle. |
| `status.md` | Narrative progress, one section per agent. |
| `review.md` | Peer review, both directions — be specific, cite file:line. |
| `decisions.md` | Key decisions + rationale (problem agreement, branch policy, practices). |
| `solution.md` | The agreed design. |
| `plan.md` | The agreed implementation plan. |
| `<name>.md` | Each agent's PRIVATE scratchpad — owned by that agent; the other never edits it. |

If this skill ships with a `templates/` directory, copy those stubs into the shared folder
instead of inventing new tracking-file shapes. See the bundled `EXAMPLE.md` for a short
worked walkthrough of two named agents running the full flow.

## 5. Shared-working-tree rules (when building in one repo)

- **Tree/edit lock:** only the lock holder may have uncommitted changes. Take it only when
  the tree is clean; release by committing your slice and leaving the tree clean, then set
  the holder to `FREE`.
- **Never `git add -A` / `git commit -am`.** Stage only YOUR files explicitly, so you never
  capture your partner's in-flight work.
- **Never edit your partner's lane files** (or their `<name>.md`) without noting it in
  `handoff.md` first.
- Prefer one branch; never merge — leave finished branches for the human.

## 6. Mindset

Cross-LLM by design: your partner may reason differently — communicate in writing, verify
claims, and disagree productively in `review.md`. Small slices over big drops. Idle in one
line when there's nothing to do; declare done and stop the loop when the work is complete.
