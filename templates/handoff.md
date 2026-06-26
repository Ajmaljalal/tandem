# Handoff Tracker — [problem slug]

Read this first every cycle. This is the live state source of truth. The tables below are
**live** — keep them current (edit/supersede stale rows). The Handoff Log is **append-only**.
On a reopened session, normalize these tables to reflect *now* before doing any work.

## Current Ownership

| Field | Value |
| --- | --- |
| Current owner | [Agent A / Agent B / Human / unassigned] |
| Current action | [what is happening now] |
| Tree/edit lock holder | [agent name / FREE] |
| [Agent A] state | [state] |
| [Agent B] state | [state] |
| Parallel mode | [yes/no + constraints] |
| Watcher ([Agent A]) | [id + 1-min cadence + ACTIVE until complete; restart first if stopped; downgrade cadence + flag human if partner stalls; if 1-min impossible, stop and ask human] |
| Watcher ([Agent B]) | [id + 1-min cadence + ACTIVE until complete; restart first if stopped; downgrade cadence + flag human if partner stalls; if 1-min impossible, stop and ask human] |
| Last updated by | [agent name] |
| Last updated at | [date/time/timezone] |

## Active Lanes

In parallel mode, every lane needs its own row with an explicit owner, exact file set, and
branch/worktree/repo — and still clears the agreement gate before that lane builds.

| Lane | Owner | State | Branch / worktree / repo + exact files | Next trigger |
| --- | --- | --- | --- | --- |
| Problem verification | [agent] | [state] | docs + read-only code | [specific handoff trigger] |

## Shared Working Tree Rule

- The lock holder is the only agent allowed to have uncommitted changes.
- Stage only owned files. Never use `git add -A` or `git commit -am`.
- If parallel mode is enabled, each agent must list its exact file set here first.

## Closeout Check (all must hold before declaring complete)

- [ ] No Active Lane is still in-progress / "changes requested"
- [ ] Every live row cites the same current branch HEAD (no two rows citing different commits)
- [ ] Each agent's watcher state is accurate; each stopped its own (or logged a pending cleanup)
- [ ] Human follow-ups (deploy / recovery / merge) listed separately from finished agent work
- [ ] Final mutual sign-off posted in `review.md`

## Handoff Log

Append-only. **Each agent's first line here is its arrival announcement:** name, whether it is
the first mover or a later joiner, and what it will do next, written the moment it finishes
reading this file and before any code work, so the partner knows immediately that someone showed up.

- [date/time] [agent]: [arrival announcement on join; thereafter handoff summary + explicit ask to partner]
