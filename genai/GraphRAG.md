## GraphRAG 
— Retrieve Relationships, Not Just Similar Text.

### Concept

Traditional RAG asks:

> **“Which chunks are semantically similar to this question?”**

That works well for direct factual questions, but poorly when the answer depends on **relationships across multiple documents**.

**GraphRAG** adds a knowledge graph:

**documents → entities + relationships → graph → graph-aware retrieval → LLM**

So instead of retrieving only a chunk mentioning *SAP Integration Suite*, you can retrieve connected facts such as:

**Customer → uses → SAP S/4HANA → integrates via → Integration Suite → depends on → Network Whitelisting**

This is especially useful for **multi-hop questions** and corpus-level questions such as “What are the major dependency patterns across our implementations?” Microsoft’s GraphRAG work specifically targets cases where ordinary top-k vector retrieval struggles with global sensemaking across large corpora. ([arXiv][1])

### Practical case study

Suppose you have 500 historical proposals.

You ask:

> “Which customer dependencies have repeatedly caused SAP integration delays?”

Vector RAG may retrieve proposals containing the words *SAP*, *delay*, and *dependency*.

GraphRAG can follow relationships:

```text
SAP Project
   ├─ DEPENDS_ON → Firewall Whitelisting
   ├─ DEPENDS_ON → Sandbox Access
   └─ DEPENDS_ON → SAP Basis Team

Firewall Whitelisting
   └─ CAUSED_DELAY_IN → Project X, Project Y, Project Z
```

Now the answer can identify **patterns across projects**, not merely summarize similar paragraphs.

### When to use it

Use GraphRAG when answers depend on **relationships, dependency chains, organizational structures, ownership, lineage, fraud networks, supply chains, architecture dependencies, or recurring patterns across many documents**.

Do **not** automatically replace normal RAG with GraphRAG. Building and maintaining the graph adds ingestion cost, entity-resolution complexity, and another failure mode: incorrect extracted relationships.

### Architecture takeaway

A useful decision rule is:

**“Find passages about X” → vector/hybrid RAG**

**“How is X related to Y through Z?” → GraphRAG**

**“What patterns exist across the entire corpus?” → GraphRAG global/community retrieval**

In production, the strongest architecture is often **not GraphRAG instead of vector RAG**, but:

**vector retrieval → identify relevant entities → graph traversal → reranking → LLM**

Microsoft GraphRAG itself distinguishes **local search, global search, DRIFT search, and basic vector-style search**, because no single retrieval strategy is optimal for every question. ([Microsoft GitHub][4])

**Key principle:**

> **Use vectors to find what is similar; use graphs to discover how things are connected.**

[1]: https://arxiv.org/abs/2404.16130?utm_source=chatgpt.com "From Local to Global: A Graph RAG Approach to Query-Focused Summarization"
[2]: https://neo4j.com/docs/neo4j-graphrag-python/current/user_guide_rag.html "User Guide: RAG — neo4j-graphrag-python documentation"
[3]: https://neo4j.com/docs/neo4j-graphrag-python/current/?utm_source=chatgpt.com "GraphRAG for Python — neo4j-graphrag-python documentation"
[4]: https://microsoft.github.io/graphrag/?utm_source=chatgpt.com "Welcome - GraphRAG"
