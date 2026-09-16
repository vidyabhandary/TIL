## Context Compaction 
— Keep Long-Running Agents Within the Context Window

### Concept

The previous nugget covered **durable execution**: preserving workflow state after failures.

A different problem appears when the agent simply keeps running:

```text
User messages
+ model responses
+ reasoning
+ tool calls
+ tool results
+ retrieved documents
        ↓
   Context keeps growing
        ↓
   Context-window limit
```

Simply deleting old messages is dangerous because the agent may forget **decisions, constraints, tool outcomes, or unfinished work**.

**Context compaction** replaces a large conversation history with a smaller representation that preserves the state needed to continue.

```text
120K-token history
        ↓
     compact
        ↓
Compact state + recent high-value context
        ↓
Agent continues
```

This is different from ordinary summarization: the goal is not a human-readable summary—it is **continuity of model execution with fewer tokens**.

OpenAI now provides native compaction in the Responses API, including automatic server-side compaction and a standalone `responses.compact` endpoint. ([OpenAI][1])

### Practical case study

Consider an AI solutioning agent that spends 45 minutes:

**RFP → requirements → research → architecture → security analysis → costing → proposal**

After dozens of tool calls, the conversation may contain huge quantities of intermediate material.

You *do not* necessarily need the complete raw output from an earlier AWS pricing search.

You **do** need to retain:

> Selected architecture = active-active multi-region
> Residency constraint = EU only
> Database decision = Aurora PostgreSQL
> Open issue = customer IdP integration

Compaction keeps the important working state while removing context that no longer needs to consume the model's attention.

### When to use it

Use compaction for **long-running agents, coding agents, research workflows, multi-step tool loops, or persistent conversations**.

Don't introduce it for short independent calls where the entire relevant context comfortably fits in the context window.

Also, don't confuse compaction with **durable business state**. Important facts such as approvals, transaction IDs, architecture decisions, or customer requirements should still live in your application database—not solely inside compacted model context.

### Important current development

Modern agent runtimes are increasingly treating **context management as infrastructure**, rather than expecting developers to manually summarize history. OpenAI's current implementation can create an encrypted, token-efficient compaction item and continue the workflow using that plus selected high-value context; Codex uses this mechanism for long-running coding workflows. ([OpenAI][1])

### Architecture takeaway

Think of agent memory as **three different layers**:

**Database / workflow state** → facts that must never be lost
**Compacted context** → what the model needs to keep reasoning
**Recent context** → immediate working detail

> **Don't solve a growing-context problem by endlessly increasing the context window. Design what the agent must remember—and at what fidelity.**

[1]: https://openai.com/index/equip-responses-api-computer-environment/?utm_source=chatgpt.com "From model to agent: Equipping the Responses API with a computer environment | OpenAI"
[2]: https://openai.github.io/openai-agents-python/sessions/?utm_source=chatgpt.com "Overview - OpenAI Agents SDK"
