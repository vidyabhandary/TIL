## Adaptive Reasoning
— Spend More “Thinking” Only Where It Helps**

### Concept

You’ve already seen **model routing**: choose a cheaper or stronger model depending on the task.

**Adaptive reasoning effort is different.** You keep the *same model*, but vary how much inference-time reasoning it uses.

For a reasoning-capable model, that lets you treat compute as a dial:

**simple extraction → low reasoning**
**multi-step architecture analysis → medium/high reasoning**
**high-stakes trade-off → very high reasoning**

This matters because extra reasoning is not free: it can increase latency and output-token usage, and many simple tasks gain little from it. Current GPT-5.6 Sol supports `none`, `low`, `medium`, `high`, `xhigh`, and `max` reasoning effort. ([OpenAI Developers][1])

### Practical case study

Consider a proposal copilot handling three tasks with the **same model**:

* “Extract the customer's cloud preference.” → **low**
* “Compare three deployment architectures against residency, latency, and cost.” → **high**
* “Rewrite this paragraph professionally.” → **none/low**

Running all three at `high` is like using a senior architect to copy values from a spreadsheet.

A better production design is:

**task metadata → reasoning policy → model call → quality/latency telemetry**

That gives you another optimization lever *before* introducing multiple models.

### Production-oriented Python

```python
import logging
import os
from enum import StrEnum

from openai import APIError, OpenAI
from pydantic import BaseModel, Field
from pydantic_settings import BaseSettings, SettingsConfigDict

logger = logging.getLogger(__name__)


class Settings(BaseSettings):
    model_config = SettingsConfigDict(env_prefix="AI_")
    model: str = "gpt-5.6-sol"


class TaskType(StrEnum):
    EXTRACTION = "extraction"
    SUMMARIZATION = "summarization"
    ANALYSIS = "analysis"
    HIGH_STAKES_DECISION = "high_stakes_decision"


class Request(BaseModel):
    prompt: str = Field(min_length=5, max_length=50_000)
    task_type: TaskType


def reasoning_effort(task_type: TaskType) -> str:
    return {
        TaskType.EXTRACTION: "low",
        TaskType.SUMMARIZATION: "low",
        TaskType.ANALYSIS: "high",
        TaskType.HIGH_STAKES_DECISION: "xhigh",
    }[task_type]


settings = Settings()
client = OpenAI()


def run(request: Request) -> str:
    effort = reasoning_effort(request.task_type)

    try:
        response = client.responses.create(
            model=settings.model,
            reasoning={"effort": effort},
            input=request.prompt,
        )

        usage = response.usage
        reasoning_tokens = (
            usage.output_tokens_details.reasoning_tokens
            if usage and usage.output_tokens_details
            else 0
        )

        logger.info(
            "llm_request_completed",
            extra={
                "task_type": request.task_type.value,
                "reasoning_effort": effort,
                "reasoning_tokens": reasoning_tokens,
            },
        )

        return response.output_text

    except APIError:
        logger.exception(
            "LLM request failed",
            extra={"reasoning_effort": effort},
        )
        raise
```

The current Responses API accepts the `reasoning.effort` control, and usage metadata exposes reasoning-token consumption so you can evaluate whether higher effort is actually buying better outcomes. ([OpenAI Developers][2])

### When to use it

Use adaptive effort when your workload mixes **routine extraction/summarization with genuine multi-step reasoning**.

Don't automatically crank reasoning to maximum for every request. Instead, measure whether the extra compute improves your **domain evals** enough to justify added latency and cost.

### Architecture takeaway

Think of GenAI optimization as having **two independent knobs**:

**Model routing** → *Which model should do this?*
**Reasoning effort** → *How hard should that model think?*

A relevant recent development: OpenAI's **August 13, 2026** GPT-5.6 builder guide reports cases where reducing reasoning effort materially improved economics, and notes that GPT-5.6 Sol at **low** reasoning outperformed GPT-5.5 at **high** on one benchmark under the same harness. That does **not** mean low is universally best—it means old reasoning defaults should be re-evaluated when models improve. ([OpenAI][3])

**Key principle:**

> **Allocate reasoning compute according to task difficulty, then prove the policy with evals rather than intuition.**

[OpenAI: GPT-5.6 builder guide — August 13, 2026](https://openai.com/index/builders-guide-to-gpt-5-6/?utm_source=chatgpt.com)

[1]: https://developers.openai.com/api/docs/models/gpt-5.6-sol?utm_source=chatgpt.com "GPT-5.6 Sol Model | OpenAI API"
[2]: https://developers.openai.com/api/reference/typescript/resources/beta/subresources/responses/methods/create?utm_source=chatgpt.com "Create a model response | OpenAI API Reference"
[3]: https://openai.com/index/builders-guide-to-gpt-5-6/?utm_source=chatgpt.com "The builder’s guide to GPT‑5.6 | OpenAI"
