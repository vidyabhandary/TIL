## MCP 
Standardizing How AI Connects to Enterprise Systems

### Concept

The **Model Context Protocol (MCP)** is a standard interface between an AI application and external capabilities. Rather than building a custom integration between every LLM/agent and every internal system, you expose capabilities through an MCP server. The host application contains an MCP client that discovers and invokes them. ([MCP Python SDK][1])

MCP defines three useful primitives:

* **Tool** — model decides to call it; e.g. calculate pricing or create a ticket.
* **Resource** — application loads data into context; e.g. account details or documentation.
* **Prompt** — user selects a reusable workflow/template.

That distinction is important because it encodes **who controls the operation**, not merely how data is transported. ([MCP Python SDK][1])

A relevant recent development: the official Python SDK **v2 is now the stable release line** and implements the **July 28, 2026 MCP specification**; among other changes, the high-level `FastMCP` class is now `MCPServer`. ([MCP Python SDK][2])

---

### Practical case study

Suppose you build a **solutioning copilot** that needs:

> customer context + approved engineering rates + effort calculation

Without MCP, you might hard-wire CRM, pricing, and project systems directly into your agent framework.

With MCP:

```text
                    ┌── CRM MCP Server
Solutioning Agent ──┼── Pricing MCP Server
                    └── Knowledge MCP Server
```

Your pricing service could expose:

* `ratecard://backend-engineer` → **Resource**
* `calculate_estimate(...)` → **Tool**

The agent framework can change later without rewriting the underlying enterprise integration.

---

### When to use it

Use MCP when you expect **multiple AI clients or agents to reuse the same enterprise capabilities**, or when you want model/framework independence around APIs, databases, SaaS applications, and knowledge systems.

Don't introduce MCP merely to call one private function inside one small application. A normal typed service interface may be simpler.

---

### Architecture takeaway

Think of MCP as:

**OpenAPI-style standardization for the AI integration boundary — not the intelligence itself.**

Your architecture becomes:

**LLM/Agent → MCP client → governed MCP server → enterprise service**

But MCP does **not** replace authorization, business rules, audit controls, or API design. In fact, production MCP servers should be treated like any other privileged service boundary.

**Key mental model:**

> **Standardize capabilities once; allow multiple AI systems to consume them safely.**

[1]: https://py.sdk.modelcontextprotocol.io/get-started/first-steps/ "First steps - MCP Python SDK"
[2]: https://py.sdk.modelcontextprotocol.io/whats-new/ "What's new in v2 - MCP Python SDK"
[3]: https://py.sdk.modelcontextprotocol.io/run/authorization/ "Authorization - MCP Python SDK"
