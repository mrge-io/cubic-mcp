# cubic MCP server

[cubic](https://www.cubic.dev) is an AI code review tool for GitHub pull requests. Its hosted MCP server connects your AI agent to cubic review findings, repository documentation, codebase scans, and team learnings.

## What you can do

- Read open review findings on a pull request, including file locations, severity, and review status.
- Start a cloud review of a pull request.
- Resolve, reopen, or dismiss findings and record the outcome on the GitHub review thread.
- Read repository wikis and individual wiki pages.
- Browse codebase scan results, inspect issues, and update their triage status.
- Read the team's review learnings for a repository.
- Inspect organization subscriptions and members. Organization admins can also manage seats, roles, and supported billing settings.

MCP uses your cubic account's permissions. You need access to the relevant repository or organization; connecting the server does not grant additional access. Available tools depend on your permissions and subscription.

## Connect

| Setting | Value |
| --- | --- |
| Server name | `cubic` |
| URL | `https://www.cubic.dev/api/mcp` |
| Transport | Streamable HTTP |
| Authentication | OAuth (recommended) or a personal API key |

Use the URL exactly as shown, including `www`. The server is hosted by cubic, so no local server installation is needed.

### GitHub Copilot in VS Code

1. Open the Command Palette and run **MCP: Add Server**.
2. Choose **HTTP** and enter `https://www.cubic.dev/api/mcp`.
3. Name the server `cubic` and choose a user or workspace configuration.
4. Run **MCP: List Servers**, start cubic, and complete the browser OAuth sign-in.
5. Use Copilot's agent mode with the cubic tools enabled.

You can also add this portable configuration to `.mcp.json` in your project. If the file already exists, add the `cubic` entry inside its `mcpServers` object:

```json
{
  "mcpServers": {
    "cubic": {
      "type": "http",
      "url": "https://www.cubic.dev/api/mcp"
    }
  }
}
```

### Cursor

Add the following to `~/.cursor/mcp.json` for all projects, or `.cursor/mcp.json` for one project, then connect cubic in MCP settings and complete OAuth:

```json
{
  "mcpServers": {
    "cubic": {
      "url": "https://www.cubic.dev/api/mcp"
    }
  }
}
```

### Claude Code

```bash
claude mcp add --transport http --scope user cubic https://www.cubic.dev/api/mcp
```

Open Claude Code, run `/mcp`, choose cubic, and complete the browser OAuth flow.

### Codex CLI

```bash
codex mcp add cubic --url https://www.cubic.dev/api/mcp
codex mcp login cubic
codex mcp list
```

### Scripts, CI, and clients without OAuth

Generate a personal API key in [Settings > Integrations > MCP](https://www.cubic.dev/settings?tab=integrations&integration=mcp). Send it as a bearer token in the `Authorization` header and skip the client's OAuth login step.

For VS Code extension-host sessions, use an input in `.vscode/mcp.json` so the key is not written into the workspace configuration. For Agent Host sessions, use OAuth or a portable configuration with environment-based secrets; VS Code does not forward servers that require interactive input.

```json
{
  "inputs": [
    {
      "id": "cubic-api-key",
      "type": "promptString",
      "description": "cubic personal API key",
      "password": true
    }
  ],
  "servers": {
    "cubic": {
      "type": "http",
      "url": "https://www.cubic.dev/api/mcp",
      "headers": {
        "Authorization": "Bearer ${input:cubic-api-key}"
      }
    }
  }
}
```

For scripts and CI, load the key from your secret store. Keys are personal and carry the creating user's permissions. You can revoke or regenerate a key from the same settings page.

## Use it

After connecting, try these prompts, replacing the repository and PR with ones you can access:

- "Use cubic to list the open review issues on https://github.com/OWNER/REPO/pull/NUMBER."
- "Start a cubic review of PR #42 in acme/backend."
- "List wiki pages for acme/backend and show me the authentication system page."
- "Show codebase scan issues in acme/backend with severity at least 7."
- "What review learnings apply to acme/backend?"

`get_pr_issues` reads findings and review status; it does not start a review. An empty findings list means the current commit has no open findings only when its review completed and no newer commits exist. Use `trigger_pr_review` to start a cloud review.

Use PR issue IDs from `get_pr_issues` with `update_pr_issue_status`; this updates the GitHub review thread and queues an attributed reply. Use codebase scan issue IDs from `get_scan` with `get_issue` or `update_issue_status`.

For local code reviews, use the [cubic CLI](https://docs.cubic.dev/ide/cli-review). [cubic skills](https://docs.cubic.dev/ide/skills) add workflows for investigating and fixing findings from your agent.

## Documentation and support

- [Full MCP documentation, available tools, and troubleshooting](https://docs.cubic.dev/ide/mcp-server)
- [VS Code MCP setup and configuration](https://code.visualstudio.com/docs/agent-customization/mcp-servers)
- [cubic website](https://www.cubic.dev)
- [Official MCP Registry entry](https://registry.modelcontextprotocol.io/v0.1/servers/dev.cubic%2Fcubic/versions/latest)
- [Contact cubic support](mailto:support@cubic.dev)

This repository contains the public documentation and registry metadata for cubic's hosted MCP service. The registry name is `dev.cubic/cubic` and its metadata is in [server.json](server.json).
