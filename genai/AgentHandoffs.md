## Agent Handoffs 
— Transfer Control, Don’t Just Call Another Agent

### Concept

In a multi-agent system, there are two very different orchestration patterns:

**Manager pattern:** one orchestrator remains in control and calls specialist agents like tools.

**Handoff pattern:** one agent **transfers control** to another specialist, which then continues the conversation.

```text
Manager pattern
User → Orchestrator → Security Agent
                   ← result
     ← Orchestrator answers


Handoff pattern
User → Triage Agent
            ↓
      Security Agent → User
```

OpenAI's current Agents SDK explicitly supports both patterns. With a handoff, the destination agent normally receives the conversation history and becomes the active agent. ([OpenAI GitHub][1])

---

### Practical case study

Imagine a **proposal copilot**.

The initial agent handles general questions:

> “Review this proposal and identify concerns.”

During the conversation the user says:

> “Now assess whether our EU data-residency design meets the security requirements.”

Instead of the general agent pretending to be a security specialist, it can transfer control to a **Security Architecture Agent**.

That specialist has:

* security-specific instructions,
* security tools and policies,
* perhaps a stronger reasoning model,
* stricter guardrails.

The user can continue naturally without re-explaining the proposal.

---

### Production-oriented Python

The handoff can carry **typed metadata** such as why escalation occurred:

```python
import logging
import os

from agents import Agent, Runner, RunContextWrapper, handoff
from pydantic import BaseModel, Field

logger = logging.getLogger(__name__)


class Escalation(BaseModel):
    reason: str = Field(min_length=10, max_length=300)
    severity: str = Field(pattern="^(medium|high)$")


async def log_security_handoff(
    ctx: RunContextWrapper[None],
    data: Escalation,
) -> None:
    # Do not perform privileged actions here based only on model input.
    logger.info(
        "security_handoff",
        extra={
            "reason": data.reason,
            "severity": data.severity,
        },
    )


security_agent = Agent(
    name="Security Architecture Specialist",
    model=os.environ["SECURITY_MODEL"],
    instructions=(
        "Assess security architecture, data residency, identity, "
        "network boundaries and regulatory requirements. "
        "Clearly distinguish facts, assumptions and risks."
    ),
)

security_handoff = handoff(
    agent=security_agent,
    input_type=Escalation,
    on_handoff=log_security_handoff,
)

triage_agent = Agent(
    name="Proposal Architect",
    model=os.environ["DEFAULT_MODEL"],
    instructions=(
        "Handle general solutioning questions. "
        "Transfer substantial security analysis to the "
        "Security Architecture Specialist."
    ),
    handoffs=[security_handoff],
)


async def analyse(request: str) -> str:
    if not request.strip():
        raise ValueError("Request cannot be empty")

    result = await Runner.run(
        triage_agent,
        request,
        max_turns=8,
    )

    logger.info(
        "agent_run_completed",
        extra={"final_agent": result.last_agent.name},
    )

    return str(result.final_output)
```

The SDK exposes the `input_type` schema to the model, validates the handoff payload locally, and passes the parsed value to `on_handoff`. A handoff can also filter what conversation history the receiving agent sees. ([OpenAI GitHub][2])

---

### When to use it

Use handoffs when specialists should **own the next part of the conversation**: security, legal, billing, technical support, database troubleshooting, etc.

Don't use handoffs merely because you have multiple agents. If a central agent needs to combine outputs from several specialists—

**Architecture + Pricing + Security → one recommendation**

—keep the orchestrator in control and expose specialists as **agent tools** instead. ([OpenAI GitHub][1])

---

### Architecture takeaway

The choice is fundamentally about **control flow**:

**Specialist provides expertise → Agent-as-tool**

**Specialist takes ownership → Handoff**

And don't confuse orchestration with authorization: handoff metadata is still model-generated. OpenAI's current documentation specifically warns that authorization based on handoff arguments must be checked deterministically before performing application side effects. ([OpenAI GitHub][2])

**Key principle:**

> **Create multiple agents because responsibilities genuinely differ—not simply because multi-agent architecture sounds more advanced.**

[1]: https://openai.github.io/openai-agents-python/agents/?utm_source=chatgpt.com "Agents - OpenAI Agents SDK"
[2]: https://openai.github.io/openai-agents-python/handoffs/?utm_source=chatgpt.com "Handoffs - OpenAI Agents SDK"
