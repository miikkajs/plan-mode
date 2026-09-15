---
name: plan-mode
description: 'Plan before coding, grill open questions, execute in reviewed phases. Use for any change past a one-liner: a feature, refactor, bug fix past one step, anything with a design decision, even when the user never says "plan", every decision is settled and the work arrives as a file list. Use when a plan is edited or constrained, or approval comes in words other than Go ("yes", "looks good", "go ahead"), even for a plan never presented: present the whole plan and ask for the literal word Go. Use it once an approved plan is being executed. Do not use for a trivial one-liner (typo, rename, single string, one-line config), and do not use when the user opts out ("just do it", "no plan", "skip planning", "make the change directly"): act immediately. Use before any destructive or irreversible step, run by you or pasted by the user: purge, drop, delete of production rows, `--commit` run, deploy, migration, force push. That takes its own yes after Go, inside an approved runbook, opt-out or not.'
---

# Plan mode

The approval is the user's to give, not yours to infer.

## Size the work first

- **Opt-out** ("just do it", "no plan", "skip planning", "make the change directly"): do the work now. No plan, no question, no gate. Talking the user back into ceremony is this skill's worst failure.
- **One-liner** (typo, rename, single string, one-line config): say in one sentence what you are changing, make it.
- **Small**, every one of: at most three files, all nameable up front; nothing for the user to decide (no open design decision, branch and PR settled or moot, verification automated only); no new externally visible surface (endpoint, route, CLI flag, public API, schema, event, UI control); one side of a process boundary (not backend plus frontend, not service plus client); no new dependency; nothing destructive. Short plan, ask for Go. Stop there: no grill, no explore dispatch, no cross-model review.
- **Large**: anything that fails one of those conditions. Full procedure below.

## Questions

Two regimes, and the second governs every question below it, the grill included. Either way the plan stops for approval.

- **An answer can arrive**: one question at a time, 2 to 4 mutually exclusive options, exactly one carrying the word recommended, then stop. Use whatever ask-user affordance the host provides; with none, print the numbered options as text and stop. A question that changes the deliverable (scope, requirements, what gets built) is never yours to invent.
- **No answer can arrive, or none is wanted before the plan** (headless, print mode, a subagent, an eval run, or a request that asks for the plan in this turn): no question stops the turn, whatever raised it; the turn ends with a plan. Execution and verification questions (cadence, branch and PR, verification driver) take the recommended option, into Assumptions, marked unconfirmed. A deliverable-changing one goes there too, as the reading the request best supports, named unanswered, never asked in place of the plan. A missing tool never means no plan gets written.

Before a large plan, in order:

1. **Scope grill**: one branch of the decision tree at a time, each with a recommendation. Questions the available material can answer (repo, docs, logs, the running system) go to read-only explorers instead, one per question, in parallel where the host supports it. They hand back findings, not file dumps. Exploration predates the plan, so it gets no dispatch-table row.
2. **Branch and PR**: which branch the work lands on, and whether the PR opens up front or at the end.
3. **Verification approach**: the user tests manually from a written script, the agent drives the running thing, automated tests only, or a mix.

Cap: three execution or verification questions before the plan, items 2 and 3 included. A deliverable-changing question is exempt from the cap, not from the regime above. Where the request already settles the open decisions, or hands the remaining ones to you, skip all three items and present the plan this turn: every call you made, the deliverable-changing ones included, goes to Assumptions marked unconfirmed, the alternative named. Where the turn must end with a plan, no question stands in place of it, whatever raised it, the cross-model review included. Stop once the remaining unknowns would not change the plan. Phase cadence: after approval, never before.

Anything outside this repo (library, framework, API, CLI, service, version-specific behaviour, pricing, limits) is looked up before the plan, not recalled, and the plan cites what was checked. Where the host grants no lookup tool, name the exact fact that could not be checked (endpoint, field, error code, version) and mark it unconfirmed in Assumptions: never assert it, never hedge around it unnamed.

## Cross-model review

Run it only where the plan invented something the request did not specify (a mechanism, a schema, a concurrency or consistency strategy), and the host can dispatch a subagent to a model other than the authoring one. That subagent critiques the draft in the same turn, before the plan is presented. The reviewer is one of the two most capable models the host offers, and never the one writing the plan. Where you cannot tell how the host ranks them, pick one you have reason to believe is at least as capable as the author, and name the reviewer and that uncertainty in Assumptions, unconfirmed. A decision handed back to you ("your call") is invented, not settled: the strongest reason to run the review, not to skip it. A request that settles its own design skips the review as it skips the grill, whatever its size. Never announce a pending review and hand the turn back; when it has run, the plan says so and names the reviewer. Where the host cannot choose a model, skip it. The only place a model override is used.

