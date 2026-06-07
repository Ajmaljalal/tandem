# tandem

**Two AI engineers (any LLMs) collaborate on one software problem, coordinating only through shared files.**

`tandem` is an [Agent Skill](https://github.com/vercel-labs/skills). It turns two separate AI
agents into a disciplined engineering pair that moves through problem, agreement, solution,
plan, and build with explicit, automatic handoffs, a tree lock so the two stay out of each
other's way, and mutual code-grounded review. The two agents can be different models. They
never assume shared chat context. Everything they say to each other is written into a small
set of tracking files in one shared folder.

## What tandem is

tandem is a way to put two AI agents on the same software problem and have them behave like a
real engineering pair instead of two people guessing at each other. Each agent has a name, a
role, and a partner. They read and write a shared set of files, hand work back and forth with
explicit asks, verify each other's claims against the real code, and only declare the work
done after both sign off.

Two rules are non-negotiable:

- **Collaboration is mandatory. Neither agent works solo.** No agent agrees on the problem,
  picks a solution, writes the plan, or builds a slice on its own. Every step goes to the
  partner first and waits for agreement. Acting ahead of your partner is the single most
  common way this goes wrong, so the protocol forbids it.
- **The watcher is the only communication channel, and it must never stop.** The two agents
  do not share a chat. The only way one reaches the other is by writing to the shared files
  and reading them back on a fixed schedule. That schedule is the watcher. It runs while the
  task is active and never stops on its own. If it is ever interrupted or stopped, for example
  because you interrupt the agent, re-triggering the watcher is the first thing the agent does
  on its next turn, before any other work. See [The watcher](#the-watcher-is-the-collaboration-channel).

Today tandem is built for exactly two agents on one urgent problem at a time.

## Install

```bash
npx skills add Ajmaljalal/tandem
```

Then invoke it inside your agent with `/tandem` (or say "work with the other AI on this"). One
global install is enough; both agents use that same installed skill.

## How to use it

You start tandem once in each agent. The first agent sets everything up and hands off. The
second agent joins and answers. From then on the two talk only through the shared files, on the
watcher's schedule.

### The trigger

The trigger is `/tandem` followed by the context that agent needs. You can pass the context in
plain language in the same message. If you leave something out, the skill asks for it before it
starts. Passing it up front saves a round trip.

### What each agent needs (the props)

Every agent that joins a tandem needs four things:

| Prop | Required | What to provide |
| --- | --- | --- |
| Your name | Always | The name this agent answers to in the collaboration, for example `Iris`. Your partner refers to you by this name in every file. |
| Your role | Always | Either **first agent** (no tracking files exist yet) or **joining agent** (your partner already started). |
| Partner name | When joining | The name of the agent who already started, so you know whose handoffs to read and answer. |
| Problem location | Always | The shared folder that holds `problem.md`, for example `docs/tandem/fix-upload/`. Every tracking file for this problem lives in this same folder. |

### Trigger the first agent

The first agent finds no tracking files in the folder, so it becomes the first mover. It
creates the tracking files, claims its name and the tree lock, starts the watcher, begins the
work, and hands off to the partner with an explicit ask.

```
/tandem My name is Iris. I am the first agent, no one else has started yet.
My partner will be Mika. The problem is in docs/tandem/fix-upload/.
```

### Trigger the second agent (joining)

The second agent finds tracking files already in the folder, so it joins. It adds itself to
`status.md` and `handoff.md` so the partner knows it is present, starts its own watcher, then
acts on the current handoff. If it is the partner's turn or the partner holds the lock, it waits
or does read-only work instead of editing shared code.

```
/tandem My name is Mika. Iris already joined as the first agent.
The problem is in docs/tandem/fix-upload/.
```

### Where the problem document lives

One repo can hold many tandem problems at the same time. Some are finished, some are in
progress by other pairs of agents, and some have not started. Because of that, the agent cannot
guess which one you mean. You must point it at the exact folder so both agents work on the same
problem and write to the same tracking files.

The shared folder is `docs/tandem/<problem-name>/`, where `<problem-name>` is a short,
hyphenated name for the problem:

```
docs/tandem/
  fix-upload/                 problem A, in progress (Iris and Mika)
    problem.md
    handoff.md
    status.md
    ...
  harden-tandem-protocol/     problem B, done (Codex and Opus)
    problem.md
    ...
  migrate-auth/               problem C, not started yet
    problem.md
```

Always include the problem location in the trigger. Two agents pointed at different folders are
not collaborating, they are working alone.

### If the problem does not exist

If you point tandem at a folder that has no `problem.md`, the agent stops right there. It does
not create files, claim a name, or start a watcher. It tells you the problem does not exist, and
it explains what a `problem.md` should contain so you can write one:

- A plain-language statement of the problem.
- The evidence and context: file paths, symptoms, and why it matters.
- The desired outcome, or how you will know it is solved.

Add `problem.md` to that folder, then trigger tandem again.

### The watcher is the collaboration channel

The watcher is how the two agents actually talk. Each agent runs its own watcher on a 1-minute
cadence. Every tick re-reads `handoff.md` and acts if it is this agent's move, otherwise it
stays quiet or posts a single idle line.

- It is created or verified as the first operational priority, before any code edit or any
  waiting on the partner.
- It runs at exactly a 1-minute cadence. If the harness cannot create a 1-minute watcher, the
  agent stops and asks you rather than quietly using a slower one.
- It never stops on its own while the task is active. The only time it stops is at final mutual
  sign-off, when the work is completely done.
- If it is ever interrupted or stopped before that, for example because you interrupt the
  agent, re-triggering the watcher is the first thing the agent does on its next turn, before
  anything else.

Because the channel is the watcher and nothing else, neither agent decides or builds anything
solo. Every problem call, solution, plan, and slice is written down, handed to the partner, and
agreed before the work moves on.

## Files it uses

All of these live in the one shared folder, `docs/tandem/<problem-name>/`. Code changes never
go here. They go in the repo's normal source tree on a `feat/<problem-name>` branch.

| File | Purpose |
| --- | --- |
| `problem.md` | The problem statement. Must exist before anything else starts. You write this. |
| `handoff.md` | Live state and single source of truth: lock holder, each agent's state, active lanes, watcher ids, and an append-only handoff log. Read first, every cycle. |
| `status.md` | Narrative progress, one section per agent. |
| `review.md` | Peer review in both directions. Be specific, cite file and line. |
| `decisions.md` | Key decisions and the reasons behind them. |
| `solution.md` | The agreed design. |
| `plan.md` | The agreed implementation plan. |
| `<name>.md` | Each agent's private scratchpad. The other agent never edits it. |

Copy-paste stubs are in [`templates/`](templates/). A worked two-agent walkthrough is in
[`EXAMPLE.md`](EXAMPLE.md).

## The flow, in order

No step is skipped, and no agent moves to the next step before both agents agree on the current
one.

1. **Agree on the problem.** Read `problem.md`, then inspect the real code to confirm the
   stated problem is actually a problem. Record agreement in `decisions.md`.
2. **Write the solution together** in `solution.md`. Trade feedback in `review.md` until both
   agree.
3. **Write the plan** in `plan.md`, sliced, with a clear split of who does what.
4. **Build**, preferring one shared branch. Small slices, one reviewable commit each, with
   typecheck and tests run before each commit.
5. **Mutual review and sign-off** in `review.md` before declaring done.

## Why it works

Hard-won rules are baked in: collaborate on everything and never decide solo; keep the watcher
alive as the one channel between agents; don't implement ahead of your partner's
acknowledgment; only the tree-lock holder has uncommitted changes; never `git add -A`;
independently verify the partner's claims against the real code; ship small committed slices;
keep the live handoff tables current; idle in one line when there is nothing to do; and stop
cleanly only after final sign-off.

## Contributing

Contributions are welcome. tandem is developed in the open at
[github.com/Ajmaljalal/tandem](https://github.com/Ajmaljalal/tandem). Work happens through
forks and pull requests:

1. **Fork** the repo to your own account from
   [github.com/Ajmaljalal/tandem](https://github.com/Ajmaljalal/tandem).
2. **Clone** your fork and add the original repo as `upstream`:
   ```bash
   git clone https://github.com/<your-username>/tandem.git
   cd tandem
   git remote add upstream https://github.com/Ajmaljalal/tandem.git
   ```
3. **Branch** off `main` for your change:
   ```bash
   git checkout -b feat/your-change
   ```
4. **Make your change.** Keep edits to `SKILL.md`, `templates/`, `EXAMPLE.md`, and this README
   consistent with each other, since the agents read all of them.
5. **Commit and push** to your fork:
   ```bash
   git push origin feat/your-change
   ```
6. **Open a pull request** from your branch against `Ajmaljalal/tandem` `main`. Describe what
   changed and why, and link any related issue.

Before opening a PR, sync your fork with upstream so your branch is up to date:

```bash
git fetch upstream
git rebase upstream/main
```

For larger changes, open an issue first so the direction can be agreed before you build.

## License

MIT. See [LICENSE](LICENSE).
