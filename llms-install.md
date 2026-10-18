# Installing the AffixIO MCP server (instructions for AI agents such as Cline)

AffixIO is a **hosted remote MCP server**. Do not clone this repository, run `npm install`, build anything or look for a local entry point. There is no server code here. You only need to add a remote server entry to the client's MCP settings.

## Facts

- Server name: `affixio`
- URL: `https://www.affix-io.com/mcp/` (keep the trailing slash)
- Transport: Streamable HTTP
- Authentication: OAuth 2.1 with PKCE and dynamic client registration. No API key, no environment variables.
- Official MCP Registry name: `com.affix-io/affixio`

## Steps for Cline

1. Open Cline's MCP settings file, `cline_mcp_settings.json` (MCP Servers, Configure, "Configure MCP Servers").
2. Add the following entry inside `mcpServers`, keeping any existing servers:

```json
{
  "mcpServers": {
    "affixio": {
      "type": "streamableHttp",
      "url": "https://www.affix-io.com/mcp/"
    }
  }
}
```

3. Save the file. Cline will connect and the server will ask for authentication.
4. When prompted, click the "Authenticate" button for `affixio` in the MCP Servers panel. A browser window opens at AffixIO Studio. Ask the user to sign in (or create a free account) and approve access. Do not try to enter credentials yourself.
5. After approval, the server should show as connected with 102 tools. Verify by calling the `affix_health` tool.

## If the client cannot do OAuth for remote servers

Use the `mcp-remote` bridge instead. It runs locally through `npx` and opens the browser sign-in itself:

```json
{
  "mcpServers": {
    "affixio": {
      "command": "npx",
      "args": ["-y", "mcp-remote", "https://www.affix-io.com/mcp/"]
    }
  }
}
```

This needs Node.js 18 or later.

## First things to try

- `affix_health`: confirm the service is reachable.
- `tools_catalogue`: list the available capabilities.
- `affix_verify_policies` then `affix_verify`: run a first yes or no proof.
- `affix_attestation_public_key`: fetch the ML-DSA-65 public key for offline verification.

New accounts get free proofs: 150 for AI agents, 100 for humans, no card needed.

Quickstart: https://www.affix-io.com/docs/quickstart/
Support: affixio@mail.grokbot.com
