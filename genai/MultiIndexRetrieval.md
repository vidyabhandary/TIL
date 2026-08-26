## Multi-index retrieval
 Multi-index retrieval is a RAG technique where the same knowledge is stored/searchable through **multiple indexes**, so the system has more than one way to find the right information.

For example, suppose you have 10,000 company documents. You might create:

* **Vector index** → finds content with similar *meaning*
* **Keyword/BM25 index** → finds exact terms, IDs, product names
* **Summary index** → searches document-level summaries first
* **Metadata index** → filters by date, customer, department, document type, etc.

When a user asks:

> “What did Fluidra decide about customer creation?”

the system can search several indexes, combine the best matches, and then give those passages to the LLM.

### Why do this?

A **single index can miss things**. Semantic search is good at meaning but may struggle with exact codes or names; keyword search is the opposite.

So, very simply:

> **Multi-index retrieval = create multiple searchable views of the same knowledge and use the most appropriate one(s) for each query.**

Think of it like searching a library through **the title catalogue, subject catalogue, author catalogue, and full-text search** instead of relying on just one catalogue.

Multi-index retrieval means giving a RAG system multiple ways of finding information, rather than assuming one search/indexing strategy will work equally well for every kind of question.
