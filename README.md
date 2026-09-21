# opencode-planning-skills

opencode skills for the two-phase plan workflow: **creating** plans (S0–S4 ceremony) and **executing** plans (thin-orchestrator task-runner loop).

## Skills

| Name               | Use it when                                  |
| ------------------ | -------------------------------------------- |
| `plan-creation`    | Making a new plan or converting a goal into a plan file |
| `plan-execution`   | Executing or resuming an existing plan        |

Each skill is loaded on demand via opencode's native `skill` tool, so bundling them costs only one short description per skill in context. Agents call the `skill` tool individually — no context bloat.

## Layout

```
opencode-planning-skills/
  plan-creation/SKILL.md
  plan-execution/SKILL.md
```

## Install

Both skills are self-contained; they depend only on opencode's core tooling (no external packages). The real workflow also uses a `plans/` directory and a `task-runner` agent — see the skill bodies for the full contract.

### Option A — clone into a project

```sh
git clone <this-repo> <project>/.opencode/skills
```

### Option B — register the path (skills can live anywhere)

```json
{
  "$schema": "https://opencode.ai/config.json",
  "skills": { "paths": ["/path/to/opencode-planning-skills"] }
}
```

### Option C — global

Clone into `~/.config/opencode/skills/`.

Typical layout inside the running project:

```
.opencode/
  skills/             <- symlink or clone of this repo
    plan-creation/SKILL.md
    plan-execution/SKILL.md
  agents/
    task-runner.md    <- companion agent the execution skill delegates to
```

Restart opencode after installing.

## Notes

- Skill names must match their folder names and stay unique across all loaded skill locations.
- The `task-runner` agent and the `plans/` convention are companions to these skills; they live in the workspace that runs the plans, not in this repo.