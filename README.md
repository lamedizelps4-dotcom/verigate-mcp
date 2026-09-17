# VeriGate SMT Safety Gatekeeper

[![Smithery Quality](https://smithery.ai/badge/lamedizelps4/verigate-mcp)](https://smithery.ai/server/lamedizelps4/verigate-mcp)
[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Runtime](https://img.shields.io/badge/Runtime-Rust%20MUSL-orange.svg)](https://verigate-a2a-861341012353.us-central1.run.app)
[![Latency](https://img.shields.io/badge/Solver%20Latency-%3C15ms-brightgreen.svg)](https://verigate-a2a-861341012353.us-central1.run.app)

**VeriGate** is an enterprise-grade Model Context Protocol (MCP) server providing **pre-flight SMT constraint verification** (Z3 QF-LIA / QF-BV) for autonomous AI agents.

It intercepts proposed AI agent tool calls before execution and formally proves whether executing the tool violates system safety invariants (e.g., daily spend ceilings, unauthorized state transitions, rate limits, or access controls) with sub-15ms solver latency and cryptographic Ed25519 proof-of-verification receipts.

---

## Tools

| Tool | Description | Read-Only | Destructive |
| :--- | :--- | :---: | :---: |
| `verigate.verify_invariants` | Formally proves mathematical invariant satisfiability on proposed tool calls | **Yes** | **No** |

### `verigate.verify_invariants`

Verifies mathematical safety invariants and SMT constraint satisfiability on proposed AI agent tool calls before execution. Call this tool whenever an agent proposes a high-impact action (e.g. database mutation, money transfer, system reconfiguration, or credential emission) to prove it cannot violate predefined safety boundaries.

#### Parameters

| Parameter | Type | Required | Description | Example |
| :--- | :--- | :---: | :--- | :--- |
| `action_id` | `string` | **Yes** | Unique tracking identifier for the proposed action | `"act-4829-payout"` |
| `tool_name` | `string` | **Yes** | Target tool name proposed for execution | `"treasury.transfer_funds"` |
| `parameters` | `object` | **Yes** | Key-value dictionary mapping tool argument names to proposed values | `{"amount_usdc": 50.0}` |
| `ambient_state` | `object` | **Yes** | Current system snapshot and environmental variables | `{"daily_disbursed_usd": 1200.0, "max_daily_budget_usd": 5000.0}` |
| `invariants` | `array` | **Yes** | List of safety invariant rules and SMT formulas | `[{"id": "budget", "formula": "ambient_state.daily_disbursed_usd + parameters.amount_usdc <= ambient_state.max_daily_budget_usd", "description": "Budget cap"}]` |

#### Output Schema

| Field | Type | Description |
| :--- | :--- | :--- |
| `status` | `string` | Verification outcome (`VALID` or `CONTRADICTION_PROVED`) |
| `solver_latency_ms` | `number` | SMT solver execution duration in milliseconds |
| `proof_receipt` | `string` | Signed Ed25519 cryptographic receipt authorizing execution |
| `checked_invariants_count` | `integer` | Count of evaluated invariant formulas |

#### Annotations
* **Read-Only**: `true`
* **Destructive**: `false`
* **Open-World**: `false`
* **Audience**: `["user", "assistant"]`
* **Priority**: `0.95`

---

## Installation

### Claude Desktop

Add to `claude_desktop_config.json`:

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

Install directly via Smithery:

```bash
npx -y @smithery/cli install lamedizelps4/verigate-mcp --client claude
```

---

## Usage

### Example Tool Invocation

Agents invoke `verigate.verify_invariants` before executing financial transactions:

```json
{
  "jsonrpc": "2.0",
  "id": 1,
  "method": "tools/call",
  "params": {
    "name": "verigate.verify_invariants",
    "arguments": {
      "action_id": "act-4829-payout",
      "tool_name": "treasury.transfer_funds",
      "parameters": {
        "recipient": "0x49B5B23933c0615951f28b495C3a58e0a3952f46",
        "amount_usdc": 50.0
      },
      "ambient_state": {
        "daily_disbursed_usd": 1200.0,
        "max_daily_budget_usd": 5000.0,
        "caller_role": "finance_agent"
      },
      "invariants": [
        {
          "id": "inv_daily_budget_limit",
          "formula": "ambient_state.daily_disbursed_usd + parameters.amount_usdc <= ambient_state.max_daily_budget_usd",
          "description": "Ensure proposed payout does not breach daily budget ceiling"
        }
      ]
    }
  }
}
```

### Response

```json
{
  "status": "VALID",
  "solver_latency_ms": 1.42,
  "proof_receipt": "vgate1:7a8b9c0d... (Ed25519 signature)",
  "checked_invariants_count": 1
}
```

---

## 🔒 Security & Compliance
* **NIST SP 800-88 & AI RMF Ready**
* **Cryptographic Proofs**: Outputs signed `vgate1:...` Ed25519 verification receipts.
* **Watsonx Governance Telemetry**: Emits W3C `traceparent` headers for audit trails.

## 📄 License
Apache-2.0
