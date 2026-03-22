# apoco-plugins

A [Claude Code plugin marketplace](https://code.claude.com/docs/en/plugin-marketplaces) by Apoco.

## Plugins

| Plugin | Description |
| --- | --- |
| [mqtt](./plugins/mqtt/) | MQTT channel for Claude Code — pub/sub bridge with topic-based access control. |

## Install

Add the marketplace:

```
/plugin marketplace add https://github.com/apocohq/claude-plugins
```

Install a plugin:

```
/plugin install mqtt@apoco-plugins
```

## Adding a plugin

Each plugin lives in its own directory under `plugins/`. The marketplace catalog is at `.claude-plugin/marketplace.json`.

To add a new plugin:

1. Create `plugins/<name>/` with a `.claude-plugin/plugin.json` manifest and your plugin files (skills, hooks, MCP servers, etc.).
2. Add an entry to `.claude-plugin/marketplace.json` in the `plugins` array.
3. Validate with `/plugin validate .` or `claude plugin validate .`.

## License

Apache-2.0
