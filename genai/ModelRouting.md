## Model Routing
 — Don’t Use Your Best Model for Everything

### Concept

A production GenAI system does not necessarily need one model. **Model routing** selects a model based on the difficulty, risk, latency target, and business value of each request.

For example:

**simple extraction → fast/cheap model**
**architecture reasoning → stronger model**
**high-impact decision → strongest model + human review**

This matters because current model families can differ substantially in cost and capability, so routing can reduce spend without forcing low-capability models onto difficult problems. ([OpenAI Platform][1])

---

### Practical case study

Imagine an AI assistant supporting proposal development.

It receives three requests:

> “Extract the customer name from this paragraph.”

> “Compare Azure and AWS architectures for this workload.”

> “Recommend whether we should commit contractually to a 99.99% SLA.”

Using the strongest available model for all three wastes capacity.

A better policy might be:

```text
Extraction        → FAST model
Architecture      → BALANCED model
Contract/SLA risk → STRONG model + review
```

The important point is that **routing should be based on observable business signals**, not simply on the model saying, “I am 87% confident.”

Model names live in configuration, not application code. That lets you change the model ladder after evaluation without redeploying routing logic.

---

### When to use it

Use routing when you have **high request volume, several task types, meaningful model-cost differences, or clear separation between low-risk and high-risk work**.

Avoid sophisticated routing when traffic is small, virtually every task needs the same capability level, or the routing errors would cost more than simply using a stronger model.

---

### Architecture takeaway

Treat model selection like any other architecture optimization:

**measure → classify workload → route → evaluate → adjust**

Do not optimize purely for cost. Maintain an evaluation set for each route and verify that the cheaper model still meets the required quality threshold. OpenAI also recommends pinning model versions where consistency matters and using evaluations to detect behavioral changes. ([OpenAI Platform][2])

**Key principle:**

> **Use the least expensive model that reliably satisfies the quality and risk requirements of the task.**

[1]: https://platform.openai.com/pricing?model=contentfilter-alpha-001&utm_source=chatgpt.com "Pricing | OpenAI API"
[2]: https://platform.openai.com/docs/api-reference/debugging-requests?lang=curl&utm_source=chatgpt.com "API Reference - OpenAI API"
