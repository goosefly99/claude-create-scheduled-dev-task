# create-scheduled-dev-task

Claude Code skill for creating autonomous scheduled development tasks that work through a project's development roadmap.

## What It Does

This skill takes a project and sets up a fully configured scheduled task that:

1. Reads the project's `dev_roadmap.md` to find the next incomplete item
2. Reads the relevant `AGENTS.md` and design docs for context
3. Implements the item, runs tests, lints, and builds
4. Commits changes with a roadmap reference
5. Updates the tracking table status
6. Repeats on a configurable schedule (default: hourly)

## Prerequisites

The target project must have:

- A **development roadmap** (`dev_roadmap.md`) with a tracking table
- **AGENTS.md** files at each significant directory level
- **Design docs** and/or **ADRs** in `docs/`

If these don't exist, the skill automatically invokes the [`project-design-and-development`](../claude-project-design-dev) skill to create them first.

## Usage

Trigger the skill with phrases like:

- "Create a scheduled dev task for this project"
- "Set up an autonomous dev agent"
- "Schedule a development loop for librefrets"
- "Automate development on a schedule"

## Skill Structure

```
skills/create-scheduled-dev-task/
├── SKILL.md                                        # Main skill — 4-step workflow
└── references/
    ├── scheduled-task-prompt-template.md            # Autonomous agent prompt template
    └── project-discovery-checklist.md               # Information gathering checklist
```

## Workflow

| Step | What Happens |
|------|-------------|
| **1. Discovery** | Scan project for roadmap, AGENTS.md, docs, git config, build commands. Invoke `project-design-and-development` if docs are missing. |
| **2. Prompt Assembly** | Build a self-contained agent prompt from the template, replacing all placeholders with project-specific values. |
| **3. Task Creation** | Install the scheduled task via `mcp__scheduled-tasks__create_scheduled_task` with the assembled prompt and cron schedule. |
| **4. Verification** | Confirm installation, verify SKILL.md was created, present summary, optionally trigger first run. |

## Dependencies

- **`project-design-and-development` skill** — invoked when a project lacks required documentation
- **`mcp__scheduled-tasks` MCP server** — used to create and manage the scheduled task
- **Engineering skills** (optional) — `engineering:architecture`, `engineering:system-design`, `engineering:tech-debt`, `engineering:testing-strategy` — delegated through `project-design-and-development` when scaffolding docs
