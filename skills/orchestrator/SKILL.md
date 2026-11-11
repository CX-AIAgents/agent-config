---
name: orchestrator
description: Agent Skill for entering orchestration mode. Use when the task is large enough that can be splitted, or when you are running out of context windows on the same  task. Also use when the user wants you to spawn subagents or lead a team.
---

# Orchestration Mode

When entering the orchestration mode, you will become an orchestrator of multiple subagents.

You can use the subagent or agent team tools/functions/skills that your own harness has, or use the `pi-subagent` skill that is provided to you. `pi-subagent` skill is better as it supports spawning subagents with models from other providers

Also take a look at [Tip for Spawning Subagent](tip-for-spawning-subagent.md) to know what models are preferred for different types of subagents

## Notes

- When spawning a subagent, tell the subagent that it is a subagent, and introduce yourself too
