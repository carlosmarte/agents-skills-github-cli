---
name: github-cli-enforcement
description: Enforce the official GitHub CLI (`gh`) as the only sanctioned way to create, modify, or read GitHub pull requests, issues, releases, repos, workflows, and gists — and explicitly prohibit the MCP `github-create_pull_request` tool (and other `github-*` MCP wrappers) and ad-hoc REST/GraphQL calls when an equivalent `gh` command exists. Use whenever an operation touches a GitHub PR, issue, release, label, review, or any repo feature; whenever an agent is about to call an MCP GitHub tool or `curl` the GitHub API; or when standardizing a team's GitHub automation on native CLI tooling.
tier: org
---

# Git & GitHub CLI Enforcement

This skill is the standard operating procedure for interacting with GitHub. The rule is simple and non-negotiable:

> **NO GITHUB MCP. Use `gh` for all GitHub operations.** Do not use *any* tool from a GitHub MCP server — not `github-create_pull_request`, and not any other `github-*` / `mcp__github__*` tool. Do not hand-roll REST/GraphQL calls when a `gh` command exists.

The goal is reliable, native, fully-featured GitHub interactions that behave exactly as a human developer in a terminal would expect — instead of limited, drifting wrapper tools.

## When to apply

Apply this skill any time an operation requires creating, modifying, or reading GitHub pull requests, issues, releases, labels, reviews, runs, or any other repository feature. It is for AI agents, automation scripts, and developers working within a terminal environment.

## The Prohibition (read first)

- **NO GitHub MCP — at all.** Do not call *any* tool exposed by a GitHub MCP server, regardless of name (`github-create_pull_request`, `github-create_issue`, `mcp__github__*`, etc.). If such a tool is offered or available, treat it as off-limits and use the equivalent `gh` command instead. This is a blanket ban on the GitHub MCP surface, not just the create-PR tool.
- **Never** hand-roll a raw `curl`/`fetch` against `api.github.com` *when an equivalent `gh` command exists*. Reach for the API only when `gh` genuinely cannot express the operation — and even then prefer `gh api` (the authenticated passthrough, below) over a hand-rolled request.
- A `gh` command erroring out is **not** a license to fall back to GitHub MCP or the raw API. Diagnose the error (auth, branch, repo context) and fix the root cause.

## Workflow

1. **Identify the need** — name the specific GitHub task (open a PR, file an issue, cut a release, request a review).
2. **Verify environment** — confirm you are inside the target local Git repository (`git rev-parse --is-inside-work-tree`) and authenticated (`gh auth status`). Check the active branch with `git status -sb` before any `gh pr create`.
3. **Bypass all GitHub MCP tools** — acknowledge the ban; do not call any GitHub MCP tool (`github-*` / `mcp__github__*`), including but not limited to `github-create_pull_request`.
4. **Execute the `gh` command** — formulate and run the appropriate command in the shell. Use descriptive `--title` strings and Markdown-formatted `--body` text.

## Command reference

| Task | Command |
|------|---------|
| Create a PR | `gh pr create --title "Feature: Update navigation" --body "Adds new links to the header."` |
| Create a PR against a specific base | `gh pr create --base main --head my-branch --title "..." --body "..."` |
| View / list PRs | `gh pr view <n>` · `gh pr list` |
| Check out a PR locally | `gh pr checkout <n>` |
| Review / merge a PR | `gh pr review <n> --approve` · `gh pr merge <n> --squash` |
| Create an issue | `gh issue create --title "Bug: Login failure" --body "Users cannot log in on Safari."` |
| View / list issues | `gh issue view <n>` · `gh issue list` |
| Cut a release | `gh release create v1.2.3 --notes "..."` |
| Inspect CI runs | `gh run list` · `gh run view <id> --log` |
| Anything `gh` lacks a verb for | `gh api <endpoint>` (authenticated REST/GraphQL passthrough — still the CLI) |

A successful `gh pr create` / `gh issue create` returns the direct URL to the new PR or issue on its last line — capture it as the operation's result.

## Best practices

- Always use descriptive strings for `--title` and format `--body` text with standard Markdown.
- Verify the active Git branch (`git status` / `git branch`) before `gh pr create` so the PR opens from the intended branch.
- For non-interactive/automation contexts, pass every field explicitly (`--title`, `--body`, `--base`) so `gh` never drops into a prompt.
- When you need an endpoint `gh` does not wrap directly, use `gh api` rather than `curl` — it reuses `gh`'s auth and host config.

## Common mistakes to avoid

- Reaching for *any* GitHub MCP tool (`github-create_pull_request`, `github-*`, `mcp__github__*`) — they are banned outright, even as a fallback when a `gh` command errors out. Fix the error instead.
- Running `gh` commands outside the relevant repository directory, causing "no git repository" / wrong-repo context errors.
- Hand-rolling `curl` calls to `api.github.com` for things `gh` already does (PRs, issues, releases, reviews).

## Prerequisites

- The `gh` CLI is installed (`gh --version`).
- The agent is authenticated (`gh auth login` / verify with `gh auth status`).
- The working directory is a Git repository with a GitHub remote.

## Why this skill exists

- **Complete feature access** — `gh` covers GitHub's surface comprehensively; MCP wrappers are narrow and lag behind.
- **Native, predictable behavior** — commands behave exactly as a human developer expects, and are trivially scriptable.
- **Actively maintained by GitHub** — the CLI tracks the platform; wrapper tools drift.

Limitation to accept: this approach requires shell access and a configured local environment (installed `gh`, valid auth, repo context). When those are missing, fix the environment — do not switch tools.
