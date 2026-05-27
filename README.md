<!-- gen-readme:auto -->
# agents-skills-github-cli

A skill repository for Claude Code centered on **GitHub CLI enforcement** — keeping all
GitHub operations on the official `gh` CLI instead of MCP wrappers or ad-hoc REST/GraphQL
calls. Each skill lives under `.agents/skills/<name>/` and is mirrored into
`.claude/skills/<name>` as a relative symlink so the harness auto-discovers it.

## Skills

| Skill | What it does |
|-------|--------------|
| [`github-cli-enforcement`](.agents/skills/github-cli-enforcement/SKILL.md) | Enforce the official GitHub CLI (`gh`) as the only sanctioned way to create, modify, or read GitHub pull requests, issues, releases, repos, workflows, and gists — and explicitly prohibit the MCP `github-create_pull_request` tool (and other `github-*` MCP wrappers) and ad-hoc REST/GraphQL calls when an equivalent `gh` command exists. |

## Install

### Per skill — `npx skills add`

Install any single skill into Claude Code:

```bash
npx skills add carlosmarte/agents-skills-github-cli \
  --skill github-cli-enforcement -a claude-code
```

## Layout

```
.agents/skills/<name>/SKILL.md                          # source of truth for each skill
.claude/skills/<name> -> ../../.agents/skills/<name>    # relative symlink (harness-discovered)
```
