# artificer

An artificer's kit for building minimal dark-factories on Claude Code. You compose small, gated agent steps into a deterministic build you can read end to end. The model does the work. The harness stays thin.

Built by [hackers&wizards](https://hackersandwizards.dev). We have spent 20+ years building and running production systems, and we teach Functional Agentic Engineering: small composable steps over sprawling harnesses. artificer is that practice as Claude Code tooling.

A "dark factory" is a lights-out production line that runs without people watching it. The usual way to get there is a giant harness that nobody can follow. artificer builds the other kind: a line small enough to hold in your head, where the model does the thinking and the harness only keeps it on track.

## What you build

The `build-workflow` plugin scaffolds one stage at a time. Each stage gets a subagent with a gate that re-runs until its check passes, a launcher skill to run the stage on its own, and a line in a workflow script that threads each stage's output into the next.

A worked example is the sdlc line in `examples/sdlc/`. A feature request goes in, a tested and accepted change comes out:

```
plan -> tests -> implement -> simplify -+-> review-claude -+-> converge -> acceptance -> report
                                        +-> review-codex --+       |
                                                    (real issues? loop back to implement, max 3)
```

Nine stages. The two reviews run in parallel, converge waits for both and loops back on real issues. Every stage is a few lines you can read. The orchestration is plain JavaScript control flow, not a black box.

## Install

Add the marketplace, then install the plugin:

```
/plugin marketplace add hackersandwizards/artificer
/plugin install build-workflow@hackersandwizards
```

## Build a stage

```
/build-workflow <workflow> <order> <step>: role / models / tools / check
```

For example, a first stage that turns a feature request into a plan:

```
/build-workflow sdlc 1 plan: Turn the request into a plan with acceptance criteria / opus, high / Read, Write / true
```

Each call appends a stage. The check command is the contract: a stage is done only when its gate exits 0 (tests green, build clean, review complete). For parallel stages, loops, and escalation, edit the generated `.claude/workflows/<workflow>.js` directly. The generator embeds the recipes as comments.

The plugin also ships `/improve-workflow`. It runs a generated workflow on a real input, checks that its assumptions hold (gates fire, structured returns branch, parallel stages overlap), and feeds what it learns back into the workflow and the generator templates.

## Scope

artificer targets Claude Code. The stages it builds are Claude Code agents, skills, and workflow scripts.

## Repository layout

```
.claude-plugin/marketplace.json   the marketplace manifest
plugins/build-workflow/           the build-workflow plugin
examples/sdlc/                    a dark-factory built with build-workflow
```

## License

MIT. See [LICENSE](LICENSE).
