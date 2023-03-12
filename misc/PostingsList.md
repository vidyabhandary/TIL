### What is Postings list ?

Reading Chapter 3 in Designing Data-Intensive Applications by Martin Kleppman, I came across this sentence in the subtopic "Data Structures That Power Your Database".

"A secondary index can easily be constructed from a key-value index. The main difference
is that keys are not unique; i.e., there might be many rows (documents, vertices)
with the same key. _This can be solved in two ways: either by making each value in the
index a list of matching row identifiers (like a postings list in a full-text index) or by
making each key unique by appending a row identifier to it_. Either way, both B-trees
and log-structured indexes can be used as secondary indexes."

While appending a row identifier is clear, what is postings list as mentioned in the following sentence ?

**"Making each value in the index a list of matching row identifiers (like a postings list in a full-text index)"**

1. A postings list is a data structure that is commonly used to represent the inverted index of a full-text search engine.

2. An inverted index is an index structure that maps each unique term in a document collection to the documents that contain that term. This reminds me of **TF-IDF** in NLP.

3. A postings list is essentially a list of document IDs or pointers to documents that contain a particular term in the index.

   For example, if the term "philomath" appears in documents 1, 3, 5, and 7, the postings list for "philomath" would contain the IDs or pointers to those documents. When a search query contains the term "philomath", the search engine can quickly retrieve the postings list for "philomath" and use it to identify the relevant documents.

### Use of postings list in TF-IDF

> TF-IDF is calculated by multiplying the term frequency (TF), which is the number of times a term appears in a document, by the inverse document frequency (IDF), which is a measure of how rare the term is in the entire document collection.

> The **postings list** is used to calculate the term frequency for each document. The postings list is essentially a list of document IDs or pointers that contain a particular term, so the length of the postings list for a term is equal to the frequency of the term in the document collection.

---

Ref:

1. Designing Data-Intensive Applications by Martin Kleppman - Chapter 3 - Storage and Retrieval
