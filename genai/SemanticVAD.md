## Semantic VAD 
— Knowing When a User Has Finished Speaking

### 1. Concept

When building a voice-based AI assistant, how does it know when the user has finished speaking?

Traditional **Voice Activity Detection (VAD)** detects speech and silence.

But silence doesn't always mean someone has finished talking.

Consider:

> "I want to cancel my subscription..."  
> *(2-second pause)*  
> "...but only after my current billing period."

Traditional VAD might treat the pause as the end of the request.

**Semantic VAD** goes further. It estimates whether the user's utterance is complete based on its meaning, not merely the duration of silence.

```text
                  User pauses
                       │
             ┌─────────┴─────────┐
             ▼                   ▼
        Traditional VAD     Semantic VAD
             │                   │
       Silence detected    Meaning incomplete
             │                   │
       Start responding    Continue listening
```

OpenAI's Realtime API supports both `server_vad` and `semantic_vad`, with configurable responsiveness. [OpenAI Developers](https://developers.openai.com/api/docs/guides/realtime-vad?utm_source=chatgpt.com)

### 2. Practical case study

Imagine an AI-powered customer-support avatar.

A customer says:

> "My order arrived damaged, and I was wondering... actually, could you arrange a replacement?"

If the avatar interrupts after "wondering," the experience feels unnatural.

Semantic VAD reduces premature interruptions by recognizing that the user's thought may be unfinished.

This is particularly useful for **voice agents, AI tutors, interview assistants, and digital avatars**.

### 3. Production-oriented code

Using OpenAI's Realtime SDK, configure semantic turn detection on an existing voice session.

```python
import logging
from typing import Literal

from openai import AsyncOpenAI
from pydantic_settings import BaseSettings

logger = logging.getLogger(__name__)


class Settings(BaseSettings):
    model: str = "gpt-realtime-2.1"
    eagerness: Literal["low", "medium", "high"] = "medium"


async def monitor_turns() -> None:
    cfg = Settings()
    client = AsyncOpenAI()

    try:
        async with client.realtime.connect(
            model=cfg.model
        ) as conn:

            await conn.session.update(session={
                "type": "realtime",
                "audio": {
                    "input": {
                        "turn_detection": {
                            "type": "semantic_vad",
                            "eagerness": cfg.eagerness,
                            "create_response": False,
                        }
                    }
                }
            })

            # Audio is streamed by the application's
            # existing microphone/WebRTC integration.
            async for event in conn:
                if event.type == "input_audio_buffer.speech_started":
                    logger.info("user_started_speaking")

                elif event.type == "input_audio_buffer.speech_stopped":
                    logger.info("user_finished_speaking")
                    # Hand off to application response policy.

                elif event.type == "error":
                    logger.error("realtime_session_error")
                    raise RuntimeError("Realtime session failed")

    except Exception:
        logger.exception("voice_session_failed")
        raise
    finally:
        await client.close()
```

This is the turn-detection component, not a complete audio-streaming application. Automatic responses are deliberately disabled so the application can control when an answer is generated.

### 4. When to use it

Use semantic VAD when **natural conversational flow** matters, particularly when users pause, hesitate, or formulate complex questions.

**Don't use it** when minimizing response latency is more important than conversational naturalness, such as simple voice commands.

### 5. Important architectural development

Modern realtime APIs increasingly expose turn-taking behavior as a configurable capability.

OpenAI's semantic VAD supports three responsiveness settings:

- **High:** Respond sooner; greater risk of premature turn completion.
- **Medium:** Balance responsiveness and patience.
- **Low:** Wait longer for the user to finish.

This introduces a deliberate tradeoff between **latency and interruption risk**. [OpenAI Developers](https://developers.openai.com/api/reference/resources/realtime/client-events?utm_source=chatgpt.com)

### 6. Architecture takeaway

For voice agents, latency isn't simply:

**Speech recognition + LLM inference + speech synthesis**

It also includes **deciding when the user's turn is complete**.

> **A voice assistant that responds in 300 ms but frequently interrupts users may provide a worse experience than one that waits another second.**

Measure both response latency and premature-interruption rate.

