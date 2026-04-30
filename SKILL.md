---
name: skill-graph
description: Navigate the agent-native skill graph — load skills by task, follow manifests, apply workflow orchestration. Use when the agent needs to discover, create, or improve skills following the three-layer architecture (Skills/Context/Frameworks).
version: 1.0.0
author: BlutAgent
license: MIT
metadata:
  hermes:
    tags: [skills, graph, architecture, meta, manifest, context]
    related_skills: [self-improver, skill-publisher, writing-plans]
  manifest:
    always_load:
      - CANONICAL.md
      - context/identity.md
      - context/user.md
    context:
      - context/manifest.yaml
    references:
      - SOUL.md
---

# Skill Graph

## Overview

Skill Graph is the navigation system for the agent-native skill architecture. It implements the three-layer pattern described in the [Agent-Native Systems and Skill Graphs](https://baselinestudio.design/blog/agent-native-systems-and-skill-graphs) article: **Skills** (methodology), **Context** (business knowledge), and **Frameworks** (cross-cutting patterns).

**Core philosophy:** Skills are not a folder of prompt files. They're nodes in a graph. Each node declares what it needs through its manifest. The agent traverses the graph — it doesn't scan blindly.

## Architecture: Three Layers

```
~/.hermes/
├── CANONICAL.md          ← Routing layer (read first, every time)
├── SOUL.md               ← Agent soul (long-form identity)
├── context/              ← CONTEXT LAYER — business knowledge
│   ├── manifest.yaml     ← What context exists, what loads where
│   ├── identity.md       ← Agent voice, values, boundaries
│   └── user.md           ← Human preferences, environment
├── skills/               ← SKILL LAYER — domain methodologies
│   ├── github/           ← GitHub domain skills
│   ├── software-dev/     ← Dev skills + frameworks
│   ├── meta/             ← Self-improvement skills
│   └── ...
└── config.yaml           ← System config
```

### Layer 1: Skills
A skill is a complete methodology for one domain. Not a prompt. A methodology. It defines approaches, decision points, and deliverables — it teaches the agent how to do the work.

### Layer 2: Context
Business-specific knowledge, separate from any skill. Identity, voice, user preferences, environment details. Context doesn't tell the agent what to do — it tells the agent who it is and who it's working with. When a skill loads context, the output stops being generic.

### Layer 3: Frameworks
Reusable methodologies not owned by any single skill. Debugging patterns. TDD workflow. Security review. Planning and estimation. Loaded by multiple skills, maintained independently.

**Key insight: Good instructions with no context produce polished generic output. Average instructions with rich context produce specific, useful output. Good instructions with rich context produce output you actually ship.**

## The Manifest Pattern

Every skill should declare what it needs in its YAML frontmatter:

```yaml
metadata:
  manifest:
    always_load:        # Loaded every time the skill runs
      - skill-file      # The skill itself
      - core-frameworks # e.g., plan, systematic_debugging
    context:            # Business-specific files
      - identity.md
      - user.md
      - domain-specific
    references:         # Loaded when task needs depth
      - templates/
      - examples/
```

### Why manifests matter

1. **Progressive disclosure** — the agent reads the manifest first, knows what exists, loads only what's relevant
2. **Graph edges** — manifests define connections between nodes. The agent traverses them.
3. **No guessing** — the agent doesn't scan a folder hoping to find the right file
4. **Independent maintenance** — change a context file, every skill that loads it improves

## Creating a New Skill

### Checklist

- [ ] **One complete thought.** The skill covers one domain thoroughly — not two domains shallowly.
- [ ] **Methodology, not prompt.** It teaches how to do the work, not just what to say.
- [ ] **Manifest complete.** `always_load`, `context`, `references` declared.
- [ ] **Related skills linked.** What skills does this build on? What skills build on this?
- [ ] **When to use / when NOT to use.** Clear triggers and boundaries.
- [ ] **Pitfalls section.** What goes wrong? What do people miss?
- [ ] **Verification section.** How do you know the skill worked correctly?
- [ ] **Security section** (if applicable). File validation, endpoint validation, owner/repo validation.
- [ ] **Changelog.** Track versions and what changed.

### Template

```markdown
---
name: skill-name
description: One-sentence description of what this skill does and when to use it.
version: 1.0.0
author: BlutAgent
license: MIT
metadata:
  hermes:
    tags: [domain, keywords]
    related_skills: [skill-a, skill-b]
  manifest:
    always_load:
      - CANONICAL.md
      - context/identity.md
    context:
      - context/user.md
    references: []
---

# Skill Title

## Overview
What this skill does, when to use it, core philosophy.

## When to Use
- Clear trigger conditions
- When NOT to use this skill

## Workflow
### Step 1: Plan
### Step 2: Clarify
### Step 3: Load Context
### Step 4: Execute
### Step 5: Verify

## Pitfalls
- Common mistakes
- What people miss
- Edge cases

## Verification
How to confirm the skill worked correctly.

## Changelog
### v1.0.0 (YYYY-MM-DD)
- Initial release
```

## Workflow Orchestration

All skills follow the same meta-workflow. Domain changes, execution pattern doesn't.

### The Five-Step Pattern

1. **Plan before executing.** State understanding → outline approach → identify context → note open questions → wait for approval.

2. **Clarify before starting.** Gather essential information. If answers missing or ambiguous, ask directly. Don't assume.

3. **Load relevant context.** Read the manifest. Core context always loads. Extended context based on domain. References when depth needed.

4. **Execute domain-specific work.** The skill defines approaches, when to use each, how to execute, deliverables.

5. **Quality checks.** Binary pass/fail. Verify before marking done. If it doesn't pass, it's not done.

### Error Recovery Loop

```
Quality check failed?
  → Identify what specifically isn't working
  → Diagnose why (unclear reqs? missing context? wrong approach?)
  → Apply fix (clarify, load more, try different path)
  → Re-run quality checks
  → If 2-3 attempts don't resolve: escalate, don't spin
```

### Session Management

- **One major task per session.** Context degrades before it runs out.
- **Start fresh after milestones.**
- **Long sessions → less precise output, forgotten decisions, repeated mistakes.**

## Four Governing Principles

Applied to all work, across all skills:

1. **Simplicity first** — Is there a simpler way? Complexity must be justified. If you can cut it, cut it.
2. **Root cause focus** — Understand deeply before solving. Ask "why" until you reach the real problem.
3. **Demand elegance** — For non-trivial work, pause: is there a more elegant framing? Right framing → obvious solution.
4. **Verification before done** — "I think it's done" is not done. Test. Demonstrate correctness.

## Auditing the Graph

Run weekly via `self-improver`:

```bash
# Check manifest completeness
grep -L "always_load" ~/.hermes/skills/**/SKILL.md

# Check context layer health
ls $VAULT/memories/

# Check for orphan skills (no related_skills links)
grep -L "related_skills" ~/.hermes/skills/**/SKILL.md

# Check for stale changelogs
# Compare mtime vs last changelog entry date
```

## Portability

This architecture works with any AI tool. The same files work with Claude Code, Cursor, Windsurf, Codex, GitHub Copilot, JetBrains AI. The agent reads CANONICAL.md and knows what to do.

**Agent-native means the system is portable. Not locked to a platform. Locked to a pattern.**

## Changelog

### v1.0.0 (2026-04-24)
- Initial release based on Baseline Core architecture
- Three-layer architecture: Skills / Context / Frameworks
- Manifest pattern with always_load / context / references
- Five-step workflow orchestration
- Four governing principles
- Skill creation template and checklist
