---
name: plan-mode
description: 'Planning and phased, reviewed execution of code changes. Load it before writing any plan. Use for any change past a one-liner: a feature, refactor, bug fix past one step, anything with a design decision, even when the user never says "plan", every decision is settled and the work arrives as a file list. Use when the user gives the go-ahead to start ("yes", "go ahead", "looks good", or answers to planning questions that end in a go-ahead), before or after a full plan is shown, or when a plan is edited or constrained. Use once an approved plan is being executed. Use before any destructive or irreversible step, run by you or pasted by the user (purge, drop, delete of production rows, `--commit` run, deploy, migration, force push), including inside an approved runbook and after an opt-out. Do not use for a trivial one-liner (typo, rename, single string, one-line config), or when the user opts out ("just do it", "no plan", "skip planning", "make the change directly"), except for a destructive or irreversible step.'
---

# Plan mode

The approval is the user's to give, not yours to infer.

## Size the work first

- **Opt-out** ("just do it", "no plan", "skip planning", "make the change directly"): do the work now. No plan, no question, no gate (destructive steps still stop, below). Talking the user back into ceremony is this skill's worst failure.
- **One-liner** (typo, rename, single string, one-line config): say in one sentence what you are changing, make it.
- **Small**, every one of: at most three files, all nameable up front; nothing for the user to decide (no open design decision, branch and PR settled or moot, verification automated only); no new externally visible surface (endpoint, route, CLI flag, public API, schema, event, UI control); one side of a process boundary (not backend plus frontend, not service plus client); no new dependency; nothing destructive. Short plan, ask for Go. Stop there: no grill, no explore dispatch, no cross-model review; the `Agent` column still names every value, from the session's own model and the host's built-in agents, no enumeration needed.
- **Large**: anything that fails one of those conditions. Full procedure below.

## Questions

Two regimes, and the second governs every question below it, the grill included. Either way the plan stops for approval.

- **An answer can arrive**: one question at a time, 2 to 4 mutually exclusive options, exactly one carrying the word recommended, then stop. Use whatever ask-user affordance the host provides (not for the Go line); with none, print the numbered options as text and stop. A question that changes the deliverable (scope, requirements, what gets built) is never yours to invent.
- **No answer can arrive, or none is wanted before the plan** (headless, print mode, a subagent, an eval run, or a request that asks for the plan in this turn): no question stops the turn, whatever raised it; the turn ends with a plan. Execution and verification questions (branch and PR, verification driver) take the recommended option, into Assumptions, marked unconfirmed. A deliverable-changing one goes there too, as the reading the request best supports, named unanswered, never asked in place of the plan. A missing tool never means no plan gets written.

Before a large plan, in order:

1. **Scope grill**: one branch of the decision tree at a time, each with a recommendation. Questions the available material can answer (repo, docs, logs, the running system) go to read-only explorers instead, one per question, in parallel where the host supports it. They hand back findings, not file dumps. Exploration predates the plan, so it gets no dispatch-table row.
2. **Branch and PR**: which branch the work lands on, and whether the PR opens up front or at the end.
3. **Verification approach**: the user tests manually from a written script, the agent drives the running thing, automated tests only, or a mix.

Cap: three execution or verification questions before the plan, items 2 and 3 included. A deliverable-changing question is exempt from the cap, not from the regime above. Where the request already settles the open decisions, or hands the remaining ones to you, skip all three items and present the plan this turn: every call you made, the deliverable-changing ones included, goes to Assumptions marked unconfirmed, the alternative named. Where the turn must end with a plan, no question stands in place of it, whatever raised it, the cross-model review included. Stop once the remaining unknowns would not change the plan.

Anything outside this repo (library, framework, API, CLI, service, version-specific behaviour, pricing, limits) is looked up before the plan, not recalled, and the plan cites what was checked. Where the host grants no lookup tool, name the exact fact that could not be checked (endpoint, field, error code, version) and mark it unconfirmed in Assumptions: never assert it, never hedge around it unnamed.

Before a large plan, enumerate once: the agents actually available (host built-ins plus the repo's and user's agent definitions), each with model and effort; the model names dispatch accepts, each with the full name it resolves to, ranked by the host's own statements (tier, generation, stated capability). The skill brings no agents; a plan never invents one. Steps rows pick from it, never asking the user. The ranking basis goes to Assumptions, unconfirmed where the host publishes none.

