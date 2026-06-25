---
name: plan
description: Create a lean, phase-based implementation plan for a task, display it to the user, and offer options to revise, execute, save as Markdown, save and execute, or close.
---

# Plan

## Overview

Create a concise implementation plan before doing task work. Use this skill when the user asks to plan, create a plan, draft implementation phases, save a plan, or choose between revising and executing planned work.

## Workflow

1. Understand the task.
   - Inspect the relevant files, commands, docs, or current state needed to make the plan concrete.
   - Keep discovery focused. Do not implement task changes while planning unless the user already chose execution.
   - If a plan cannot be made safely without a critical missing answer, ask for that answer before presenting options.

2. Write the plan in phases.
   - Use short phase names and concrete outcomes.
   - Include validation as its own phase when the task changes code, data, build output, or user-facing behavior.
   - Call out assumptions or risks only when they materially affect execution.
   - Keep the plan lean enough to scan quickly.

3. Display the plan.
   - Present the plan clearly in the conversation.
   - Include this option menu exactly, renumbering the duplicated final option as `5`:

```text
Options:
1. Revise the plan
2. Execute the plan
3. Save the plan as a .md file
4. Save the plan as a .md file and execute the plan
5. Close
```

4. Handle the user's choice.
   - `1. Revise the plan`: ask what they want changed, then update and redisplay the plan with the option menu.
   - `2. Execute the plan`: carry out the phases end to end. Keep the user updated as work progresses and verify the result.
   - `3. Save the plan as a .md file`: save the displayed plan to a sensible repo-local Markdown path, then report the path.
   - `4. Save the plan as a .md file and execute the plan`: save first, then execute.
   - `5. Close`: acknowledge and stop without executing or saving.

## Saving Plans

- Prefer a repo-local path such as `plans/<task-slug>.md` unless the user gives a path or the repo has an existing planning/docs convention.
- Create parent directories if needed.
- The saved file should contain the task title, phases, assumptions or risks when present, and validation plan.
- Do not include the option menu in the saved Markdown unless the user asks for it.

## Execution Rules

- During execution, follow the displayed plan unless new information makes a change necessary.
- If the plan changes materially, pause to explain the change before continuing.
- Respect dirty worktrees and existing user changes.
- Finish with a concise summary of what was done, what was verified, and where the saved plan lives if one was created.
