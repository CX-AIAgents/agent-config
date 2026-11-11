---
name: pi-subagent
description: Agent Skill for spawning a subagent. Use when you need to spawn a subagent.
---

# Pi Subagent

Spawn subagent using Pi Coding Agent.

## Prerequisites

`pi --help` resolves and returns the help menu

Otherwise stop and tell the user to install Pi Coding Agent

## Choosing Models

Run `pi --list-models` to see all available models (or `pi --list-models <provider>` to see models for a specific provider)

- If you don't know, by default choose the latest `flash` model from `deepseek` provider.
- Must use `--provider` to specify the provider.
- Avoid provider `anthropic`
- Always use `--thinking max`, it will fallback to the highest available thinking effort for the model.

## Pi Agent Setup to be known

- `pi` can read the project wise `AGENTS.md` file and `.agents/skills/`
- `pi` has a copy of the same user-wise `AGENTS.md` or `CLAUDE.md` file, and a copy of the same agent skills at `~/.agents/skills/`

## Other notes

- `-c` option can be used to continue from the previous chat. Use it when you only spawn one subagent and want the same subagent to continue further works.
- Give subagent the clear goal and context. And also tell the subagent that it can forward questions that you can forward to the user, if anything is unclear.
  - Subagent should have the same skill set and the same AGENTS.md context file as you have. So no need to repeat these skills or AGENTS.md context to the subagent, but do make sure when spawning the subagent, the skills and AGENTS.md context file are available to the subagent.