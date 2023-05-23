### Misc of Misc

A kaleidoscope of tech snippets that are too small to fit into a single post.
Like an anki card sized note.

---

1.  **The key idea with Avro is that the writer’s schema and the reader’s schema don’t have to be the same—they only need to be compatible. When data is decoded (read), the Avro library resolves the differences by looking at the writer’s schema and the reader’s schema side by side and translating the data from the writer’s schema into the reader’s schema.**

Ref:

- Designing Data-Intensive Applications by Martin Kleppman - Chapter 4 - Encoding and Evolution

2.  What is read-after-write consistency ?

**This is also known as read-your-writes consistency. This is a guarantee that if the user reloads the page, they will always see any updates they submitted themselves. It makes no promises about other users: other users’ updates may not be visible until some later time. However, it reassures
the user that their own input has been saved correctly.**

- Designing Data-Intensive Applications by Martin Kleppman - Chapter 5 - Replication

---
