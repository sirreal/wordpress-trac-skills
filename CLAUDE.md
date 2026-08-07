# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with
code in this repository.

## Project Overview

Claude Code plugin marketplace repository. Plugins are published under the
`sirreal` marketplace namespace.

**Marketplace install:**

```text
/plugin marketplace add sirreal/agent-skills
/plugin install wordpress-trac@sirreal
```

## Repository Structure

```text
.claude-plugin/
└── marketplace.json        # Marketplace metadata and plugin list
plugins/
├── wordpress-trac/
│   ├── .mcp.json           # Plugin-shipped Trac MCP server
│   ├── README.md
│   └── skills/
│       └── wp-trac-fix/    # Defect reproduction workflow and references
└── phptools-lsp/
```

There is no marketplace-level or plugin-level `plugin.json` in this
repository. Plugin metadata and versions live in
`.claude-plugin/marketplace.json`.

## Development

Validate the marketplace from the repository root:

```bash
claude plugin validate .
```

Load the WordPress Trac plugin directly:

```bash
claude --plugin-dir ./plugins/wordpress-trac
```

MCP configuration changes are not picked up live. Run `/reload-plugins` in the
Claude Code session or restart it after changing `.mcp.json`. Confirm the
`wp-trac` approval prompt appears, `/mcp` lists the server, the
`mcp__plugin_wordpress-trac_wp-trac__*` tools resolve, and `/wp-trac-fix` is
the only slash command supplied by this plugin.

## Plugin Patterns

Remote MCP servers are declared in `.mcp.json` at the plugin root:

```json
{
  "mcpServers": {
    "wp-trac": {
      "type": "http",
      "url": "https://wordpress-trac-mcp-server-prod.a8c-aiops.workers.dev/mcp"
    }
  }
}
```

Plugin-shipped servers use Claude Code's per-server approval flow. Do not also
declare the same server inline in a plugin manifest.

Skills use YAML frontmatter for routing metadata. `wp-trac-fix` is an
instruction-only workflow with no `allowed-tools` entry and no bundled
scripts. Keep references relative to its skill directory.
