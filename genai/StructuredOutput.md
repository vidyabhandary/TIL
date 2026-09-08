## Structured Outputs 
— Make LLM Responses Machine-Safe

### Concept

Suppose you ask an LLM:

> “Extract requirements, priority and owner from this RFP.”

Prompting it to **“return JSON”** is not enough. You might receive:

```json
{"priority": "critical"}
```

when your application expects:

```json
{"priority": "high", "owner": "...", "requirement": "..."}
```

**Structured Outputs** constrain generation to a defined schema:

**unstructured input → LLM + schema → validated typed object**

This is stronger than older **JSON mode**. JSON mode guarantees syntactically valid JSON; Structured Outputs can enforce the supplied JSON Schema. OpenAI's current API documentation explicitly recommends `json_schema` rather than the older `json_object` mode for models that support it. ([OpenAI Developers][1])

The mechanism is essentially **constrained decoding**: while generating, tokens that would violate the schema are excluded from the valid next-token choices. ([OpenAI][2])

### Practical case study

Consider an RFP-processing pipeline:

```text
RFP
 ↓
LLM extraction
 ↓
Proposal database
 ↓
Architecture workflow
```

If the LLM sometimes returns `priority="urgent"` and sometimes `severity=1`, downstream code becomes full of parsing and repair logic.

Instead, establish an application contract:

```text
priority = mandatory | high | medium | low
category = security | integration | data | functional
confidence = 0.0–1.0
```

The LLM is now producing something your software can reliably consume.

### Production-oriented Python

```python
import logging
from enum import StrEnum

from openai import APIError, AsyncOpenAI
from pydantic import BaseModel, Field
from pydantic_settings import BaseSettings, SettingsConfigDict

logger = logging.getLogger(__name__)


class Settings(BaseSettings):
    model_config = SettingsConfigDict(env_prefix="AI_")
    model: str


class Priority(StrEnum):
    MANDATORY = "mandatory"
    HIGH = "high"
    MEDIUM = "medium"
    LOW = "low"


class Requirement(BaseModel):
    requirement: str = Field(min_length=10, max_length=1000)
    priority: Priority
    owner: str | None = Field(default=None, max_length=100)
    confidence: float = Field(ge=0, le=1)


class Extraction(BaseModel):
    requirements: list[Requirement] = Field(max_length=50)


settings = Settings()
client = AsyncOpenAI()


async def extract_requirements(text: str) -> Extraction:
    if len(text.strip()) < 20:
        raise ValueError("Input document is too short")

    try:
        response = await client.responses.parse(
            model=settings.model,
            input=[
                {
                    "role": "developer",
                    "content": (
                        "Extract only requirements supported by the text. "
                        "Do not infer missing owners or priorities."
                    ),
                },
                {"role": "user", "content": text},
            ],
            text_format=Extraction,
        )

        result = response.output_parsed

        if result is None:
            raise RuntimeError("No structured result returned")

        logger.info(
            "requirements_extracted",
            extra={"count": len(result.requirements)},
        )

        return result

    except APIError:
        logger.exception("Requirement extraction failed")
        raise
```

Using a Pydantic model gives you **one definition for both the LLM contract and application validation**, avoiding hand-written JSON parsing.

### When to use it

Use Structured Outputs for **document extraction, classifiers, routing decisions, evaluation results, agent plans, API payload preparation and any LLM output consumed by software**.

Don't force everything into schemas when the desired output is naturally free-form—such as drafting an executive summary or brainstorming architecture options.

### Architecture takeaway

Structured Outputs solve:

> **“Is the output structurally valid?”**

They do **not** solve:

> **“Is the output factually correct?”**

A perfectly valid object can still contain:

```json
{"priority": "mandatory"}
```

when the RFP never said the requirement was mandatory.

So production architecture should separate:

**Schema validation → structural correctness**
**Grounding/evaluation → semantic correctness**
**Business rules → application correctness**

Current OpenAI models that advertise Structured Outputs support can enforce these contracts, while model capability pages also make clear that support varies by model—so schema support should be part of your model-selection criteria. ([OpenAI Platform][3])

**Key principle:** **Use probabilistic AI to determine the values; use deterministic schemas to control the shape.**

[1]: https://developers.openai.com/api/reference/java/resources/beta/subresources/responses?utm_source=chatgpt.com "Responses | OpenAI API Reference"
[2]: https://openai.com/index/introducing-structured-outputs-in-the-api/?utm_source=chatgpt.com "Introducing Structured Outputs in the API | OpenAI"
[3]: https://platform.openai.com/docs/models/gpt-4-turbo-and-gpt-4?utm_source=chatgpt.com "Models | OpenAI API"
