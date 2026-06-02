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
| Watcher ([Agent A]) | [id + cadence; if scheduler cost forces >1min, note actual cadence + reason] |
| Watcher ([Agent B]) | [id + cadence; if scheduler cost forces >1min, note actual cadence + reason] |
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

## Handoff Log

- [date/time] [agent]: [handoff summary + explicit ask to partner]
