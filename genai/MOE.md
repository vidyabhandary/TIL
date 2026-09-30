## Mixture of Experts (MoE)
 — A Large Model That Activates Only Part of Itself

### Concept

A normal **dense transformer** uses essentially all of its feed-forward parameters for every token.

A **Mixture-of-Experts (MoE)** model replaces some of those dense layers with many specialized neural-network “experts.” A small **router** decides which experts should process each token.

```text
Dense model

Token → [all parameters] → output


MoE model

                    ┌→ Expert 3 ─┐
Token → Router ─────┤            ├→ output
                    └→ Expert 17 ┘

       Other experts remain inactive
```

This creates an important distinction:

> **Total parameters ≠ active parameters per token.**

For example, the current Qwen3.5-35B-A3B checkpoint has roughly **35B total parameters but only about 3B active parameters** for each token. Its MoE layers contain 256 experts, with eight routed experts plus one shared expert active per token. 

---

### Practical case study

Suppose you're choosing a self-hosted model for an enterprise support assistant.

You want more model capacity than a 3B dense model, but don't want the compute cost of executing a 35B dense model for every token.

An MoE model can give you:

```text
Large total capacity
        ↓
many experts can learn different patterns
        ↓
Router selects only a few per token
        ↓
Much smaller active computation
```

So a token involving Python may route differently from one involving financial terminology.

A subtle but important point: **you should not think of a 35B-total/3B-active MoE as simply “a 3B model.”** The full expert weights still need to be stored somewhere, and distributing experts across GPUs introduces communication and routing costs.


Modern Transformers runtimes also support **expert parallelism**, where different experts can reside on different accelerators and tokens are routed to the appropriate device. 

### When to use it

MoE is attractive when you want **high model capacity without paying dense-model compute for every token**, particularly for large general-purpose or multilingual models.

### When not to use it

Don't automatically choose MoE because the **active parameter number looks small**.

It can be a poor fit when:

- GPU memory is the main constraint—the complete expert weights still matter.
- communication between GPUs is slow.
- your deployment is small enough that a dense model is simpler and sufficiently capable.
- operational simplicity matters more than maximum model capacity.

---

### Important development

MoE is becoming increasingly mainstream rather than an exotic research architecture. Current Transformer runtimes now expose dedicated **expert-parallel execution and optimized grouped expert kernels**, reflecting how frequently modern large models use sparse experts.

### Architecture takeaway

When comparing an MoE model, track **three numbers**, not one:

```text
Total parameters   → storage / memory requirement

Active parameters  → approximate compute per token

Experts activated  → routing + distributed-system overhead
```

> **A sparse 100B model can have the compute profile of a much smaller model—but it does not have the deployment footprint of that smaller model.**

**Ledger update:** #36 — Mixture of Experts / sparse expert routing.