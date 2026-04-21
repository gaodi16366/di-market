# di-market

A community plugin marketplace for Claude Code. Plugins are distributed via `.claude-plugin/marketplace.json`.

> Spec source: https://code.claude.com/docs/en/plugin-marketplaces
> If this doc looks stale, fetch the latest from that URL and update this file.

## Marketplace file schema

`.claude-plugin/marketplace.json` — required fields:

```json
{
  "name": "di-market",
  "owner": {
    "name": "string (required)",
    "email": "string (optional)"
  },
  "metadata": {
    "description": "string",
    "version": "string",
    "pluginRoot": "./plugins"
  },
  "plugins": []
}
```

**Reserved marketplace names** (cannot be used): `claude-code-marketplace`, `claude-code-plugins`, `claude-plugins-official`, `anthropic-marketplace`, `anthropic-plugins`, `agent-skills`, `knowledge-work-plugins`, `life-sciences`. Names impersonating official marketplaces are also blocked.

## Plugin entry fields

Each entry in `plugins[]`:

| Field        | Required | Description |
|--------------|----------|-------------|
| `name`       | Yes      | Kebab-case identifier. Users install via `/plugin install <name>@di-market` |
| `source`     | Yes      | Where to fetch the plugin (see sources below) |
| `description`| No       | Brief description |
| `version`    | No       | Semver string |
| `author`     | No       | `{ "name": "...", "email": "..." }` |
| `homepage`   | No       | Documentation or source URL |
| `repository` | No       | Source code URL |
| `license`    | No       | SPDX identifier e.g. `Apache-2.0`, `MIT` |
| `keywords`   | No       | Array of strings |
| `category`   | No       | String for organization |
| `strict`     | No       | Boolean (default `true`). If `false`, marketplace entry is the full definition — plugin must not have a `plugin.json` declaring components |
| `skills`     | No       | Path(s) to skill directories containing `<name>/SKILL.md` |
| `commands`   | No       | Path(s) to flat `.md` skill files or directories |
| `agents`     | No       | Path(s) to agent files |
| `hooks`      | No       | Hooks config or path to hooks file |
| `mcpServers` | No       | MCP server configs or path to config |
| `lspServers` | No       | LSP server configs or path to config |

## Plugin sources

### Relative path (same repo)
```json
{ "source": "./skills/skill-creator" }
```
Must start with `./`. Resolved relative to marketplace root (the directory containing `.claude-plugin/`). Does **not** work with URL-based marketplace distribution — only works when the marketplace is cloned via git.

### GitHub
```json
{
  "source": {
    "source": "github",
    "repo": "owner/repo",
    "ref": "v2.0.0",
    "sha": "a1b2c3d4..."
  }
}
```
`ref` = branch or tag (optional). `sha` = full 40-char commit SHA to pin exact version (optional).

### Git URL
```json
{
  "source": {
    "source": "url",
    "url": "https://gitlab.com/team/plugin.git",
    "ref": "main",
    "sha": "a1b2c3d4..."
  }
}
```

### Git subdirectory (monorepo)
```json
{
  "source": {
    "source": "git-subdir",
    "url": "https://github.com/acme/monorepo.git",
    "path": "tools/claude-plugin",
    "ref": "v2.0.0",
    "sha": "a1b2c3d4..."
  }
}
```
Uses sparse checkout — only fetches the subdirectory.

### npm
```json
{
  "source": {
    "source": "npm",
    "package": "@acme/claude-plugin",
    "version": "2.1.0",
    "registry": "https://npm.example.com"
  }
}
```

## Plugin directory structure

```
skills/<plugin-name>/
├── SKILL.md          # Required — YAML frontmatter (name, description) + instructions
├── LICENSE.txt       # Required — include the plugin's license
├── agents/           # Optional — subagent instruction files
├── assets/           # Optional — templates, icons, fonts
├── references/       # Optional — docs loaded into context
└── scripts/          # Optional — executable helper scripts
```

Skills use `${CLAUDE_PLUGIN_ROOT}` to reference files at runtime (plugins are copied to a cache on install).

## Strict mode

- `strict: true` (default) — plugin's own `plugin.json` is the authority; marketplace entry can add extra components on top.
- `strict: false` — marketplace entry is the entire definition; plugin must NOT have a `plugin.json` that declares components.

## Adding plugins: checklist

1. Add plugin files to `skills/<name>/` (or `mcp-servers/`, `hooks/`, etc.)
2. Include `LICENSE.txt` in the plugin directory
3. Add entry to `plugins[]` in `.claude-plugin/marketplace.json` with `author` and `homepage` if not authored by us
4. Pin third-party plugins with `sha` to an exact commit
5. Run `claude plugin validate .` to verify

## How users add this marketplace

```json
// ~/.claude/settings.json
{
  "extraKnownMarketplaces": {
    "di-market": {
      "source": {
        "source": "github",
        "repo": "gaodi16366/di-market"
      }
    }
  }
}
```

Then install a plugin:
```
/plugin install skill-creator@di-market
```
