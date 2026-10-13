# Connect an MCP client

Use `https://inbox-mcp.com/mcp` with Streamable HTTP. InboxMCP supports OAuth authorization discovery, dynamic client registration and PKCE. The client opens the browser to sign in and request permissions. No shared API key belongs in a public configuration.

## Claude Code

```sh
claude mcp add --transport http inboxmcp https://inbox-mcp.com/mcp
```

Run `/mcp` and complete authentication. The separate [Claude plugin](https://github.com/breakcold/inboxmcp-claude) includes workflow skills:

```sh
claude plugin marketplace add breakcold/inboxmcp-claude
claude plugin install inboxmcp@inboxmcp
```

## Cursor and clients using mcpServers

```json
{
  "mcpServers": {
    "inboxmcp": {
      "url": "https://inbox-mcp.com/mcp"
    }
  }
}
```

Complete OAuth in the client's MCP settings. The [Cursor plugin](https://github.com/breakcold/inboxmcp-cursor) includes InboxMCP workflow skills.

## VS Code

In `.vscode/mcp.json`:

```json
{
  "servers": {
    "inboxmcp": {
      "type": "http",
      "url": "https://inbox-mcp.com/mcp"
    }
  }
}
```

Start the server and complete the OAuth prompt.

## Other remote MCP clients

Add the endpoint as a custom remote connector, then follow the browser sign-in. A client must support authenticated Streamable HTTP and the server's OAuth flow. A directory entry alone does not establish that every client is compatible.

## Discovery and verification

- Protected resource: `https://inbox-mcp.com/.well-known/oauth-protected-resource/mcp`
- Authorization server: `https://inbox-mcp.com/.well-known/oauth-authorization-server`
- Public runtime documentation: `https://inbox-mcp.com/api/docs`

An unauthenticated MCP request returns an OAuth challenge. After authorization, call `inbox_list_accounts` to verify access without sending any messages. Provider-specific tools appear for connected provider accounts; refresh discovery after adding an account.