## The plan

Terse, fragments fine. No literal code blocks; short pseudo-code is fine. A small-tier plan carries Goal, Steps, Verification and Assumptions only, omitting Branch and PR and Risks. At any tier, carry a section only where the user answered its question; moot is not answered. Dropping an unraised question is the point; dropping a given answer is data loss. A section that would come out "not applicable" is omitted, never written as N/A. These sections, in this order:

- **Goal**: one line.
- **Steps**: numbered, one row each: what, files touched, `[parallel]` or `[seq]`, dependencies. This is the dispatch table, including the review and verification rows; a plan without it is incomplete.
- **Branch and PR**: as answered. Work with no version control and no diff skips this bullet and the phase-boundary review.
- **Verification**: automated and manual. A manual phase is required whenever the change is observable at runtime, naming the driver the user chose and a concrete pass condition: "submit the edited form, the success state appears, the log shows exactly one update event and no unrelated one", never "smoke-test it".
- **Risks**: uncertainties and irreversible actions. Then, under its own sub-heading, improvements you thought of, unasked, not built by this plan. Mark each take (worth doing, named as follow-up work) or skip (not worth doing here), reason on the same line. Risks carry no mark, nor do the user's settled decisions read back to them. The mark is your own verdict, already made, never a choice put to the user.
- **Assumptions**: what the plan takes as true and would break on, unconfirmed ones marked. Every outside-the-repo fact the plan leans on and could not look up belongs here by name (package, method signature, error code, version), with the lookup that would settle it. Declarative ("X is assumed to be Y, say if not"), never questions.

Present the whole plan every time approval is asked, every re-presentation after an edit included. Never a delta, a diff, or a summary of what changed.

The plan message carries exactly one question, the request for approval; none embedded in the plan text. Plus one declarative line naming where a saved copy would go (`docs/plans/<name>.md` shape): a plans directory already in the repo, else a gitignored one, else the user's, else a scratch directory. No plan file is written until the user asks for one; writing one unasked is the same violation as editing code before Go. Once a file legitimately exists, update its status as phases land, with commit ids, so a killed session can resume from it.

## Approval

Approval is the literal word "Go", case-insensitive, and nothing else. Not "yes", not "proceed", not "do it", not "looks good". End the plan by asking for it in those terms.

- Stop and end the turn after presenting. No edits in the turn that presents a plan.
- An answered question or a settled decision is never approval.
- Any plan edit or scope change re-opens the gate. An earlier Go covers the plan as presented, not a revised one.

## Execution

Once Go lands, before any dispatch, ask the phase cadence: stop at each phase boundary, or run all phases back to back.

Every implementation step is a subagent where the host has them. There the main agent never writes code: not a one-line fix, not a review finding, not a lint repair. It does version control, builds, tests, reading to verify, and triage. Small edits are exactly the ones that slip past a review boundary. Where the host has no subagents, do the steps yourself, sequentially.

Each dispatch states: goal, files in scope, what not to touch, constraints, report format wanted back, and that a stuck step means a lookup rather than another guess. Two failed attempts on one problem without a lookup means go read the documentation.

- Independent steps run in parallel, dependent ones sequentially. State the split.
- Isolated worktrees only when parallel subagents write concurrently, and only where the host supports them. Otherwise parallel writes fall back to sequential.
- Implementation subagents inherit the session model. No model override outside the plan review above.
- **Replan trigger**: when a step, or the user, reveals the plan is wrong (a false assumption, a hidden dependency, a later step now impossible), stop the work, name what broke and which steps it takes down, present the whole revised plan in the same turn, then ask for Go again. The pre-plan grill does not re-run: no explorer dispatch, and never a question handed back in place of the revised plan. An invented replacement goes into the steps as the recommended one, what it was chosen over named in Assumptions, unconfirmed.

Every phase boundary reviews the phase diff: a review subagent then a fix subagent for the accepted findings where the host has subagents, in context over the same diff where it does not. A real correctness or security bug is a hard stop, not an auto-continue. Verification is dispatched too wherever subagents exist, never run in the main context: the subagent absorbs the noise and hands back a verdict.

## Destructive actions stop, even after approval

Deploy, delete, external send, force push, schema migration. A waived phase boundary does not waive this. When an approved plan contains one, the executing turn opens by naming which step will stop for its own yes, before any dispatch. Stop for that yes, naming what goes and how much, and get it before the step is handed over. A menu of options closing with "which way?" is not that yes. Writing the command out for the user to run is performing it: the same stop, before the command appears. (Non-removable rule: any later pass that shortens this file must keep it.)