## Cross-model review

Run it only where the plan invented something the request did not specify (a mechanism, a schema, a concurrency or consistency strategy), and the host can dispatch a subagent to a model other than the authoring one. That subagent critiques the draft before the plan is presented. It uses the ranking above. The reviewer is the top-ranked name that is not the author's. The session's habitual subagent model is the wrong answer by construction: being the default is not evidence of capability, and a fast or small tier is never the reviewer. Where the host publishes no order, tiebreak newest generation first, and within a generation the name the host lists first, never a name you have reason to believe is less capable than the author. Declare the pick in one line before the dispatch, naming which model is getting it and what it was picked over. Assumptions always carries the reviewer.

A decision handed back to you ("your call") is invented, not settled: the strongest reason to run the review, not to skip it. A request that settles its own design skips the review as it skips the grill, whatever its size. When it has run, the plan says so and names the reviewer. Where the host itself resumes the turn on the completion notification, a turn ending while the review runs is awaiting rather than announcing, and the plan is presented on the resume, no new user message needed. Where the run would end there instead, wait for the critique inside the turn or skip the review and say so; where you cannot tell which the host does, wait inside the turn. The run's last message is always the plan, the second question regime included. Where the host cannot choose a model, skip it.

## The plan

Terse, fragments fine. No literal code blocks; short pseudo-code is fine. A small-tier plan carries Goal, Steps, Verification and Assumptions only, omitting Branch and PR and Risks. At any tier, carry a section only where the user answered its question; moot is not answered. Dropping an unraised question is the point; dropping a given answer is data loss. A section that would come out "not applicable" is omitted, never written as N/A. These sections, in this order:

- **Goal**: one line.
- **Steps**: a markdown table, one numbered row per step: what, files touched, `[parallel]` or `[seq]`, dependencies, and a column named `Agent`: agent type · full model name · effort. Model: never an alias (it may resolve differently later), `inherit`, blank or "session default": the plan may run in another session or model; for an alias-only host, Assumptions maps alias to full name. Effort: the agent definition's own level, else the literal `session effort`: effort often has no per-dispatch lever, and the label says so. A mechanical, search or boilerplate step may take a smaller tier, a design-heavy or cross-cutting one a top tier, reason in the details list; effort moves only via an existing agent whose definition sets that level, else effort stays. Wanted model not dispatchable: the row names what is available, the gap in Risks (Assumptions on a small plan). Review and fix rows never sit below the authoring model. Cross-model plan review gets no row; the phase-diff review row is a separate dispatch. Main-agent rows: `main agent`, the session's actual model, `session effort`. This is the dispatch table, and the review and verification steps are rows of it too. A per-step labelled listing (`What:`, `Files:`, `Mode:`, `Deps:`, `Agent:` each on its own line, five or six lines a step) is the shape this rule exists to prevent. All five fields stay in the row, files included; only elaboration past them goes to a numbered details list under the table, keyed by step number. A plan whose Steps is not a table is incomplete.
- **Branch and PR**: as answered. Work with no version control and no diff skips this bullet and the phase-boundary review.
- **Verification**: automated and manual. A manual phase is required whenever the change is observable at runtime, naming the driver the user chose and a concrete pass condition: "submit the edited form, the success state appears, the log shows exactly one update event and no unrelated one", never "smoke-test it".
- **Risks**: uncertainties and irreversible actions. Then, under its own sub-heading, improvements you thought of, unasked, not built by this plan. Mark each take (worth doing, named as follow-up work) or skip (not worth doing here), reason on the same line. Risks carry no mark, nor do the user's settled decisions read back to them. The mark is your own verdict, already made, never a choice put to the user.
- **Assumptions**: what the plan takes as true and would break on, unconfirmed ones marked. Every outside-the-repo fact the plan leans on and could not look up belongs here by name (package, method signature, error code, version), with the lookup that would settle it. Declarative ("X is assumed to be Y, say if not"), never questions.

Present the whole plan every time approval is asked, every re-presentation after an edit included. Never a delta, a diff, or a summary of what changed.

