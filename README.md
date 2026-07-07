# 21st.dev — Agent Skills

Search, install and publish React/shadcn components, themes and templates on
[21st.dev](https://21st.dev) from any coding agent, via the `21st` CLI
(`@21st-dev/cli`). Ships four focused skills: `21st-cli-use` (find & install),
`21st-ai` (generate & iterate), `21st-registry` (publish & manage), and
`21st-design-sync` (publish a project's design as a theme).

## Install

Cross-agent, via [skills.sh](https://skills.sh) (70+ supported agents —
Claude Code, Cursor, Codex, Windsurf, Cline, Copilot, Goose, and more):

```bash
npx skills add 21st-dev/skill
```

Or install all three globally with the CLI:

```bash
npx @21st-dev/cli install-skill
```

(or fetch any one raw, e.g. `curl -fsSL https://21st.dev/api/skills/21st-cli-use
-o ~/.claude/skills/21st-cli-use/SKILL.md`).

For Claude Code and Codex, these skills are also bundled together with the
21st MCP server in one plugin — see
[`21st-dev/claude-code-plugin`](https://github.com/21st-dev/claude-code-plugin)
and [`21st-dev/codex-plugin`](https://github.com/21st-dev/codex-plugin).

## What it does

Teaches the agent the full `21st` CLI surface, split across three skills:

- [`skills/21st-cli-use/SKILL.md`](./skills/21st-cli-use/SKILL.md) — `search`,
  `get`, `theme`, `add` (install), bookmarks, teams, auth.
- [`skills/21st-ai/SKILL.md`](./skills/21st-ai/SKILL.md) — `generate`,
  `generation`, `iterate`, `take`: sketch, edit-in-place, and grab code.
- [`skills/21st-registry/SKILL.md`](./skills/21st-registry/SKILL.md) — `publish`
  / `publish-theme` / `publish-template` and `edit` / `delete`.
- [`skills/21st-design-sync/SKILL.md`](./skills/21st-design-sync/SKILL.md) —
  read the project's design tokens and publish them as a theme.

Each is a live mirror of
[21st.dev/api/skills/&lt;name&gt;](https://21st.dev/api/skills/21st-cli-use).

Get a 21st API key at https://21st.dev/settings/api-keys, or run
`npx @21st-dev/cli login`.
