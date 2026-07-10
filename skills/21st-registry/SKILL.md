---
name: 21st-registry
description: >-
  Publish your own work to 21st.dev and manage it from the terminal with the
  `21st` CLI (`@21st-dev/cli`): publish a React component to a team library,
  publish a CSS theme or a template listing, edit / unpublish / delete your
  published items, and read/replace your public profile page (bento board).
  Triggers when the user says "publish/share/upload this to 21st", "залей в
  наш регистр", "опубликуй компонент/тему/темплейт", "share with team", "make
  this reusable", "unpublish/edit/delete my component", "change its
  visibility", "add this to my 21st profile/bento", "update my profile page",
  "обнови мой профиль на 21st", "добавь блок в бенто".
  For finding and installing existing items use `21st-cli-use`; for turning a
  project's design tokens into a theme use `21st-design-sync`.
---

# Publish & manage on the 21st registry

Publish components, themes and templates to 21st.dev, and manage what you've
already published, all through the unified `21st` CLI (`@21st-dev/cli`).

## Pre-flight (always)

1. **Auth needs a real API key.** Publish, edit and delete are management
   endpoints: they accept a `21st_sk_…` key **only**, not a `21st login` session
   token. Get one at **https://21st.dev/mcp** (or **https://21st.dev/settings/api-keys**)
   and pass it via `--api-key 21st_sk_…` or the `TWENTYFIRST_TOKEN` /
   `API_KEY_21ST` env var. If the user has no key, point them there.
2. The CLI is the unified `@21st-dev/cli` (bin `21st`). Don't reinvent — use it.
3. **Search before publishing** (`21st search "<query>"`) so you don't add a
   duplicate to the library.

---

## Publish a component

The positional file path triggers auto-detection — name from the default export,
slug from the filename, tags from imports, demo auto-found or synthesised. In
~95% of cases this is enough:

```bash
21st publish ./path/to/Component.tsx \
  --to default \
  --description "1-2 sentences: what it does and when to use it"
```

### Decide visibility — default to unlisted

| User says… | Flag |
|---|---|
| "share with team", "залей нам", default for any unqualified ask | *(none — defaults to `unlisted`)* |
| "shareable link but not listed" | `--unlisted` |
| "publish publicly", "make it public on 21st" | `--public` |
| "restrict to the team library", "keep it internal" | `--private` |

**Never use `--public` without an explicit "publish publicly".** Public
components go through admin moderation and appear in the 21st library.

### Flag reference

| Flag | When to use |
|---|---|
| `--description "…"` | **Required**, 10+ chars: what it does + when to use it. Never fabricate — read the code or ask the user. |
| `--name`, `--slug`, `--tags a,b` | Override auto-detected name / URL slug / 1-5 tags. |
| `--demo <file>` | Demo file. Auto-found (`{Comp}.demo.tsx`, `demos/{slug}.tsx`, `demos/default.tsx`) or a trivial one is synthesised. A real demo gives a much better preview. |
| `--preview <img>` | Optional static thumbnail (png/jpg/webp); the library also renders a live iframe. |
| `--registry ui\|hooks\|blocks\|icons` | Target sub-registry (default `ui`). |
| `--to <library-slug>` | Target team library (e.g. `--to default`). Omit for the team's default. |
| `--registry-dep <ref>` | Repeatable. A shadcn dep by bare name, URL, or `@namespace/name`. |
| `--public` / `--unlisted` / `--private` | Visibility (default `unlisted`). |

The command prints the component URL and the install line
(`npx @21st-dev/cli add @<handle>/<slug>`).

### Updating vs a new component

Re-run with the **same slug** → upsert (prints "Updated"). Teammates always get
the latest; no version flag. If the user meant a NEW component but the slug
collides, confirm before overwriting and suggest a different `--slug`.

### Hard rules for agents

- ❌ Never `--public` without an explicit instruction.
- ❌ Never fabricate a description; never publish a file with API keys, secrets,
  or unsaved edits (flush first).
- ✅ Always search first; always ship a useful demo when you can.

---

## Publish a theme

A theme is a CSS file that must define **both** a `:root` and a `.dark` block of
token values. It publishes as a **public** community theme.

```bash
21st publish-theme ./my-theme.css --name "Midnight" [--tags dark,minimal]
```

To generate that CSS from the current project's design tokens instead of writing
it by hand, use the `21st-design-sync` skill.

## Publish a template

A template is a metadata listing (URLs, not files). It lands in `draft` for
moderation.

```bash
21st publish-template "SaaS Starter" \
  --site https://demo.example.com \
  --preview https://example.com/thumb.png \
  [--description "…"] [--price 49] [--buy-url https://…] [--video https://…] [--tags 1,2]
```

---

## Edit & delete your published items

```bash
# Update YOUR OWN item (owner-only; 404 otherwise).
21st edit <slug> --type component --visibility public|unlisted|private \
  [--description "…"] [--tags a,b]
21st edit <theme-id> --type theme [--visibility public|private] [--name "New Name"] [--tags a,b]
21st edit <template-id> --type template [--name N] [--description D] [--tags 1,2]

# Remove an item. ALWAYS requires --yes. Semantics differ by type:
#   component -> unpublished (visibility set to private; reversible via edit/re-publish)
#   theme     -> unpublished (removed from the marketplace; reversible)
#   template  -> PERMANENT hard delete
21st delete <id|slug> --type component|theme|template --yes
```

Notes: template visibility is moderation-controlled (`--visibility` is ignored
for templates). Themes rename via `--name` and only support `public|private`.
Anything the CLI can't do, point the user to their studio at
`https://21st.dev/studio/<username>`.

---

## Manage your profile page (bento board)

Your public profile at `21st.dev/@you` can show a bento-style board of blocks
instead of the classic layout. `set` is a FULL REPLACE (like `--tags` on
`edit`) — read the current board first if you want to keep existing content.

```bash
21st profile get [--json]                 # read your current board (as blocks)
21st profile set --file board.json        # replace it (JSON array, or pipe it via stdin)
21st profile set --clear                  # revert to the classic (non-bento) profile
21st profile upload ./cover.png           # upload a png/jpg/gif/webp, prints a url
```

`board.json` is an array of blocks, top to bottom:

```json
[
  { "type": "component", "demoId": 143 },
  { "type": "social", "url": "https://x.com/you" },
  { "type": "note", "text": "Building UI for 21st.dev" },
  { "type": "hire", "headline": "Work with me", "url": "https://cal.com/you" },
  { "type": "divider" },
  { "type": "image", "mediaUrl": "<url from `profile upload`>" }
]
```

Block types: `component` (your OWN demoId — from `21st search --mine` or
`21st get`), `social` (a link card, icon auto-detected from the domain),
`note` (text), `hire` / `pro` / `support` (headline/body/url/email/cta),
`image` (`mediaUrl` from `profile upload` — a foreign url is dropped, not an
error), `divider` (splits the board into a new section). Every block accepts
optional `w`/`h` (grid units, 1-3 wide / 1-2 tall) to size it.

Reordering = re-post the blocks array in the order you want; the board packs
top-to-bottom, left-to-right in array order.

## Teams & config

```bash
21st teams                          # your teams
21st team <teamId>                  # a team's libraries
21st team-lists <teamId>            # a team's shared bookmark lists
21st team-components <teamId> [--library <id>]

21st init --client cursor|claude|codex|vscode|windsurf [--write]   # write MCP config
```
