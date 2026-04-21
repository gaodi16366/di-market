# di-market

A community marketplace for Claude Code plugins.

## How to add this marketplace to Claude Code

Add the following to your `~/.claude/settings.json`:

```json
{
  "extraKnownMarketplaces": {
    "di-market": {
      "source": {
        "source": "git",
        "url": "https://github.com/gaodi16366/di-market.git"
      }
    }
  }
}
```

## Available plugins

See [`.claude-plugin/marketplace.json`](.claude-plugin/marketplace.json) for the full registry.

### Skills

| Plugin | Description |
|--------|-------------|
| [skill-creator](skills/skill-creator/SKILL.md) | Create new skills, modify and improve existing skills, and measure skill performance |

## Plugin types

This marketplace supports the following plugin types:

- **Skills** (`skills/`) — Claude Code skills loaded via `SKILL.md`
- **MCP Servers** (`mcp-servers/`) — Model Context Protocol servers *(coming soon)*
- **Hooks** (`hooks/`) — Event-driven automation hooks *(coming soon)*

## Contributing

To submit a plugin, open a pull request with your plugin added to the appropriate directory and registered in `.claude-plugin/marketplace.json`.

## License

Each plugin carries its own license. See the `LICENSE.txt` in each plugin directory.
