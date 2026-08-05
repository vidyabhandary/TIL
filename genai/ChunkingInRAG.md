# **Chunking in RAG**

### Concept

In Retrieval-Augmented Generation, documents are usually split into smaller pieces called **chunks** before embeddings are created.

Why? An entire 80-page document is too large and too broad to retrieve effectively. Smaller chunks make retrieval more precise.

But chunk size matters:

* **Too large:** retrieved text contains irrelevant content.
* **Too small:** important context gets broken apart.
* **Overlapping chunks:** help preserve meaning across boundaries.

---

### Practical case study

Suppose a support assistant answers questions from software manuals.

A user asks:

> “How do I configure SSO for external users?”

If the manual is split strictly every 500 characters, the SSO configuration steps may be separated from the prerequisites. The assistant may retrieve only half the answer.

A better approach is to split by:

1. Headings
2. Paragraphs
3. Token limits
4. Small overlap between chunks

This keeps related information together.

---

### Targeted Python example

```python
def chunk_text(text: str, chunk_size: int = 40, overlap: int = 10):
    words = text.split()
    chunks = []

    start = 0

    while start < len(words):
        end = start + chunk_size
        chunks.append(" ".join(words[start:end]))

        start += chunk_size - overlap

    return chunks


document = """
Single sign-on allows users to authenticate through an identity provider.
Before enabling SSO, configure the application redirect URL and metadata.
External users must also be assigned to the correct access group.
"""

for chunk in chunk_text(document):
    print(chunk)
```

The overlap repeats a small amount of text between neighboring chunks, reducing the chance that a critical sentence boundary is lost.

---

### When to use it

Use chunking when:

* Indexing long documents for RAG
* Searching policies, manuals, contracts, or knowledge bases
* Building assistants that need precise citations
* Documents exceed the model’s practical context size

### When not to use it

Avoid arbitrary fixed-size chunking when:

* The source is already structured as small records
* Meaning depends heavily on tables or diagrams
* A document is short enough to retrieve in full
* Splitting would separate legally or logically linked clauses

---

### Architecture takeaway

**Chunking is not just preprocessing; it directly affects answer quality.**

A strong RAG design usually combines:

* Structure-aware splitting
* Controlled chunk size
* Small overlap
* Metadata such as section, page, and document ID
* Retrieval evaluation using real user questions

Poor chunking cannot be fully repaired by a better LLM.
