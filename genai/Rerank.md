## Reranking 
— Retrieve Broadly, Then Rank Precisely

### Concept

Vector search is optimized to **find plausible candidates quickly**, not necessarily to put the best evidence first.

A stronger RAG pattern is **two-stage retrieval**:

```text
Query
  ↓
Vector / hybrid search
  ↓
Top 30–50 candidates
  ↓
Reranker
  ↓
Best 3–5 chunks
  ↓
LLM
```

Embeddings represent the **query and documents separately** and compare their vectors. A reranker instead evaluates each candidate **against the actual query**, allowing a more precise relevance judgment.

So think:

**Retriever = high recall** → don't miss useful evidence.
**Reranker = high precision** → put the best evidence at the top.

Cohere's current Rerank 4.0 models provide `pro` and lower-latency `fast` variants, support multilingual and semi-structured content, and increased per-query/document context to 32K tokens. ([Cohere Documentation][1])

### Practical case study

An architecture RAG system receives:

> “What prevents active-active deployment of this application?”

Vector search might return:

1. Disaster-recovery overview
2. Application architecture
3. Database replication limitations
4. Availability-zone requirements
5. Backup policy

All are semantically related.

But the crucial evidence may be:

> “The legacy database permits only one writable regional instance.”

A reranker can promote that chunk from **#3 to #1**, meaning the LLM sees the decisive constraint first.

This often improves RAG without changing the embedding model or rebuilding your vector store.

### When to use it

Use reranking when your retriever frequently finds the **right document but ranks it poorly**, or when you're using hybrid search and need a common ranking stage.

Don't use it to compensate for bad ingestion, missing documents, poor access controls, or retrieval that never finds the relevant evidence. A reranker can only reorder what it receives.

Also avoid sending thousands of candidates—the first retrieval stage exists precisely to narrow the search space.

### Architecture takeaway

Don't optimize RAG solely for:

> **“Did retrieval find the correct chunk?”**

Also measure:

> **“How highly was the correct chunk ranked?”**

That distinction becomes especially important because managed RAG platforms are increasingly making reranking a first-class capability. Amazon Bedrock Managed Knowledge Base, generally available since **June 17, 2026**, includes document ranking and agentic retrieval; its managed retrieval currently enables service-managed reranking by default when managed embeddings are used. ([Amazon Web Services, Inc.][3])

**Key principle:** **retrieve broadly enough not to miss the answer; rerank narrowly enough that the LLM sees the best evidence first.**

[1]: https://docs.cohere.com/changelog/rerank-v4.0?utm_source=chatgpt.com "Cohere's Rerank v4.0 Model is Here! | Cohere"
[2]: https://docs.cohere.com/docs/reranking-quickstart?utm_source=chatgpt.com "Reranking - quickstart | Cohere"
[3]: https://aws.amazon.com/about-aws/whats-new/2026/06/amazon-bedrock-managed-knowledge-base/?utm_source=chatgpt.com "Amazon Bedrock Managed Knowledge Base is now generally available - AWS"
