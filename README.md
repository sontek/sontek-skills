# Sentry Skills

Agent skills, following the [Agent Skills](https://agentskills.io) open format.

Original inspiration:
- https://github.com/getsentry/sentry-skills/tree/main

## Installation

### Claude Code from local clone

```bash
# Clone the repository
git clone git@github.com:sontek/sontek-skills.git ~/sontek-skills

# Install the plugin directly
claude plugin install ~/sontek-skills
```

After installation, restart Claude Code. The skills will be automatically
invoked when relevant to your task.

### Updating

```bash
# Update the plugin
claude plugin update sontek-skills@sontek-skills
```

Or use `/plugin` to open the interactive plugin manager.

## Available Skills

| Skill                                                            | Description                                               |
| ---------------------------------------------------------------- | --------------------------------------------------------- |
| [code-review](plugins/sontek-skills/skills/code-review/SKILL.md) | Code review guidelines and checklist                      |
| [commit](plugins/sontek-skills/skills/commit/SKILL.md)           | Commit message conventions                                |
| [create-pr](plugins/sontek-skills/skills/create-pr/SKILL.md)     | Create pull requests following conventions                |
| [deslop](plugins/sontek-skills/skills/deslop/SKILL.md)           | Remove AI-generated code slop from branch changes         |
| [find-bugs](plugins/sontek-skills/skills/find-bugs/SKILL.md)     | Find bugs and security vulnerabilities in branch changes  |
| [iterate-pr](plugins/sontek-skills/skills/iterate-pr/SKILL.md)   | Iterate on a PR until CI passes and feedback is addressed |

## Creating New Skills

Skills follow the
[Agent Skills specification](https://agentskills.io/specification). Each skill
requires a `SKILL.md` file with YAML frontmatter.

### Skill Template

Create a new directory under `plugins/sontek-skills/skills/`:

```
plugins/sontek-skills/skills/my-skill/
└── SKILL.md
```

**SKILL.md format:**

```yaml
---
name: my-skill
description: A clear description of what this skill does and when to use it. Include keywords that help agents identify when this skill is relevant.
---

# My Skill Name

## Instructions

Step-by-step guidance for the agent.

## Examples

Concrete examples showing expected input/output.

## Guidelines

- Specific rules to follow
- Edge cases to handle
```

### Naming Conventions

- **name**: 1-64 characters, lowercase alphanumeric with hyphens only
- **description**: Up to 1024 characters, include trigger keywords
- Keep SKILL.md under 500 lines; split longer content into reference files


We keep all files limited to 80 characters, you can wrap them with:

```bash
npx prettier --write --prose-wrap always --print-width 80 your-file.md
```

### Optional Fields

| Field           | Description                                                     |
| --------------- | --------------------------------------------------------------- |
| `license`       | License name or path to license file                            |
| `compatibility` | Environment requirements (max 500 chars)                        |
| `model`         | Override model for this skill (e.g., `sonnet`, `opus`, `haiku`) |
| `allowed-tools` | Space-delimited list of tools the skill can use                 |
| `metadata`      | Arbitrary key-value pairs for additional properties             |

```yaml
---
name: my-skill
description: What this skill does
license: Apache-2.0
model: sonnet
allowed-tools: Read Grep Glob
---
```

## References

- [Agent Skills Specification](https://agentskills.io/specification)

## License

MPL-2.0
