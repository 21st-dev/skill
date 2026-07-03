# 21st-cli — Agent Skill

Search, install and publish React/shadcn components, themes and templates on
[21st.dev](https://21st.dev) from any coding agent, via the `21st` CLI
(`@21st-dev/cli`).

## Install

Cross-agent, via [skills.sh](https://skills.sh) (70+ supported agents —
Claude Code, Cursor, Codex, Windsurf, Cline, Copilot, Goose, and more):

```bash
npx skills add 21st-dev/skill
```

Or fetch the raw `SKILL.md` directly:

```bash
curl -fsSL https://21st.dev/api/skills/21st-cli \
  -o ~/.claude/skills/21st-cli/SKILL.md
```

For Claude Code and Codex, this skill is also bundled together with the
21st MCP server in one plugin — see
[`21st-dev/claude-code-plugin`](https://github.com/21st-dev/claude-code-plugin)
and [`21st-dev/codex-plugin`](https://github.com/21st-dev/codex-plugin).

## What it does

Teaches the agent the full `21st` CLI surface: `search`, `get`, `add`
(install), `generate`, `publish` / `publish-theme` / `publish-template`,
`edit` / `delete`, bookmarks, teams, and auth (`login`/`whoami`). See
[`skills/21st-cli/SKILL.md`](./skills/21st-cli/SKILL.md) for the full
reference — this is a live mirror of
[21st.dev/api/skills/21st-cli](https://21st.dev/api/skills/21st-cli).

Get a 21st API key at https://21st.dev/settings/api-keys, or run
`npx @21st-dev/cli login`.
