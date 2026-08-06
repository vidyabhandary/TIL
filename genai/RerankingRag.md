## Reranking in RAG

### Concept

A retriever usually returns the document chunks that are mathematically closest to a user’s query. But “closest” does not always mean “best answer.”

**Reranking** adds a second step:

1. Retrieve a broad set of candidate chunks.
2. Score those candidates more carefully against the full question.
3. Send only the best few to the LLM.

This improves precision without searching the entire knowledge base with an expensive model.

---

### Practical case study

A presales assistant searches previous proposals for:

> “What SLA assumptions should we use for fractional LMS support?”

Initial vector retrieval may return chunks about:

* Full-time support SLAs
* LMS administration
* Fractional staffing
* Incident severity definitions

All are semantically related, but only some directly address **fractional coverage and SLA measurement**.

A reranker can promote chunks that mention support windows, paused SLA clocks, severity targets, and dependency delays, while pushing generic LMS content down the list.

---

### When to use it

Use reranking when:

* Initial retrieval returns many plausible but weak matches.
* Citation accuracy matters.
* Your documents contain overlapping terminology.
* You want better quality without increasing the final prompt size.

### When not to use it

Skip or minimize reranking when:

* The knowledge base is very small.
* Retrieval quality is already consistently high.
* Latency is extremely sensitive.
* The use case can tolerate broader contextual answers.

---

### Architecture takeaway

A practical RAG pipeline often uses:

**fast retrieval → accurate reranking → limited context → generation**

Do not compensate for poor ranking by sending 20–30 chunks to the LLM. That increases cost, latency, and the chance that irrelevant context influences the answer.
