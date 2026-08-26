## LoRA 
— Fine-Tune Behavior Without Retraining the Whole Model

### Concept

Suppose a 7-billion-parameter model is already good at language, reasoning, and coding, but you want it to become particularly good at **your organization's style of requirement analysis**.

Full fine-tuning modifies billions of model weights. That is expensive in GPU memory, training time, and storage.

**LoRA — Low-Rank Adaptation — freezes the original model and inserts small trainable matrices into selected layers.** You train only these adapter parameters. Hugging Face's PEFT documentation describes LoRA as one of the most widely used parameter-efficient fine-tuning techniques because it can dramatically reduce the number of trainable parameters while preserving the underlying model. ([Hugging Face][1])

Conceptually:

```text
Foundation model weights ───────────── frozen
             │
             ├── small LoRA adapter ── trained
             │
             ↓
        specialized model
```

You can therefore keep **one base model + several small adapters** for different tasks.

---

### Practical case study

Imagine you have 10,000 expert-reviewed examples showing how your solution architects transform discovery-call notes into:

> requirement → assumption → risk → clarification question

Prompt engineering gets you 80% of the way there, but the output style and categorization remain inconsistent.

This is a good candidate for **supervised fine-tuning with LoRA**.

Importantly, you would *not* fine-tune the model merely to teach it your latest product catalogue. Product facts change frequently; that belongs in **RAG or an API/tool call**.

A useful distinction is:

**RAG teaches the model what to know at runtime.**
**Fine-tuning teaches the model how you want it to behave.**

---

### Production-oriented Python

Using **Hugging Face TRL + PEFT**:

```python
import os
from pathlib import Path

from datasets import load_dataset
from peft import LoraConfig
from trl import SFTConfig, SFTTrainer


MODEL_ID = os.environ["MODEL_ID"]
TRAIN_FILE = Path(os.environ["TRAIN_FILE"])

if not TRAIN_FILE.is_file():
    raise RuntimeError(f"Training dataset not found: {TRAIN_FILE}")


dataset = load_dataset(
    "json",
    data_files=str(TRAIN_FILE),
    split="train",
)

required_columns = {"messages"}
if not required_columns.issubset(dataset.column_names):
    raise ValueError(
        "Dataset must contain conversational 'messages' examples."
    )


lora = LoraConfig(
    r=16,
    lora_alpha=32,
    lora_dropout=0.05,
    bias="none",
    task_type="CAUSAL_LM",

    # QLoRA-style targeting across transformer linear layers.
    target_modules="all-linear",
)


training = SFTConfig(
    output_dir="./artifacts/solutioning-adapter",
    learning_rate=2e-4,
    num_train_epochs=3,
    max_length=2048,
    packing=True,

    # Do not train the model to reproduce the user's prompt.
    assistant_only_loss=True,

    logging_steps=10,
    save_strategy="epoch",
    report_to="none",
)


trainer = SFTTrainer(
    model=MODEL_ID,
    args=training,
    train_dataset=dataset,
    peft_config=lora,
)

trainer.train()
trainer.save_model()
```

TRL currently supports conversational datasets containing `messages` and integrates directly with PEFT adapters. `assistant_only_loss=True` can restrict training loss to assistant responses, while PEFT supports `target_modules="all-linear"` for QLoRA-style adapter training. ([Hugging Face][2])

---

### When to use it

Use LoRA when you have **many high-quality examples of a stable behavior** you want the model to learn: domain terminology, classification conventions, response structure, specialized reasoning patterns, or organization-specific writing behavior.

Don't reach for LoRA when the problem is **fresh knowledge, missing documents, a handful of examples, or something that prompt engineering/RAG already solves well**.

### Architecture takeaway

A common production stack is not:

**RAG *or* fine-tuning**

but:

**base model + LoRA behavior adapter + RAG for current knowledge + tools for live systems**

That separation matters. Your adapter may remain stable for months while the retrieved knowledge changes every day.

**Key principle:** *Fine-tune behavior; retrieve knowledge.*

[1]: https://huggingface.co/docs/peft/main/package_reference/lora?utm_source=chatgpt.com "LoRA · Hugging Face"
[2]: https://huggingface.co/docs/trl/sft_trainer?utm_source=chatgpt.com "SFT Trainer · Hugging Face"
