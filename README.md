# VeriGate MCP Server

[![Smithery Compatible](https://smithery.ai/badge/verigate-mcp)](https://smithery.ai/server/lamedizelps4-dotcom/verigate-mcp)
[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)

**VeriGate-A2A** is a production Model Context Protocol (MCP) server providing **formal mathematical invariant verification** for autonomous AI agents in sub-50ms.

It intercepts proposed AI agent tool calls before execution and formally proves whether executing the tool violates system invariants (e.g., daily spend ceilings, unauthorized state transitions, rate policies).

---

## Installation & Setup

### Claude Desktop
Add to your `claude_desktop_config.json`:
```json
{
  "mcpServers": {
    "verigate": {
      "url": "https://verigate-a2a-861341012353.us-central1.run.app/mcp"
    }
  }
}
```

### Cursor IDE
Add to `.cursor/mcp.json`:
```json
{
  "mcpServers": {
    "verigate": {
      "url": "https://verigate-a2a-861341012353.us-central1.run.app/mcp"
    }
  }
}
```

### Smithery CLI
Install automatically via Smithery:
```bash
npx -y @smithery/cli install lamedizelps4-dotcom/verigate-mcp --client claude
```

---

## Tools

### `verify_invariants`
Formally proves mathematical safety of proposed tool invocations using an SMT-based linear invariant solver in $< 50\text{ ms}$.

* **Input Parameters:**
  * `action_id`: Unique identifier for the proposed action.
  * `tool_name`: Target tool name to invoke.
  * `parameters`: Proposed tool arguments.
  * `ambient_state`: Current system state snapshot.
  * `invariants`: List of safety invariant rules (e.g. `daily_spent + amount <= 500`).

---

## License
Apache-2.0
