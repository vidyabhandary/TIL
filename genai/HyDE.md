## HyDE
 — Retrieve with a Hypothetical Answer

### Concept

Sometimes a user asks a question in very different language from the documents that contain the answer.

Example:

> “What happens if a supplier misses the handover date?”

But your contracts may say:

> “Delay in completion of transition obligations constitutes a supplier dependency breach.”

**HyDE — Hypothetical Document Embeddings — bridges this language gap.**

Instead of embedding the question directly:

**question → LLM generates an imagined ideal answer → embed that answer → retrieve similar real documents**

The generated answer is **not treated as fact**. It is only used to create a better search vector. The original HyDE research showed that this approach can substantially improve zero-shot dense retrieval when no relevance-labelled training data exists. ([arXiv][1])

---

### Practical case study

Consider a proposal knowledge base containing hundreds of SOWs.

A user asks:

> “What customer dependencies normally delay an SAP integration?”

The documents may never contain that exact wording. They may instead discuss:

> sandbox credentials, API whitelisting, SAP Basis availability, test tenants, firewall rules, and interface specifications.

HyDE can generate a hypothetical passage resembling the sort of text likely to appear in a real SOW:

> “Common customer dependencies include timely sandbox access, interface specifications, network whitelisting…”

Embedding **that passage** may land much closer to the relevant SOW sections than embedding the original question.

### When to use it

HyDE is useful when there is a large **vocabulary or style gap** between user questions and source documents: contracts, technical manuals, research papers, legal text, or highly specialized enterprise terminology.

Avoid it when ordinary hybrid retrieval already performs well, latency is critical, or queries contain exact identifiers such as invoice numbers or error codes. HyDE adds an extra LLM call before retrieval.

---

### Architecture takeaway

The critical design rule is:

> **The hypothetical document is a retrieval aid, never evidence.**

Your final answer should cite only the **real documents retrieved from the knowledge base**.

A very recent development is interesting here: a paper published **July 31, 2026** proposed **HyPE — Hypothetical Prompt Embeddings**. Instead of generating hypothetical answers at query time, HyPE generates likely questions during **indexing**, reducing runtime latency while trying to solve the same query–document language mismatch. The authors report sizeable retrieval gains across six datasets. ([arXiv][3])

So the evolving design choice is becoming:

**HyDE → smarter query at runtime**
**HyPE → smarter index at ingestion**

**Key principle:** Improve the *representation used for retrieval* without confusing generated retrieval signals with authoritative knowledge.

[1]: https://arxiv.org/abs/2212.10496?utm_source=chatgpt.com "Precise Zero-Shot Dense Retrieval without Relevance Labels"
[2]: https://api.qdrant.tech/api-reference/search/query-points/?utm_source=chatgpt.com "Query points | Qdrant | API Reference"
[3]: https://arxiv.org/abs/2607.29402?utm_source=chatgpt.com "Bridging the Question-Answer Gap in Retrieval-Augmented Generation: Hypothetical Prompt Embeddings"
