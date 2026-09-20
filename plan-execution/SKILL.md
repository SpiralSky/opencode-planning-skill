---
name: plan-execution
description: Use when EXECUTING or RESUMING a plan in plans/<plan-name>/ — the sequential task-runner delegation loop (paths only, one-line status), the shared.md read-once rule, per-step pauses, on-the-fly fractional subdivision (task-<NN>.<f>.md) at the 40–50% token trigger, appending mid-plan findings, pending-propagation, and the compact-free compaction handoff. Orchestrator-only — subagents skip this skill.
---

# Plan Execution

The main agent is a **thin orchestrator only**: never execute task work in your own context. Delegate every task end-to-end to the `task-runner` subagent, which runs in its own session with zero carryover.

## Per-task loop

1. Read `plans/<plan>/shared.md` **once** — completion state + pending-propagation list — to find the next incomplete task. **Never read the task file itself**; the runner reads it in its own (cheap-to-parent) context.
2. Invoke `task-runner` with **paths only**: plan folder, `task-<NN>.md` name, and the return contract. Do not paste task contents.
3. Runner returns exactly `task-<NN> done` or `task-<NN> ERROR: <one line>`.
4. `done`: retain nothing; proceed to the next incomplete task.
5. `ERROR`: pass the one-line error to a fresh `task-runner` (fix/retry); if it fails again, pause for the user with just that line.

Knowledge crosses between tasks **only through files**: the runner itself updates `shared.md` (Status line + condensed one-line note) and handles the pending-propagation list.

## shared.md hygiene

- Read `shared.md` once at step start and once more on resume after a handoff. Do not re-read mid-step; re-read between steps only if you believe it changed.
- Prefer shared.md over other task files unless absolutely necessary.
- When a task completes, condense its completed-notes to **one line** in the task-list section. Keep `shared.md` lean.

## Pauses

- **Do not** move to the next step immediately after completing a step. Pause/hand off before proceeding. **Exception:** isolated `task-runner` steps proceed without a pause (nothing accumulates in the orchestrator context). Keep handoffs for user-confirmation rounds.
- **No plan-completion webhook.** Finishing all tasks fires nothing on its own — do not claim or implement one.

## Token-budget handoffs (fractional steps)

- Effective budget = `min(100_000, model max context)` tokens.
- **HARD STOP at 40–50%** of that budget: stop immediately (even mid-step), insert a fractional step `task-<NN>.<f>.md` (e.g. `task-01.5.md`) **before** the next real step. It becomes the current step until resumed; on resume, mark it done (one-line note) then continue.
- **Checkpoint content** = everything the resumed agent will use, condensed: exact remaining goal (quote the user verbatim when short), current position + next step, confirmed decisions, pending questions, condensed facts/digest (marked `do not re-explore`). Defer anything discoverable. Nothing needed again may be left only in the old context.

## Updating the plan mid-execution

- **Default: append** new findings as a clearly-marked block to the **immediately-next** step file (`task-<NN+1>.md`). Do **not** rewrite many future steps (fan-out of reads = token cost).
- **Compact by subagent:** hand that step file to `compact-free` (Compaction Subagent Contract in `plans/INSTRUCTIONS.md`). It reads **only** that file, merges the appended block (dedupe, condense, keep structure), rewrites it. It must not drop prior valid content.
- **Bound edits:** per finding, touch at most **2 future step files + shared.md**.
- **Far-future impact:** do NOT edit the affected step directly — add a **one-line pointer** to `shared.md`'s pending-propagation list (e.g. `task-03 finding affects task-06: <one line>`); each step checks that list when it starts.

## Sources

Full process source of truth: `plans/INSTRUCTIONS.md` (Execution rules, Sequential task execution, Updating the plan, Token-budget handoffs, Compaction contract sections).