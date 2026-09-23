## Continuous Batching 
— Keep the GPU Busy While Users Generate at Different Speeds

### Concept

This is **not the same as Batch Inference**, which we covered earlier.

Traditional batching groups requests and waits for the **slowest request**:

```text
A: █████████████
B: ███
C: ██████
   └───────────── wait for A ──→ next batch
```

**Continuous batching** rebuilds the active batch during generation. The moment B finishes, another waiting request can occupy its GPU slot:

```text
A: █████████████
B: ███ D████████
C: ██████ E█████
```

Because LLM generation happens token-by-token, this can substantially improve **GPU utilization, throughput, and average latency**. Current Hugging Face Transformers implements this directly: completed requests leave the batch and waiting requests can enter at every generation step. ([Hugging Face][1])

### Case study

Imagine an internal AI assistant serving 200 employees.

One user asks:

> “Summarize this contract in detail.” → 1,000 output tokens

Another asks:

> “What is the termination date?” → 15 tokens

With static batching, the short request's GPU slot may sit idle while the long response continues.

With continuous batching:

**short request finishes → slot immediately reused → next request starts**

That means you can often serve more users from the **same GPU fleet**, rather than scaling hardware simply because request lengths vary.

### When to use it

Use continuous batching for **interactive self-hosted LLM services** with many concurrent users and widely varying prompt/output lengths.

Avoid optimizing for it when traffic is very low, workloads are purely offline, or you're using a managed model API where the provider already owns inference scheduling.

### Important development

Continuous batching is increasingly moving from specialized inference servers into mainstream model runtimes. Hugging Face now exposes it directly in Transformers and recommends `transformers serve` for production serving. Meanwhile, its older TGI server entered **maintenance mode on December 11, 2025**, with Hugging Face recommending engines such as vLLM or SGLang for new deployments. ([Hugging Face][1])

### Architecture takeaway

For self-hosted LLMs, **model choice is only half the performance problem**.

You must also design the **request scheduler**.

> **Static batching optimizes the batch. Continuous batching optimizes the GPU over time.**

**Ledger update:** #31 — Continuous Batching / dynamic inference scheduling.

[1]: https://huggingface.co/docs/transformers/en/continuous_batching "Continuous batching · Hugging Face"
