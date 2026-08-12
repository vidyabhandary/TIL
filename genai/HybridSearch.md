# Hybrid Search
(Keywords + Meaning)

### Concept

Vector search is excellent at finding **semantic similarity**, but it can struggle with exact identifiers, product names, acronyms, error codes, and uncommon terminology. Traditional lexical search such as **BM25** is often strong at exactly those things.

**Hybrid search** runs both and combines their rankings:

**BM25 retrieval + vector retrieval → rank fusion → best candidates → LLM**

A common fusion technique is **Reciprocal Rank Fusion (RRF)**. Instead of trying to normalize incompatible BM25 and vector similarity scores, RRF combines each document's *rank position* across the result lists. Elasticsearch's current RRF retriever is explicitly designed to combine multiple retrievers this way. ([Elastic][1])

---

### Practical case study

Imagine an engineering knowledge assistant receiving:

> “Why does PAY-403 occur when contractors submit payroll?”

Semantic search may retrieve documentation about **contractor payroll authorization failures**.

BM25 may retrieve the document containing the exact code **PAY-403**.

Hybrid search gives you both signals, making it particularly useful for enterprise knowledge bases containing:

* error codes,
* APIs,
* product terminology,
* policy language,
* natural-language questions.

### When to use it

Use hybrid retrieval when your corpus contains **both natural-language concepts and exact terminology**—technical documentation, contracts, support knowledge, product catalogs, or enterprise search.

Avoid the extra complexity when your corpus is tiny, exact keyword search already works extremely well, or latency requirements make running multiple retrieval strategies unjustified.

---

### Architecture takeaway

Don't automatically assume:

**“Embeddings replaced search.”**

A stronger production pattern is often:

**lexical retrieval + semantic retrieval → fusion → reranking → LLM**

One useful recent capability: Elasticsearch Stack **9.2+** supports weights on RRF child retrievers, allowing you to deliberately favor lexical or semantic retrieval when domain evaluation shows one signal is more valuable. ([Elastic][3])

The key lesson: **retrieval strategies are complementary, not mutually exclusive.**

[1]: https://www.elastic.co/docs/reference/elasticsearch/rest-apis/reciprocal-rank-fusion?utm_source=chatgpt.com "Reciprocal rank fusion | Elasticsearch Reference"
[2]: https://www.elastic.co/docs/reference/elasticsearch/clients/python/async?utm_source=chatgpt.com "Using with asyncio | Python"
[3]: https://www.elastic.co/docs/reference/elasticsearch/rest-apis/retrievers/rrf-retriever?utm_source=chatgpt.com "RRF retriever | Elasticsearch Reference"