The plan message carries exactly one question, the request for approval; none embedded in the plan text. Plus one declarative line naming where a saved copy would go (`docs/plans/<name>.md` shape): a plans directory already in the repo, else a gitignored one, else the user's, else a scratch directory. No plan file is written until the user asks for one; writing one unasked is the same violation as editing code before Go. Once a file legitimately exists, update its status as phases land, with commit ids, so a killed session can resume from it.

## Approval

Approval is the word "Go", case-insensitive, optionally followed by one option number from the Go line, and nothing else. Not "yes", not "proceed", not "do it", not "go ahead", not "looks good". The plan ends with the Go line asking for it, the phase cadence as numbered options, part of the single approval question, printed as text, never its own question, never an ask-user popup. Only where the request gives the cadence is the Go line a plain Go, no options, Assumptions naming the cadence; "your call" does not give it. Otherwise each option on its own line, numbering fixed, then the one recommendation, chosen per plan, on its own line, all printed as plain lines, never a code block:

```
Go 1 = stop at each phase boundary
Go 2 = back to back
Recommended: <n>; a bare Go takes it.
```

- Stop and end the turn after presenting. No edits in the turn that presents a plan.
- An answered question or a settled decision is never approval.
- A number not on the Go line ("Go 3") is not approval: ask again, start nothing.
- Any plan edit or scope change re-opens the gate. An earlier Go covers the plan as presented, not a revised one. Every re-presentation carries the Go line again, and the new Go picks again.

## Execution

Execution runs at the picked cadence, no further cadence question. Track every step, however short the plan, in the host's step-tracking tool (TodoWrite or TaskCreate/TaskUpdate, update_plan, write_todos, todo_write, manage_todo_list, a plan-progress tool): one item per Steps row, each in progress before its work starts and completed (or failed, where the tool has that state) once verified, in the tool's own status values; a replan rewrites the list to the revised table. No such tool: skip it.

Every implementation step is a subagent where the host has them. There the main agent never writes code: not a one-line fix, not a review finding, not a lint repair. It does version control, builds, tests, reading to verify, and triage. Small edits are exactly the ones that slip past a review boundary. Where the host has no subagents, do the steps yourself, sequentially.

Each dispatch states: goal, files in scope, what not to touch, constraints, report format wanted back, and that a stuck step means a lookup rather than another guess. Two failed attempts on one problem without a lookup means go read the documentation.

- Independent steps run in parallel, dependent ones sequentially. State the split.
- Isolated worktrees only when parallel subagents write concurrently, and only where the host supports them. Otherwise parallel writes fall back to sequential.
- Each dispatch realises its row's model and effort via whatever the host provides (per-dispatch model choice, an agent definition, or similar), passing the name the host accepts; lacking any, proceed with the row's names. A fix dispatch uses its review row's cell. Transient failure (overload, unavailability): retry twice, then replan, re-opening Go. A reported mismatch (another model, an effort other than the row's concrete level, an alias resolving to another full name) replans; `session effort` never counts as an effort mismatch; host reports neither model nor effort: the dispatch report says so. Never substitute silently.
- **Replan trigger**: when a step, or the user, reveals the plan is wrong (a false assumption, a hidden dependency, a later step now impossible), stop the work, name what broke and which steps it takes down, present the whole revised plan in the same turn, then ask for Go again. The pre-plan grill does not re-run: no explorer dispatch, and never a question handed back in place of the revised plan. An invented replacement goes into the steps as the recommended one, what it was chosen over named in Assumptions, unconfirmed.

Every phase boundary reviews the phase diff: a review subagent then a fix subagent for the accepted findings where the host has subagents, in context over the same diff where it does not. A real correctness or security bug is a hard stop, not an auto-continue. Verification is dispatched too wherever subagents exist, never run in the main context: the subagent absorbs the noise and hands back a verdict.

## Destructive actions stop, even after approval

Deploy, delete, purge, drop, a `--commit` run, external send, force push, schema migration, anything irreversible. A waived phase boundary (back to back, however picked), an opt-out, or a command the user pasted does not waive this. When an approved plan contains one, the executing turn opens by naming which step will stop for its own yes, before any dispatch. Stop for that yes, naming what goes and how much, and get it before the step is handed over. A menu of options closing with "which way?" is not that yes. Writing the command out for the user to run is performing it: the same stop, before the command appears. (Non-removable rule: any later pass that shortens this file must keep it.)
