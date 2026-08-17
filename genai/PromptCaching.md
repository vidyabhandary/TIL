## Prompt Caching
 — Make Long Context Cheaper Without Losing It

### Concept

Many production GenAI applications repeatedly send the same large context: system instructions, policies, few-shot examples, tool definitions, schemas, or reference material. **Prompt caching** lets the model reuse previously processed prompt prefixes instead of recomputing them on every request. ([OpenAI Developers][1])

The architectural trick is simple:

**stable context first → cache breakpoint → changing user context last**

With GPT-5.6, explicit cache breakpoints let you deliberately mark the reusable prefix. The prefix through the breakpoint must contain at least **1,024 tokens**, and a consistent `prompt_cache_key` improves matching. ([OpenAI Developers][1])

---

### Practical case study

Suppose an enterprise support copilot includes:

* 4,000-token operating policy
* 2,000-token product terminology
* tool definitions
* output schema
* a 100-token user question

Without caching, almost the entire 6,000+ token context is processed repeatedly.

Instead:

```text
Shared policies
Product terminology
Examples
Tool definitions
        ↓
   CACHE BREAKPOINT
        ↓
Customer-specific question
```

Requests can reuse the expensive stable prefix while the user-specific suffix changes. This is especially valuable for high-volume assistants and agent loops with substantial common context. OpenAI specifically recommends keeping stable instructions and shared material before changing values such as user input, timestamps, and request IDs. ([OpenAI Developers][1])

---

### When to use it

Use prompt caching when you repeatedly send **large, stable prefixes**: enterprise policies, coding-agent instructions, schemas, long few-shot examples, tool definitions, or shared reference context. ([OpenAI Developers][1])

Don't optimize for caching when prompts are small, rarely repeated, or highly dynamic. Constantly changing material before the breakpoint defeats cache reuse and can produce repeated cache writes. ([OpenAI Developers][1])

### Architecture takeaway

Treat prompt design partly like **data-layout optimization**:

**stable + reusable → early**
**dynamic + request-specific → late**

And monitor:

**cache-hit tokens / cache-written tokens**

The broader lesson is important: **GenAI performance optimization isn't only about choosing a smaller model. The structure of the context itself can materially affect latency and cost.**

[1]: https://developers.openai.com/api/docs/guides/prompt-caching "Prompt caching | OpenAI API"
