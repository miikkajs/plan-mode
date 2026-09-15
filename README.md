# plan-mode

An agent skill that makes a coding agent agree the shape of the work before it touches code, then execute it in phases, a subagent per step, with a review at each boundary.

## Why

- You describe a change and the agent starts editing. By the time you see the first diff, a design decision you cared about has been made for you, in a file you did not expect, and unwinding it costs more than the change was worth.
- "Looks good" gets taken as consent. An agent that reads reactions as approval will eventually read one as approval that was never meant as any.
- The plan drifts under execution. Small edits are exactly the ones that slip past a review boundary, so what ships is not what was agreed.
- One agent writes, reviews and verifies its own work in a single context, so the edits arrive as one undifferentiated pile and the small ones are never looked at by anybody.
- Ceremony lands on a request that was already settled, and the work gets planned twice over instead of done.

## What it does

- Sizes the work first, so a trivial request stays trivial and an opt-out means the work happens now.
- Grills the open questions before writing a plan, and sends the ones the repo can answer to explorer agents rather than to you.
- Writes the plan in a fixed shape every time, so you read it the same way twice.
- Takes approval as one literal word, Go. Nothing else starts the work, and any edit to the plan re-opens the gate.
- Hands every implementation step to a subagent under a brief (goal, files in scope, what not to touch, the report wanted back), leaving the main agent version control, builds, tests and triage rather than code of its own.
- Treats the plan's Steps table as the dispatch table: independent steps in parallel, dependent ones in order, the split stated in the plan. A host without subagents runs the same steps in order in the main context.
- Executes in phases, reviewing each phase's diff at its boundary, with the review, the fixes and the verification dispatched out too.
- Stops separately at a destructive step (deploy, delete, external send, force push, schema migration), for its own yes after Go.

The rules themselves live in `plugins/plan-mode/skills/plan-mode/SKILL.md`, one markdown file: packaged as a Claude Code plugin, and read directly by OpenAI Codex, GitHub Copilot and Cursor.

## Install

### Claude Code

From GitHub:

```
claude plugin marketplace add miikkajs/plan-mode
claude plugin install plan-mode@miikkajs
```

From a local checkout, for development (the marketplace is named `miikkajs` and holds the one plugin, `plan-mode`):

```
claude plugin marketplace add /path/to/plan-mode
claude plugin install plan-mode@miikkajs
```

### Codex, Copilot, Cursor

There is no plugin mechanism to hook into on these, so installing means putting the one file where the host looks for it. All three want it in a directory named after the skill, with the file itself called `SKILL.md`, and take the skill name from that directory rather than from the filename.

| Host | Where the file goes |
| --- | --- |
| OpenAI Codex | `.agents/skills/plan-mode/SKILL.md` |
| GitHub Copilot | `.github/skills/plan-mode/SKILL.md`. Copilot also reads `.claude/skills/` and `.agents/skills/`, so a Codex install is picked up as it stands |
| Cursor | `.agents/skills/plan-mode/SKILL.md`. Cursor also reads `.cursor/skills/`, `.claude/skills/` and `.codex/skills/` |

Codex, Cursor and Copilot all read `.agents/skills/`, so one install covers all three. Symlink it and a `git pull` updates every host at once:

```
skill=/path/to/plan-mode/plugins/plan-mode/skills/plan-mode/SKILL.md

mkdir -p .agents/skills/plan-mode
ln -s "$skill" .agents/skills/plan-mode/SKILL.md
```

Those paths are per project; a host that also reads a user-level skills directory takes the same file in the same shape there. `.cursor/agents/`, `.claude/agents/` and `.codex/agents/` are not skill directories: the file parses there, but registers as a subagent to dispatch rather than as a skill the main agent loads, which inverts a skill whose whole point is telling the main agent what to do.

## Limits

- It is written against all four hosts, but has only been exercised on Claude Code. Its behaviour on Codex, Copilot and Cursor rests on those hosts having the affordances the skill names.
- It is prompt text. A model can read it and still do something else, and nothing here enforces the gate at the tool layer. It shifts the default; it is not a lock.
- The tier boundaries are judgement calls. Conditions like "nothing for the user to decide" are applied by a model to your request, and a request near the line can land on either side of it.

## Licence

MIT. See `LICENSE`.
