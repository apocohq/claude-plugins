# CLAUDE.md

This is a Claude Code plugin marketplace named `apoco-plugins`.

## Repository structure

```
.claude-plugin/marketplace.json   # Marketplace catalog — lists all plugins and their sources
plugins/                          # Each subdirectory is a plugin
  mqtt/                           # MQTT channel plugin (MCP server, TypeScript/Bun)
```

## Key concepts

- **Marketplace file**: `.claude-plugin/marketplace.json` defines the marketplace name, owner, and plugin entries. Each plugin entry has a `name`, `source` (relative path to the plugin dir), and metadata.
- **Plugin manifest**: Each plugin has `.claude-plugin/plugin.json` with its own name, description, version, and keywords.
- **Skills**: Markdown files under `plugins/<name>/skills/<skill>/SKILL.md` that define slash commands.
- **MCP servers**: Configured in `.mcp.json` within each plugin directory.

## Conventions

- Plugin names are kebab-case.
- Plugin sources use relative paths (`./plugins/<name>`) since plugins live in-repo.
- The marketplace `metadata.pluginRoot` is set to `./plugins`.
- The mqtt plugin runs on Bun and uses the `@modelcontextprotocol/sdk` package.

## Validation

Run `claude plugin validate .` from the repo root to check marketplace and plugin manifests.
