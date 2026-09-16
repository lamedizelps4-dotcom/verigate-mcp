# VeriGate MCP Server

[![Smithery Quality](https://smithery.ai/badge/lamedizelps4/verigate)](https://smithery.ai/server/lamedizelps4/verigate)
[![License](https://img.shields.io/badge/License-Apache_2.0-blue.svg)](https://opensource.org/licenses/Apache-2.0)
[![Runtime](https://img.shields.io/badge/Runtime-Rust%20MUSL-orange.svg)](https://verigate-a2a-861341012353.us-central1.run.app)
[![Latency](https://img.shields.io/badge/Solver%20Latency-%3C15ms-brightgreen.svg)](https://verigate-a2a-861341012353.us-central1.run.app)

**VeriGate** is an enterprise-grade Model Context Protocol (MCP) server providing **pre-flight SMT constraint verification** (Z3 QF-LIA / QF-BV) for autonomous AI agents. 

It intercepts proposed AI agent tool calls before execution and formally proves whether executing the tool violates system safety invariants (e.g., daily spend ceilings, unauthorized state transitions, rate limits, or access controls) with sub-15ms solver latency and cryptographic Ed25519 proof-of-verification receipts.

---

## 🚀 Quickstart

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
npx -y @smithery/cli install lamedizelps4/verigate --client claude
```

---

## 🛠️ Tools

### `verigate.verify_invariants`
Verifies mathematical safety invariants and SMT constraint satisfiability on proposed AI agent tool calls before execution. Call this tool whenever an agent proposes a high-impact action (e.g. database mutation, money transfer, system reconfiguration, or credential emission) to prove it cannot violate predefined safety boundaries.

#### Parameters
| Parameter | Type | Required | Description | Example |
| :--- | :--- | :---: | :--- | :--- |
| `action_id` | `string` | **Yes** | Unique identifier for tracing verification provenance | `"act-4829-payout"` |
| `tool_name` | `string` | **Yes** | Target tool name proposed for execution | `"treasury.transfer_funds"` |
| `parameters` | `object` | **Yes** | Key-value map of proposed execution arguments | `{"recipient": "0x49B5...", "amount_usdc": 50.0}` |
| `ambient_state` | `object` | **Yes** | Current system snapshot and environmental variables | `{"daily_disbursed_usd": 1200.0, "max_daily_budget_usd": 5000.0}` |
| `invariants` | `array` | **Yes** | List of safety invariant rules and SMT formulas | `[{"id": "budget", "formula": "daily_disbursed + amount <= max_budget"}]` |

#### Annotations
* **Read-Only**: `true` (Safe, idempotent verification query)
* **Destructive**: `false`
* **Open-World**: `false`

---

## 💡 Prompts

* **`verigate.preflight_safety_audit`**: Prepares a formal pre-flight safety audit for critical tool executions.

---

## 📦 Resources

* **`verigate://specs/invariants-standard`**: Curated library of SMT linear invariant templates for financial transfers, rate limits, and access controls.

---

## 🔒 Security & Compliance
* **NIST SP 800-88 & AI RMF Ready**
* **Cryptographic Proofs**: Outputs signed `vgate1:...` Ed25519 verification receipts.
* **Watsonx Governance Telemetry**: Emits W3C `traceparent` headers for audit trails.

## 📄 License
Apache-2.0
