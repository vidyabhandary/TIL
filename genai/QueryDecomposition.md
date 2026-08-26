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
