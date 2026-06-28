---
name: build-workflow
description: "Scaffold one stage of a multi-step workflow: a per-stage subagent (with a SubagentStop gate), a manual launcher skill, and a dynamic-workflow script that orchestrates the stages deterministically. Run once per step you want to add."
argument-hint: "<workflow> <order> <step>: role / models / tools / check"
model: haiku
effort: low
allowed-tools: Task
---

Scaffold one workflow stage by orchestrating two subagents with the Task tool, in order:

1. **New workflow only.** If this is the first stage of a new workflow, first spawn the `claude-code-guide` subagent to confirm the current Claude Code docs the scaffolding depends on: subagent `Stop`/`SubagentStop` gates, whether `agentType` fires those gates inside a workflow, and the dynamic-workflow script API. Skip this on later stages of the same workflow.
2. **Scaffold.** Spawn the `build-workflow` generator agent (subagent type `build-workflow:build-workflow`) and give it the stage spec below plus any doc corrections from step 1. Return its result.

Stage spec:

$ARGUMENTS
