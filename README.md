# dark-factory-tools

A Claude Code plugin marketplace from [hackers&wizards](https://hackersandwizards.dev). It hosts plugins that turn agentic engineering practices into reusable Claude Code tooling.

## Install

Add the marketplace, then install a plugin:

```
/plugin marketplace add hackersandwizards/dark-factory-tools
/plugin install build-workflow@hackersandwizards
```

## Plugins

### build-workflow

Scaffold a multi-step agent workflow, one stage at a time. Each stage you add gets three pieces:

- a per-stage subagent with a `SubagentStop` gate that re-runs the agent until its check passes
- a manual launcher skill, so you can run a single stage on its own
- a dynamic-workflow script that runs the stages in order and threads each stage's output into the next

You describe a stage and the generator writes the files. The check command is the contract: a stage is done only when its gate exits 0 (tests green, build clean, review complete). Run the skill once per stage you want to add.

```
/build-workflow <workflow> <order> <step>: role / models / tools / check
```

For example, a first stage that turns a feature request into a plan:

```
/build-workflow sdlc 1 plan: Turn the request into a plan with acceptance criteria / opus, high / Read, Write / true
```

Linear workflows append a stage line per call. For parallel stages, loops, and escalation, edit the generated `.claude/workflows/<workflow>.js` directly. The generator embeds the recipes as comments.

The plugin also ships `/improve-workflow`, which runs a generated workflow on a real input, verifies its assumptions (gates fire, structured returns branch, parallel stages overlap), and feeds what it learns back into the workflow and the generator templates.

## Example: the sdlc workflow

`examples/sdlc/` is a worked workflow built with `build-workflow`. It carries a feature request through nine stages:

1. **plan**: turn the request into a plan with acceptance criteria
2. **tests**: write failing tests for the planned behavior
3. **implement**: make the tests pass
4. **simplify**: clean up while the tests stay green
5. **review-claude**: review the change and list real issues
6. **review-codex**: a second review via `codex exec`
7. **converge**: reconcile both reviews into one verdict
8. **acceptance**: exercise the feature in a real browser
9. **report**: summarize what was built, tested, and accepted

review-claude and review-codex run in parallel; converge waits for both. If converge finds real issues it loops back to implement, up to three rounds, then asks for help. See `examples/sdlc/sdlc.md` for the full build spec and `examples/sdlc/.claude/workflows/sdlc.js` for the orchestration.

## Repository layout

```
.claude-plugin/marketplace.json   the marketplace manifest
plugins/build-workflow/           the build-workflow plugin
examples/sdlc/                    a workflow built with build-workflow
```

## License

MIT. See [LICENSE](LICENSE).
