# agentic-marketplace

A [Claude Code](https://claude.com/claude-code) **plugin marketplace** — a community
collection of plugins (skills, agents, commands, hooks) for agentic coding. Contributors
add their own plugins; users install what they want with `/plugin`.

## Using this marketplace

Add the marketplace once, then install any plugin from it:

```
/plugin marketplace add elviin/agentic-marketplace
/plugin install grill-yourself@agentic-marketplace
```

`/plugin marketplace add` also accepts a full git URL or a local path to a clone. After
installing, restart Claude Code if prompted so the plugin's skills and agents load.

## Available plugins

| Plugin | What it does |
|-|-|
| [`grill-yourself`](plugins/grill-yourself) | Adversarial self-review — the model submits its own plan to a `grill-master` subagent that tears it apart over several rounds until genuine consensus (or disputes escalate to you). Bundles the `grill-yourself` skill and the `grill-master` agent it spawns. |

## Repository layout

```
.claude-plugin/
  marketplace.json           # the catalog — one entry per plugin
plugins/
  <plugin-name>/
    .claude-plugin/
      plugin.json            # the plugin manifest
    skills/<name>/SKILL.md   # skills the plugin ships (auto-discovered)
    agents/<name>.md         # agents the plugin ships (auto-discovered)
    commands/<name>.md       # slash commands (optional)
    hooks/hooks.json         # hooks (optional)
```

Each plugin is a self-contained directory under `plugins/`. Claude Code auto-discovers
`skills/`, `agents/`, `commands/`, and `hooks/` at the plugin root, so the manifest only
needs to declare metadata.

## Contributing a plugin

1. **Create your plugin directory** `plugins/<your-plugin>/` and add its manifest at
   `plugins/<your-plugin>/.claude-plugin/plugin.json`:

   ```json
   {
     "name": "your-plugin",
     "description": "What it does and when to use it.",
     "version": "1.0.0",
     "author": { "name": "you" },
     "keywords": ["tag"]
   }
   ```

   Only `name` is strictly required; the rest is recommended. `name` must match the
   directory name.

2. **Add your components** at the plugin root — no extra config needed, they're
   auto-discovered:
   - a skill → `skills/<name>/SKILL.md` (with `name`/`description` frontmatter)
   - an agent a skill spawns → `agents/<name>.md` (with `name`/`description`/`tools` frontmatter)
   - a slash command → `commands/<name>.md`
   - hooks → `hooks/hooks.json`

3. **Register it in the catalog** — add an entry to the `plugins` array in
   [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json), pointing `source`
   at your plugin's directory (relative to the repo root):

   ```json
   {
     "name": "your-plugin",
     "description": "What it does.",
     "version": "1.0.0",
     "category": "workflow",
     "author": { "name": "you" },
     "source": "./plugins/your-plugin"
   }
   ```

4. **Add a row** to the **Available plugins** table above.

### Conventions

- One plugin per directory under `plugins/`; keep it self-contained (a skill and the agents
  it spawns ship together in the same plugin).
- `name` must be identical across the directory name, `plugin.json`, and the
  `marketplace.json` entry.
- Make `description:` trigger-focused — it's how Claude decides when a skill applies.
- Validate before opening a PR: `/plugin marketplace add ./` (from a clone), then
  `/plugin install your-plugin@agentic-marketplace` and confirm it loads.
