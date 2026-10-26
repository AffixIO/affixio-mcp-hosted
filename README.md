<p align="center">
  <img src="assets/logo.png" alt="AffixIO logo" width="120" height="120">
</p>

# AffixIO MCP (hosted)

Verification, permission checks, attestations and Merkle audit evidence through an account-scoped OAuth connection.

See the [focused tool install guide](hosted-guide.md) for proof consumption, scopes and result interpretation, and the [directory profile](directory-profile.md) for listing details.

AffixIO is a **hosted, remote MCP server**. There is nothing to install or run locally: point your MCP client at the endpoint below and sign in with AffixIO Studio.

| | |
|---|---|
| Endpoint | `https://www.affix-io.com/mcp/` |
| Transport | Streamable HTTP |
| Authentication | OAuth 2.1 with PKCE, dynamic client registration, sign in via AffixIO Studio |
| Scopes | `mcp:read`, `mcp:write` |
| Tools | 102 |
| Official MCP Registry | `com.affix-io/affixio` |
| Quickstart | https://www.affix-io.com/docs/quickstart/ |

> **Note:** This repository contains listing material only (documentation, example client configuration, registry metadata and logo). AffixIO is a hosted service and its server source code is not published here or anywhere else.

## What AffixIO does

- **Zero-knowledge proofs for AI agents.** Prove a yes or no fact (for example "over 18", "road legal", "within spending limit") without handing the underlying personal data to the agent or the model.
- **ML-DSA-65 attestations.** Supported attestation operations can sign verification evidence. Not every tool result is a signed attestation. Public verification keys support checking the corresponding signatures.
- **Single-use proof IDs.** Proofs can be marked spent, so the same proof cannot be replayed.
- **Agent trust.** Enrol an AI agent, verify its credential and check whether it may perform a specific action against a named resource before it acts.
- **Auditable receipts.** Verification receipts are anchored in a Merkle audit tree with inclusion proofs.

## Access and usage

Hosted access requires an account and an active account-owned API key selected during consent. API usage follows your current plan and limits; see [pricing](https://www.affix-io.com/pricing/). The hosted service processes submitted tool inputs remotely. Avoid submitting unnecessary personal data.

## Connect

AffixIO uses OAuth. The first time your client connects it will open a browser window for you to sign in to AffixIO Studio and approve access. No API key needs to go in your config file.

### Clients that support remote servers (Cursor, Windsurf, Raycast and most modern clients)

```json
{
  "mcpServers": {
    "affixio": {
      "url": "https://www.affix-io.com/mcp/"
    }
  }
}
```

### Cline

Add this to `cline_mcp_settings.json` (MCP Servers, Configure, Edit configuration):

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

Step-by-step instructions for agents are in [`llms-install.md`](llms-install.md).

### VS Code (GitHub Copilot)

`.vscode/mcp.json`:

```json
{
  "servers": {
    "affixio": {
      "type": "http",
      "url": "https://www.affix-io.com/mcp/"
    }
  }
}
```

### Claude Desktop

Add AffixIO as a custom connector with the URL `https://www.affix-io.com/mcp/`, or use the `mcp-remote` bridge below in `claude_desktop_config.json`.

### Fallback for stdio-only clients (`mcp-remote`)

If your client only supports local stdio servers, use [`mcp-remote`](https://www.npmjs.com/package/mcp-remote), which handles the OAuth sign-in for you:

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

Ready-made files are in [`examples/`](examples/).

## Tools

The server currently exposes 102 tools. A selection, grouped by area:

| Area | Example tools |
|---|---|
| Core verification | `affix_verify`, `affix_verify_policies`, `affix_verify_reason_codes`, `affix_verify_receipts`, `affix_aggregate_verify`, `affix_proof_ref_verify` |
| Zero-knowledge circuits | `affix_list_circuits`, `affix_witness_prepare`, `affix_circuit_prove`, `affix_circuit_verify` |
| Post-quantum attestation | `affix_attestation_public_key` |
| Single-use proofs | `affix_spent_status`, `affix_spent_revoke` |
| Agent trust | `affix_agent_trust_enrol`, `affix_agent_trust_verify`, `affix_agent_trust_authorise`, `affix_agent_trust_audit`, `affix_agent_trust_badge` |
| Delegation and gates | `affix_delegate_issue`, `affix_delegate_verify`, `affix_gate_verify`, `affix_gate_quorum_start`, `affix_gate_multifactor_start` |
| Merkle audit | `affix_merkle_root`, `affix_merkle_proof`, `affix_merkle_verify_proof`, `affix_merkle_audit`, `affix_merkle_audit_batch` |
| Tokens and carriers | `affix_token_issue`, `affix_token_verify`, `affix_list_carriers` |
| Document parsing | `affix_mrz_parse`, `affix_aamva_parse` |
| Agentic payments | `agentic_pay_catalogue`, `agentic_pay_reverify`, `agentic_pay_charge` |
| Privacy guard | `edge_reject_pii`, `edge_audit` |
| UK vehicles | `vehicle_lookup`, `vehicle_check_road_legal`, `vehicle_check_clean_air`, `vehicle_check_fleet`, `vehicle_prove_circuits`, `road_legal_check`, `clean_air_check` |
| Eligibility passes | `age_check`, `ticket_check`, `travel_check`, `rent_check`, `stay_check`, `finance_check`, `money_check`, `right_pass_check`, `clearance_check`, `alevel_check_offer`, `ballot_session` |
| Discovery | `tools_catalogue`, `tools_search`, `tools_detail`, `affix_health` |

Ask your agent to call `tools_catalogue` or simply list the server's tools to see the full set.

## Example prompts

- "Use AffixIO to prove this user is over 18 without sharing their date of birth, and show me the attestation."
- "Is vehicle registration AB12 CDE road legal right now? Return the signed proof."
- "Enrol this agent with AffixIO agent trust and check it may run `checkout` against `store:orders`."
- "Check whether proof ID xyz has already been spent."

## Links

- Website: https://www.affix-io.com/
- Quickstart: https://www.affix-io.com/docs/quickstart/
- Documentation: https://www.affix-io.com/docs/
- MCP page: https://www.affix-io.com/mcp/
- Official MCP Registry: `com.affix-io/affixio`

## Security and privacy

- Authentication is OAuth 2.1 with PKCE. Tokens are issued per client and scoped to `mcp:read` and `mcp:write`.
- Account, key, OAuth and webhook administration is not exposed through MCP.
- A signature protects a payload; it does not by itself establish that a source is true. See the documentation for the evidence boundaries of each proof mode.

## Contact

affixio@mail.grokbot.com

## Licence

The documentation, example configuration and metadata in this repository are licensed under [CC BY 4.0](LICENSE). The AffixIO service, its server software and the AffixIO name and logo are proprietary and are not covered by that licence.
