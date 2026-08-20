## Batch Inference 
— Separate “Needs AI” from "“"Needs AI Now"

### Concept

A common GenAI architecture mistake is sending **every workload through a synchronous API**.

Many AI jobs are not interactive:

* nightly document classification,
* thousands of requirement extractions,
* offline evaluations,
* embedding generation,
* bulk summarization,
* data-enrichment jobs.

For these, **batch inference** trades immediate response time for higher throughput and lower cost.

OpenAI’s current Batch API accepts asynchronous request files for endpoints including `/v1/responses` and processes them within a **24-hour completion window**. Batch processing currently receives a **50% discount on model input and output pricing**. ([OpenAI Platform][1])

---

### Practical case study

Suppose a proposal team imports **8,000 historical SOWs** and wants AI to extract:

> customer, industry, technologies, scope, effort, risks, and commercial assumptions.

A synchronous design might create:

```text
worker → API → wait → result
worker → API → wait → result
...
```

But nobody needs each extraction in three seconds.

A better design is:

```text
Documents
    ↓
Prepare validated requests
    ↓
Batch API
    ↓
Asynchronous processing
    ↓
Results matched by custom_id
    ↓
Database / analytics
```

That isolates bulk workloads from latency-sensitive user traffic and can materially reduce inference cost. Each batch request has a developer-defined `custom_id` specifically for matching outputs back to inputs. ([OpenAI Platform][1])

The current API requires batch input as **JSONL**, uploaded with purpose `batch`. A batch can contain up to **50,000 requests** and a file up to **200 MB**. ([GitHub][2])

In production, also persist:

```text
batch_id
custom_id
job status
retry count
model version
prompt version
```

so failed or expired items can be safely replayed.

---

### When to use it

Use batch inference for **large offline workloads, evaluations, migrations, analytics enrichment, nightly processing, and bulk document extraction** where minutes or hours of latency are acceptable.

Do **not** use it for chat assistants, interactive agents, approval flows, or user-facing operations that require an immediate response.

---

### Architecture takeaway

Classify GenAI workloads by **latency requirement**, not merely by model capability:

**Interactive**
→ synchronous / streaming inference

**Near-real-time**
→ queues + workers

**Offline**
→ batch inference

The broader principle is:

> **Do not pay real-time architecture costs for workloads that are inherently asynchronous.**

That distinction becomes increasingly important as GenAI moves from individual prompts to **enterprise-scale processing pipelines**.

[1]: https://platform.openai.com/docs/api-reference/batch/object?api-mode=responses&utm_source=chatgpt.com "Batch | OpenAI API Reference"
[2]: https://github.com/openai/openai-python/blob/main/src/openai/resources/files.py?utm_source=chatgpt.com "openai-python/src/openai/resources/files.py at main · openai/openai-python · GitHub"
