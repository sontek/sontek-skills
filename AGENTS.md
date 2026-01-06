# Sentry Agent Skills

A collection of agent skills for, primarily designed for
[Claude Code](https://claude.ai/claude-code).

This repository is structured as a Claude Code plugin (see
`plugins/sontek-skills/`), but the skills themselves follow the open
[Agent Skills specification](https://agentskills.io) format to maintain
compatibility with other tools that adopt the standard.

## Structure

```
plugins/sontek-skills/skills/<skill-name>/SKILL.md
```

Each skill is a directory containing a `SKILL.md` file with YAML frontmatter
(`name`, `description`) and markdown instructions.

## Creating a Skill

1. Create `plugins/sontek-skills/skills/<skill-name>/SKILL.md`
2. Add YAML frontmatter (see below)
3. Write clear instructions in markdown
4. Update `README.md` to include the new skill in the Available Skills table


### Frontmatter

**Required:**

- `name` - kebab-case, 1-64 chars
- `description` - up to 1024 chars, include trigger keywords

**Optional:**

- `model` - override model (`sonnet`, `opus`, `haiku`)
- `allowed-tools` - space-delimited list of permitted tools
- `license` - license name or path
- `compatibility` - environment requirements (max 500 chars)

```yaml
---
name: example-skill
description: What this skill does and when to use it. Include trigger keywords.
model: sonnet
allowed-tools: Read Grep Glob Bash
---
# Example Skill

Instructions for the agent.
```

## References

- [Agent Skills Spec](https://agentskills.io/specification)
