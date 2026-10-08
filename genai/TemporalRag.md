## Temporal RAG 
— Retrieve What Was True at the Right Time

**Domain:** Data, Retrieval & Knowledge Systems  
**Reading time:** 2–3 minutes

### 1. Concept

Traditional RAG retrieves documents based on **semantic relevance**.

But relevance alone isn't enough when information changes over time.

Consider two company policies:

| Policy version | Effective period | Refund window |
|---|---|---|
| Version 1 | Jan–Jun 2026 | 30 days |
| Version 2 | Jul 2026 onward | 14 days |

Both are highly relevant to the question:

**"What is our refund policy?"**

However, only one applies today.

**Temporal RAG** adds an important condition to retrieval:

> Retrieve information that is both semantically relevant **and valid for the requested date**.

### 2. Realistic business case

Imagine an enterprise HR assistant answering:

"What was the maternity leave policy in March 2025?"

A conventional RAG system might retrieve the latest policy and incorrectly apply it retrospectively.

Temporal RAG works differently:

```text
User question
      ↓
Identify relevant date
      ↓
Filter documents by effective period
      ↓
Semantic similarity search
      ↓
LLM generates grounded answer
```

This matters in **HR, finance, insurance, regulatory compliance and contractual applications**.

### 3. Production-grade code

PostgreSQL supports timestamp range types, while pgvector supports similarity search using cosine distance. Together, they provide a practical implementation. [PostgreSQL](https://www.postgresql.org/docs/current/rangetypes.html?utm_source=chatgpt.com)

```python
import logging
import math
from datetime import datetime
from uuid import UUID

import psycopg
from pgvector import Vector
from pgvector.psycopg import register_vector
from pydantic import Field, SecretStr
from pydantic_settings import BaseSettings

logger = logging.getLogger(__name__)


class Settings(BaseSettings):
    database_url: SecretStr
    embedding_dims: int = Field(default=1536, gt=0)


cfg = Settings()


def temporal_search(
    embedding: list[float],
    as_of: datetime,
    tenant_id: UUID,
    limit: int = 5,
) -> list[str]:

    if as_of.utcoffset() is None:
        raise ValueError("Timezone-aware date required")

    if not 1 <= limit <= 20:
        raise ValueError("Invalid result limit")

    if len(embedding) != cfg.embedding_dims or not all(
        math.isfinite(x) for x in embedding
    ):
        raise ValueError("Invalid embedding")

    try:
        with psycopg.connect(
            cfg.database_url.get_secret_value(),
            connect_timeout=5,
        ) as conn:
            register_vector(conn)

            rows = conn.execute(
                """
                WITH eligible AS MATERIALIZED (
                    SELECT content, embedding
                    FROM policy_chunks
                    WHERE tenant_id = %s
                      AND valid_during @> %s::timestamptz
                      AND approved = TRUE
                )
                SELECT content
                FROM eligible
                ORDER BY embedding <=> %s
                LIMIT %s
                """,
                (tenant_id, as_of, Vector(embedding), limit),
            ).fetchall()

        logger.info("temporal_search_completed", extra={"hits": len(rows)})
        return [row[0] for row in rows]

    except psycopg.Error:
        logger.exception("temporal_search_failed")
        raise
```

**The critical line:**

```sql
valid_during @> %s::timestamptz
```

It means: **Only consider documents whose validity period contains the requested date.**

The materialized intermediate result ensures the date filter precedes similarity ranking. This favors correctness, although large datasets may require a more optimized indexing strategy.

### 4. When to use it

Use Temporal RAG when documents have effective dates, historical revisions or time-bound applicability.

**Don't use it** for primarily timeless knowledge, such as programming tutorials or mathematics, where ordinary RAG is sufficient.

### 5. Architecture takeaway

Traditional RAG asks:

**"Which document best matches this question?"**

Temporal RAG asks:

**"Which document best matches this question and was valid at the relevant time?"**

This distinction is crucial for enterprise systems.

A document can be **factually correct, semantically relevant, and still be the wrong document to use**.

