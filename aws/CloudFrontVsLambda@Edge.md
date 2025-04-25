# CloudFront Functions vs. Lambda@Edge: When to Use Which?

Think of it like tools:

- **CloudFront Functions:** Like a **small, super-fast pocket knife**.
- **Lambda@Edge:** Like a **more powerful multi-tool or toolbox**.

---

**Use CloudFront Functions when:**

1.  **You need EXTREME speed and lowest latency:** They run _very_ fast (sub-millisecond).
2.  **Your logic is SIMPLE:** Things like:
    - Rewriting URLs (e.g., `/product/123` to `/product.html?id=123`).
    - Adding/removing/modifying HTTP headers (e.g., adding a security header).
    - Normalizing cache keys (e.g., ignoring certain query strings for caching).
    - Simple redirects.
    - Basic access control (like checking for a specific header).
3.  **You DON'T need network access:** Functions cannot call external APIs or other AWS services.
4.  **You DON'T need complex computation or large amounts of memory.**
5.  **Cost is a major factor:** They are generally cheaper per invocation.
6.  **You only need to modify the request/response between the _viewer_ and CloudFront.** (Viewer Request/Viewer Response events).

**Key Points for CloudFront Functions:** Lightweight, super-fast, simple logic, no network calls, cheaper, viewer-facing events only.

---

**Use Lambda@Edge when:**

1.  **You need MORE complex logic:** Anything beyond simple manipulations.
2.  **You NEED network access:** To call external APIs, databases, authentication services, or other AWS services.
3.  **You need MORE processing time or memory:** For more intensive computations.
4.  **You need access to the request BODY:** CloudFront Functions cannot access the request body.
5.  **You need to run code closer to your ORIGIN server:** Lambda@Edge can also run on Origin Request/Origin Response events (in addition to Viewer events). This lets you modify requests _before_ they hit your origin or responses _after_ they leave your origin.
6.  **You need languages other than JavaScript:** Lambda@Edge supports Node.js and Python.

**Key Points for Lambda@Edge:** Powerful, more complex logic, network calls possible, longer runtime/more memory, access request body, runs on all CloudFront events (viewer and origin), more expensive.

---

**Simple Rule of Thumb:**

- Start with **CloudFront Functions** if your task is simple, fast header/URL manipulation.
- Move to **Lambda@Edge** if you hit limitations (need network calls, complex logic, longer runtime, access to request body, or origin-facing events).
