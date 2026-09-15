## Authorization-Aware RAG 
— Filter Before Retrieval

### Concept

A serious enterprise RAG mistake is:

**retrieve documents → send them to LLM → hide unauthorized information afterward**

By then, the model has **already seen the data**.

The safer pattern is **authorization-aware retrieval**:

```text
Authenticated user
      ↓
Verified identity / groups
      ↓
ACL filtering
      ↓
Vector / hybrid retrieval
      ↓
Only authorized chunks
      ↓
LLM
```

This is sometimes called **security trimming** or **ACL-aware retrieval**.

The important principle is:

> **Authorization belongs before retrieval, not in the prompt.**

Amazon Bedrock Managed Knowledge Bases, for example, can now apply document ACLs during retrieval using verified `userContext`. AWS explicitly warns that ACL-aware filtering does **not** authenticate the user; your application must establish identity first. ([AWS Documentation][1])

### Practical case study

Imagine an internal HR assistant containing:

* general HR policies,
* employee records,
* executive compensation,
* restructuring plans.

A normal semantic search for:

> “What salary adjustments are planned this year?”

might find an executive compensation document because it is highly relevant.

A prompt saying:

> “Only show documents this employee may access”

is **not security**. The restricted text has already entered the model's context.

Instead:

```text
Employee identity
      ↓
Allowed document ACLs
      ↓
Semantic search only within allowed documents
```

The restricted document never reaches the LLM.

### When to use it

Use authorization-aware RAG whenever a knowledge base contains **different permissions by user, customer, department, project, tenant, classification, or role**.

It is usually unnecessary for genuinely public corpora where every user can access every document.

### Important current development

On **September 9, 2026**, AWS added `CheckIngestedDocumentAcl` and `GetIngestedDocumentAcl` APIs to Bedrock Managed Knowledge Bases. You can now ask:

**“Should Alice be able to retrieve document X?”**

and inspect the ACL actually stored for that document—important for debugging security issues in production RAG. ([aws.amazon.com][2])

### Architecture takeaway

Separate these three concerns:

**Authentication** → *Who are you?*
**Authorization** → *What may you see?*
**Retrieval** → *Which permitted information is relevant?*

Do **not** make the LLM responsible for any of them.

> **The safest confidential document is the one the model never receives.**

[1]: https://docs.aws.amazon.com/bedrock/latest/userguide/kb-test-retrieve-acl.html "ACL-aware retrieval on managed knowledge bases - Amazon Bedrock"
[2]: https://aws.amazon.com/about-aws/whats-new/2026/09/amazon-bedrock-knowledge-base-debugging-document-access-control/ "Amazon Bedrock Managed Knowledge Base adds APIs and console support for debugging document-level access control - AWS"
