## LLM-as-a-Judge 
— Evaluate AI Outputs with Another Model

### Concept

Once a GenAI system reaches production, the difficult question becomes:

> **“Did the new prompt/model/retrieval strategy actually make answers better?”**

For subjective tasks—summaries, architecture recommendations, grounded answers—exact-match metrics are weak.

**LLM-as-a-Judge** uses another model to grade candidate outputs against a defined rubric.

A particularly useful form is **pairwise evaluation**:

**same input → Response A + Response B → judge chooses better response**

This is often easier for a model than assigning arbitrary scores such as 7.6/10.

OpenAI's current Evals platform supports model-based graders alongside deterministic grading mechanisms, making this pattern increasingly part of normal GenAI engineering rather than ad-hoc testing. ([OpenAI Developers][1])

### Practical case study

Suppose you're changing an RFP assistant from:

**RAG v1:** top-5 vector retrieval

to:

**RAG v2:** hybrid retrieval + reranking.

You run 300 real historical questions through both systems.

For each question, a judge receives:

* original question,
* authoritative reference/context,
* answer from v1,
* answer from v2,
* rubric: **correctness, grounding, completeness, conciseness**.

Then calculate:

> **v2 wins 68%, v1 wins 19%, ties 13%**

Now you have evidence for deploying the architecture change.

---

### When to use it

Use LLM judges for **RAG answer quality, summarization, extraction quality, agent trajectories, proposal generation, code explanations, or comparing prompts/models** where human judgment is meaningful but expensive.

Don't rely on an LLM judge for things that can be checked deterministically: JSON validity, exact calculations, API success, schema compliance, latency, or whether a citation URL exists.

---

### Architecture takeaway

Treat evaluation as another system requiring validation:

**Application → candidate output → evaluator → metrics → release decision**

And use the hierarchy:

**deterministic checks first → LLM judge where judgment is needed → human review for high-stakes cases**

A notable 2026 study found that even identical pairwise evaluations could flip across repeated runs, reinforcing that a **single LLM judgment should not automatically be treated as ground truth**. ([arXiv][3])

**Key principle:**

> **Use LLMs to scale human-like judgment—but evaluate the judge before trusting the score.**

[1]: https://developers.openai.com/api/reference/java/resources/evals/methods/create?utm_source=chatgpt.com "Create eval | OpenAI API Reference"
[2]: https://arxiv.org/abs/2406.07791?utm_source=chatgpt.com "Judging the Judges: A Systematic Study of Position Bias in LLM-as-a-Judge"
[3]: https://arxiv.org/abs/2606.13685?utm_source=chatgpt.com "The Coin Flip Judge? Reliability and Bias in LLM-as-a-Judge Evaluation"
