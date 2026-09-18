# n8n-agent-workflows

Claude Code is wired to an [n8n-mcp](https://github.com/czlonkowski/n8n-mcp) server,
so the assistant can look up n8n node documentation and — once credentials are set —
read, build, validate and deploy workflows on your n8n instance.

## Setup

1. **Copy the env template and fill it in.**

   ```bash
   cp .env.example .env
   ```

   - `N8N_API_URL` — base URL of your instance, e.g. `https://acme.app.n8n.cloud`.
     No trailing slash and no `/api/v1` suffix; n8n-mcp appends that itself.
   - `N8N_API_KEY` — n8n **Settings → API → Create an API key**.

   `.env` is gitignored. Never commit it.

2. **Export the variables, then start Claude Code from this directory.**

   `.mcp.json` reads these from the *process* environment — Claude Code does not
   load `.env` on its own, so source it first:

   ```bash
   set -a; source .env; set +a
   claude
   ```

3. **Approve the project MCP server.** The first time you start Claude Code here it
   asks whether to trust the servers in `.mcp.json`. Approve `n8n`.

4. **Verify.** Run `/mcp` inside Claude Code, or from the shell:

   ```bash
   claude mcp list
   ```

   Then ask the assistant to run `n8n_health_check`.

## What you get

Without credentials the server still runs in documentation-only mode — 7 tools
covering node search, node schemas, templates and workflow validation.

With `N8N_API_URL` and `N8N_API_KEY` set, ~21 additional management tools appear:
create/update/delete workflows, list and inspect executions, manage folders,
credentials and data tables, and run instance security audits.

`N8N_MCP_ACCESS_TOKEN` is optional and **separate** from `N8N_API_KEY`. It comes
from **Settings → Instance-level MCP** and is only needed for `n8n_manage_agents`
and `n8n_explore_node_resources`.

## Notes

- **Back up before letting AI touch a production workflow.** Export the workflow
  first; validate changes before they go live.
- **Telemetry is disabled by default** in `.mcp.json`. Upstream, n8n-mcp sends
  pseudonymous usage data including workflow structures and the intent text passed
  to update tools. Set `N8N_MCP_TELEMETRY_DISABLED=false` to opt back in. See the
  upstream [privacy policy](https://github.com/czlonkowski/n8n-mcp/blob/main/PRIVACY.md).
- **Read-only mode.** To stop the assistant from writing to your instance, add
  `DISABLED_TOOLS` to the server's `env` block in `.mcp.json`, and pair it with a
  read-only n8n API key. The upstream README documents the exact tool list.
- The server version is pinned to `n8n-mcp@2.87.0` in `.mcp.json` so a background
  upstream release cannot change behaviour mid-project. Bump it deliberately.
