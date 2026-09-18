## Query Decomposition 
— Break Complex Questions Before Retrieval

### Concept

A single user question can contain **multiple information needs**.

For example:

> “Compare Azure and AWS for this EU-hosted workload, identify security risks, and estimate the main cost drivers.”

One embedding search against that entire sentence may retrieve documents that partially match everything but answer nothing particularly well.

**Query decomposition** asks an LLM to split a complex question into a small set of focused sub-queries:

**complex question → structured sub-queries → parallel retrieval → combine evidence → final answer**

This improves retrieval recall because each sub-question can independently search for the evidence it needs.

---

### Practical case study

Suppose a solutioning copilot receives:

> “Can this application run on Azure in Germany, integrate with SAP, and meet a 2-second response SLA?”

A useful decomposition might be:

```text
1. Azure regions and data-residency options in Germany
2. Supported SAP integration patterns
3. Architecture requirements for a 2-second response SLA
```

Each query retrieves different evidence.

The final LLM gets **three focused evidence sets** rather than a noisy pile of chunks matching the entire original sentence.

### When to use it

Use query decomposition when questions involve:

* comparisons,
* multiple constraints,
* troubleshooting across several systems,
* research questions,
* architecture or proposal analysis.

Avoid it for simple questions such as:

> “What is our password-reset policy?”

Decomposition there only adds latency and cost.

---

### Architecture takeaway

Query decomposition usually works best as:

**classify complexity → decompose if needed → parallel retrieval → rerank → synthesize**

The important control is **“if needed.”**

Automatically decomposing every query can create unnecessary searches, duplicate evidence, and higher inference cost.

**Key principle:**

> **Complex questions often retrieve better when treated as several precise questions rather than one large semantic query.**

[1]: https://github.com/openai/openai-python/blob/main/examples/responses/structured_outputs.py?utm_source=chatgpt.com "openai-python/examples/responses/structured_outputs.py at main · openai/openai-python · GitHub"

## Generative AI Nugget: **Query Decomposition — One Complex Question, Several Searches**

### Concept

A single retrieval query works well for:

> “What is our password rotation policy?”

But many real questions contain **multiple information needs**:

> “Can this system run active-active across EU regions, what database limitations apply, and what would that mean for RTO?”

Sending that whole sentence as one vector query can blur the intent.

**Query decomposition** uses an LLM to split the question into focused searches:

```text
Complex question
      ↓
Query planner
      ↓
 ┌──────────────┬──────────────┬──────────────┐
 EU deployment   DB limitations  RTO requirements
      ↓                ↓                ↓
            parallel retrieval
                    ↓
             merge + rerank
                    ↓
                   LLM
```

Microsoft's current Azure AI Search **agentic retrieval** uses this same pattern: an LLM can create focused subqueries, execute them in parallel, semantically rerank each result set, and merge the evidence. ([Microsoft Learn][1])

### Practical case study

Suppose a solution architect asks:

> “Can we deploy the application active-active in Germany and France while meeting the customer's 15-minute RPO?”

A single search might over-focus on *active-active*.

A planner can instead generate:

1. `supported multi-region deployment topology`
2. `database cross-region replication limitations`
3. `customer RPO requirement 15 minutes`
4. `Germany France data residency constraints`

The retriever now has several chances to find **different pieces of evidence required for the final answer**.

Decomposition happens **before retrieval**. This is different from reranking, which improves the ordering of documents that retrieval has already found.

### When to use it

Use decomposition for **multi-part questions, comparisons, investigative research, architecture analysis, root-cause analysis, and questions requiring evidence from several documents**.

Don't use it automatically for simple factual queries. Turning:

> “What is the support email?”

into four searches only adds LLM cost and latency.

### Architecture takeaway

A useful retrieval stack is becoming:

**question → decide complexity → decompose if needed → retrieve in parallel → rerank → generate**

The important architectural decision is therefore no longer simply **“Which vector database?”** It is increasingly:

> **How much reasoning should happen before search begins?**

This pattern is becoming a first-class platform capability: Azure AI Search now exposes agentic retrieval around query planning and parallel retrieval, while Microsoft also exposes retrieval reasoning controls that trade additional search reasoning against latency and cost. ([Microsoft Learn][2])

**Key principle:** **Complex questions often fail not because the knowledge is missing, but because you searched for all of it as though it were one thing.**

[1]: https://learn.microsoft.com/en-us/azure/search/agentic-retrieval-overview?utm_source=chatgpt.com "Agentic Retrieval Overview - Azure AI Search | Microsoft Learn"
[2]: https://learn.microsoft.com/en-us/azure/search/agentic-retrieval-how-to-create-pipeline?utm_source=chatgpt.com "Tutorial: Build an Agentic Retrieval Solution - Azure AI Search | Microsoft Learn"
