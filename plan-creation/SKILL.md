---
name: plan-creation
description: Use when MAKING a new plan or converting a goal into a plan file. Covers the S0–S4 planning ceremony, creating plans/<plan-name>/plan-creation.md as the fractional progress file, splitting the goal into task-<NN>.md files, and writing shared.md. Making a plan is orchestrator work — subagents skip this skill.
---

# Plan Creation

Treat plan creation as its own stepped process. It can cost more than task execution (discussion + confirmation rounds), so it gets the same pause/handoff treatment — see the Token budget section of `AGENTS.md` and `plans/INSTRUCTIONS.md`.

## Sub-steps (in order)

- **S0** Clarify scope (user discussions)
- **S1** Gather context (codebase-memory-MCP queries and/or the `explore-free` subagent → condensed digest)
- **S2** Draft architecture + task split
- **S3** Confirm decisions (user back-and-forth)
- **S4** Write `shared.md` + task files

Always gather context **before** writing the plan. Never write the plan from assumptions.

## Progress file

- Create `plans/<plan-name>/plan-creation.md` when plan creation starts. It is a **fractional step** — it precedes task-01, so it gets no `task-NN` number.
- It holds: current/next sub-step, confirmed decisions, pending questions, and the condensed digest so far. Update it at every sub-step boundary.
- **Pause/hand off at sub-step boundaries**, especially after each user-confirmation round. A finished confirmation round is a cheap handoff point; do not drag discussion noise into the writing sub-step.

## Folder & files

- One plan = one folder: `plans/<plan-name>/` (`plan-name` is a placeholder; replace it).
- Each step gets its own file: `plans/<plan-name>/task-<NN>.md` (Markdown — single format used everywhere).
- Optimize task splitting so **total token usage across all tasks combined is minimal**:
  - Keep related work in the **same task**; prefer **fewer, larger steps** so per-step overhead (file, handoff, resume) is paid fewer times.
  - Do not micro-split pre-emptively — subdivide on the fly (fractional step) only during execution if a step approaches the 40–50% trigger.
  - Minimize the **number** of tool calls: batch independent calls in parallel, delegate bulk tool loops to subagents.
- Task files may reference per-task files (`task-<NN>-<topic>.md`) holding **only** info used by that task — unless two tasks share the info, in which case they **share one file**.

## Shared info file

- Every plan has one `shared.md` storing info needed by **all** tasks + **completion state** of other tasks.
- Write into it only a **condensed "confirmed facts" digest** (optimized for retrieval, marked `do not re-explore`). Never dump raw file contents or full grep output.
- Include only facts needed by the tasks; defer anything expensive/discoverable to the task that actually needs it.

## Sources

Full process source of truth: `plans/INSTRUCTIONS.md` (Planning phase, Plan structure, Task files, Stored info sections).