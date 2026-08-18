## Agent Memory 
— Conversation History ≠ Long-Term Memory

### Concept

Giving an agent “memory” does **not** mean continually stuffing every previous conversation into its prompt.

There are two distinct concerns:

**Short-term memory** keeps the state of the current conversation or workflow. **Long-term memory** stores selected facts that should survive across conversations—for example user preferences, customer configuration, or lessons learned from previous interactions. LangGraph models these separately: checkpointers persist thread-scoped state, while stores persist application-defined information across threads. ([Docs by LangChain][1])

A useful mental model is:

```text
Current conversation → Working memory
                           ↓
                 Memory extraction policy
                           ↓
                  Long-term memory store
                           ↓
              Relevant memories recalled
```

The important word is **selected**. A production agent should generally not remember everything.

### Practical case study

Consider an architecture copilot used repeatedly for customer opportunities.

During one engagement it learns:

> “Customer requires Azure hosting and does not permit customer data to leave the EU.”

Six weeks later, a new conversation starts about the same customer. That information is useful even though the original chat history is irrelevant.

Instead of replaying six weeks of messages, persist a small customer profile:

```text
hosting_preference = Azure
data_residency = EU
source = security workshop
```

The new conversation can retrieve those facts and apply them to architecture decisions. Long-term memory stores are explicitly intended for cross-session information such as preferences and facts, and current LangGraph stores organize those memories using namespaces and keys. ([Docs by LangChain][2])

LangGraph recommends database-backed stores such as `PostgresStore` for production rather than in-memory storage. Database setup/migrations should normally be performed during deployment, not on every application request. ([Docs by LangChain][3])

### When to use it — and when not to

Use long-term memory for **stable preferences, verified customer facts, learned workflow conventions, previous decisions, and information that genuinely improves future interactions**.

Don't use it as an unlimited transcript archive. Avoid automatically storing secrets, transient comments, speculative model conclusions, or sensitive personal information just because the model encountered it.

### Architecture takeaway

The key design question is not:

> **“How do I give my agent memory?”**

It is:

> **“What deserves to become memory, who can read it, and when should it expire or be corrected?”**

A robust design separates:

**conversation state → memory extraction → validation → persistent store → selective retrieval**

LangChain's memory model also distinguishes semantic memories—facts—from episodic memories—past experiences—and procedural memories—rules or instructions, which is a useful way to reason about what your agent should retain. ([Docs by LangChain][4])

**Key principle:** *Store durable knowledge, not durable noise.*

[1]: https://docs.langchain.com/oss/python/langgraph/persistence "Persistence - Docs by LangChain"
[2]: https://docs.langchain.com/oss/python/langchain/long-term-memory "Long-term memory - Docs by LangChain"
[3]: https://docs.langchain.com/oss/python/langgraph/add-memory "Memory - Docs by LangChain"
[4]: https://docs.langchain.com/oss/python/concepts/memory?utm_source=chatgpt.com "Memory overview - Docs by LangChain"
