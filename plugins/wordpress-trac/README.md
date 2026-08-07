# WordPress Trac Plugin

A Claude Code plugin that connects to an Automattic-operated WordPress Trac
MCP server and provides a workflow for reproducing WordPress core defects.

The MCP tools need no Trac login, browser cookie, DevTools setup, local PHP
runtime, or curl extension. There is no session cookie to expire.

## Installation

```sh
claude plugin marketplace add sirreal/agent-skills
claude plugin install wordpress-trac@sirreal
```

## MCP server

The plugin connects to
`wordpress-trac-mcp-server-prod.a8c-aiops.workers.dev`. This is an
Automattic-operated service, not a WordPress.org service.

Claude Code asks you to approve the `wp-trac` MCP server before connecting on
first load. MCP configuration is not reloaded live; after installing or
updating the plugin, run `/reload-plugins` or restart Claude Code.

The server provides these tools:

| Tool | Purpose |
|---|---|
| `getTicket` | Read ticket metadata, description, comments, attachments, changesets, and linked GitHub pull requests. |
| `getChangeset` | Read a changeset and, optionally, its diff. |
| `searchTickets` | Search by text, component, milestone, status, or resolution. |
| `getTimeline` | Read recent site-wide Trac activity. |
| `getTracInfo` | List components, milestones, priorities, severities, ticket types, or statuses. |

Ask naturally; the MCP tools do not add slash commands:

```text
Tell me about Trac #30000
What changed in WordPress changeset 41062?
Find accepted HTML API tickets
Show WordPress Trac activity from the last seven days
```

### Current query limits

`searchTickets` supports `query`, `component`, `milestone`, `status`,
`resolution`, `limit`, and `page`, with at most 50 results per page. It does
not accept negated filters such as `status!=closed`. To search all open
tickets, query each open status separately: `new`, `assigned`, `accepted`,
`reopened`, and `reviewing`.

`getChangeset` returns at most 10,000 characters of diff content per call.

`getTimeline` currently returns only recent site-wide activity: at most 30 days
and 100 events. It has no author filter, historical end date, or pagination,
so contributor-specific results may be incomplete and older ranges cannot be
queried yet.

`getTicket` returns at most 50 comments. The `/wp-trac-fix` workflow requests
that maximum explicitly and reports when a longer discussion was truncated.

## `/wp-trac-fix <ticket-number>`

The plugin's only slash command reproduces and attempts a fix for a WordPress
core defect in an isolated worktree. It walks through setup, ticket reading,
reproduction, a fix under a roughly 100-line cap, and a structured outcome
report.

Additional prerequisites for `/wp-trac-fix`:

- A `WordPress/wordpress-develop` clone with an `upstream` remote pointing at
  `WordPress/wordpress-develop`.
- `envlite` available on `$PATH`.

```text
/wp-trac-fix 62345
```

See `skills/wp-trac-fix/SKILL.md` for the full workflow.
