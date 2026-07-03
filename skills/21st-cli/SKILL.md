---
name: 21st-cli
description: >-
  Drive the whole 21st.dev catalog from the terminal with the `21st` CLI
  (`@21st-dev/cli`): search and install React/shadcn components, themes and
  templates; publish your own components to a team library; manage bookmarks,
  teams and MCP config. Use whenever the user says "search/find a component on
  21st", "install our team's Button", "publish this to 21st", "залей в наш
  регистр", "add a 21st theme/template", "log in to 21st", or wants to script
  21st from the shell/CI instead of the web UI. Auto-activate when a project has
  a components.json.
---

# 21st CLI — search, install & publish from the terminal

The `21st` CLI (`npx @21st-dev/cli`, bin `21st`) is the single command-line
surface for 21st.dev. Prefer it over hand-writing UI: search the catalog first,
install what fits, and publish reusable pieces back to the team.

## Auth (do this once)

Two interchangeable credentials, both sent as the API key:

1. **Login session** — `npx @21st-dev/cli login` opens the browser, saves a
   token to `~/.config/21st/auth.json`. Best for interactive/dev machines.
2. **API key** — `21st_sk_…` from **https://21st.dev/mcp** (or the studio
   `/studio/<username>/api-keys` page). Pass via `--api-key <key>` or the
   `TWENTYFIRST_TOKEN` / `API_KEY_21ST` env var. Best for CI.

`21st whoami` shows the signed-in account; `21st usage` shows tier + remaining
free quota; `21st logout` clears the saved login.

## Metering

Metadata (search, previews) is **free**. Retrieving component **code**
(`21st get`, install) and AI generation are metered per user: a small free daily
quota, then a paywall (`21st usage` shows what's left). Community access /
membership lifts the quota.

---

## Find & install

```bash
# Search everything (components + themes + templates)
21st search "pricing table" --limit 10
21st search button --type c        # components only (c | theme | template)
21st search dark --type theme
# Filters: --tag <slug> --color <bucket> --sort <s> --free|--paid
#          --author <username> --mine --liked   --json for machine output

# Print a component's code + demo (by the id from search)
21st get 143 [--json]

# Print a theme's CSS (by a theme id from `search --type theme`)
21st theme <id> [--json]

# Install a component into the current project (shadcn under the hood)
21st add <user>/<slug>            # e.g. 21st add shadcn/button
21st add @<team>/<slug>           # from a team library
# --print to dump instead of writing files
```

`21st add` resolves the registry item, writes it to `components/ui/<slug>.tsx`,
and installs npm deps with the project's package manager. Public/unlisted items
also work with stock shadcn: `npx shadcn@latest add "https://21st.dev/r/<user>/<slug>"`.

## Generate

```bash
21st generate "a glassy pricing section with a monthly/yearly toggle"
```

Generates a UI component with 21st AI (opens the browser to iterate). Metered on
its own daily bucket.

---

## Publish a component to your team library

Point the CLI at a React file — name, slug, tags and demo are auto-detected.

```bash
21st publish ./PinList.tsx \
  --description "A pinned-items list with drag reordering" \
  --to default                       # target team registry slug (optional)
```

| Flag | Meaning |
|---|---|
| `--description "…"` | **Required**, 10+ chars: what it does + when to use it. |
| `--name`, `--slug`, `--tags a,b` | Override auto-detected name / URL slug / tags (auto: from default export, filename, imports). |
| `--demo <file>` | Demo file. Auto-found (`{Comp}.demo.tsx`, `demos/{slug}.tsx`, `demos/default.tsx`) or a trivial one is synthesised. |
| `--preview <img>` | Optional static thumbnail (png/jpg/webp). |
| `--registry ui\|hooks\|blocks\|icons` | Target sub-registry (default `ui`). |
| `--registry-dep <ref>` | Repeatable. A shadcn dep by bare name, URL, or `@namespace/name`. |
| `--public` / `--unlisted` / `--private` | Visibility. **Default `unlisted`** (shareable URL, not listed). Only use `--public` when the user explicitly asks — it goes through admin moderation. |

**Edit / re-publish:** run the same command with the **same slug** → upsert
("Updated"). Teammates always get the latest; no version flag. To change
**visibility**, re-publish with a different visibility flag. Confirm with the
user before overwriting if they meant a NEW component (change `--slug`).

The command prints the component URL and the install line
(`npx @21st-dev/cli add @<handle>/<slug>`).

---

## Teams, bookmarks & config

```bash
21st teams                          # your teams
21st team <teamId>                  # a team's libraries
21st team-lists <teamId>            # a team's shared bookmark lists
21st team-components <teamId> [--library <id>]

21st bookmarks [--type component|theme|template]
21st bookmark <id> --type <kind> [--remove]     # heart / un-heart
21st lists                          # your bookmark lists
21st list <listId>
21st list-new <name>
21st list-add <listId> <id> --type <kind> [--remove]

21st init --client cursor|claude|codex|vscode|windsurf [--write]   # write MCP config
21st install-skill                  # install this + the publish skill globally
```

---

## Publish themes & templates

```bash
# A theme is a CSS file that must define BOTH :root and .dark token sets.
21st publish-theme ./my-theme.css --name "Midnight" [--tags dark,minimal]

# A template is a metadata listing (URLs, not files). Lands in draft for review.
21st publish-template "SaaS Starter" \
  --site https://demo.example.com \
  --preview https://example.com/thumb.png \
  [--description "…"] [--price 49] [--buy-url https://…] [--video https://…]
```

## Edit & delete your published items

```bash
# Update visibility / metadata on YOUR OWN item (owner-only, 404 otherwise)
21st edit <slug> --type component --visibility public|unlisted|private \
  [--description "…"] [--tags a,b]
21st edit <theme-id> --type theme [--visibility public|private] [--name "New Name"]
21st edit <template-id> --type template [--name N] [--description D]

# Remove an item. ALWAYS requires --yes. Semantics differ:
#   component/theme -> unpublished (soft, reversible via re-publish/edit)
#   template        -> PERMANENT hard delete
21st delete <id|slug> --type component|theme|template --yes
```

Notes: publish/edit/delete need a real API key (`21st_sk_`) — a `21st login`
session token is not accepted on these management endpoints. Template
visibility is moderation-controlled (authors submit for review; `--visibility`
is ignored for templates). Anything else, point the user to their studio at
`https://21st.dev/studio/<username>`.

## Installing this skill

Cross-agent install (skills.sh, 70+ supported agents):

```bash
npx skills add 21st-dev/skill
```

For Claude Code and Codex, this skill is also bundled in the `21st` plugin,
which additionally wires up the 21st MCP server in one step — see the sibling
`claude-code-plugin/` and `codex-plugin/` directories.
