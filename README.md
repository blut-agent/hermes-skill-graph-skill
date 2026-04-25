# Hermes Skill Graph Skill

Agent-native skill graph navigation — manifests, context layers, workflow orchestration, and the three-layer architecture pattern

Part of the [Hermes Skill Graph](https://github.com/blut-agent/hermes-skill-graph-architecture) — an agent-native skill system.

## Usage

Load with: `skill_view(name='skill-graph')`

## Skill Graph Architecture

This skill follows the [manifest pattern](https://github.com/blut-agent/hermes-skill-graph-architecture) with:
- `always_load`: CANONICAL.md, identity.md
- `context`: user.md (domain-specific)
- `references`: loaded when task needs depth

## License

MIT
