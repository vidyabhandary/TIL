## MCP Boundary Decisions

## 1. Level

**Foundation — Consolidation**

---

## 2. Focus

One architectural question:

> **Where should responsibility live in an MCP solution?**

You have now seen four different boundaries:

| Decision                      | Architectural question                                                     |
| ----------------------------- | -------------------------------------------------------------------------- |
| **Host / client / server**    | Who coordinates, connects and provides the capability?                     |
| **Tool / resource / prompt**  | Is this an action, context or a user-invoked workflow?                     |
| **`stdio` / Streamable HTTP** | Is the capability local and host-managed, or remotely operated and shared? |
| **Authorization**             | What is the minimum access needed for this operation?                      |

The exam-oriented skill is not remembering four tables independently.

It is being able to read a scenario and repeatedly ask:

> **What responsibility belongs where?**

MCP deliberately standardises context exchange rather than prescribing how the host should build its entire AI application. The host coordinates MCP connections and model interaction; servers expose bounded capabilities such as tools, resources and prompts.

### Current-spec reminder

The **July 28, 2026 MCP specification** materially changed protocol lifecycle behaviour: the protocol core is now stateless, the old `initialize`/`initialized` handshake and `Mcp-Session-Id` were retired, and Streamable HTTP requests are self-describing. Some older documentation and examples still show the previous stateful lifecycle, so use the current specification when lifecycle details matter.

---

## 3. Why an architect cares

Most poor MCP designs are not caused by misunderstanding JSON-RPC.

They come from putting responsibility in the wrong place.

Examples:

* letting an MCP server see more conversation context than necessary;
* representing passive knowledge as a state-changing tool;
* deploying a workstation-only capability as an enterprise HTTP service;
* granting write permissions simply because a future workflow might require them;
* assuming successful authentication means every requested action should execute.

A useful diagnostic sequence is:

**Boundary → primitive → transport → privilege**

First determine the security and ownership boundary.

Then decide what the server exposes.

Then decide how the server is reached.

Finally decide how much access is necessary.

Avoid starting with technology such as:

> “We should use HTTP.”

Start with the requirement:

> “This capability is centrally operated and must serve hundreds of authorised hosts.”

The technology decision should follow.

---

## 4. Architect’s lens

For any MCP scenario, ask these three questions:

1. **Who should control this decision?**
   User, host/application, model or MCP server?

2. **Is information moving, or is an operation being performed?**
   This usually distinguishes resources from tools.

3. **What is the smallest deployment and permission boundary that satisfies the requirement?**
   Avoid remote infrastructure or broad privileges unless the requirement justifies them.

---

# 5. Integrated scenario

An insurance company is building a Claude-based claims-review assistant.

An analyst opens a claim in the company application.

The assistant needs to:

* read the selected claim file and policy wording;
* retrieve a live fraud-risk score from a central service;
* allow the analyst to deliberately launch a standard “Claims Review” procedure;
* write an analyst-approved review note back to the claims platform;
* process some scanned attachments stored only on the analyst’s workstation;
* prevent ordinary claim-reading sessions from automatically receiving write access.

Consider each requirement separately.

### Claim file and policy wording

The application already knows which claim and policy the analyst selected.

These are contextual inputs rather than actions.

A sensible representation is **resources** controlled by the host.

### Live fraud-risk score

The score must be requested from an operational system at decision time.

That is naturally a **tool**.

### “Claims Review” procedure

The analyst deliberately starts a recognised workflow that tells Claude how to review evidence and use appropriate capabilities.

That is naturally a **prompt**.

### Scanned local attachments

These files exist only on the analyst’s workstation, and the processing component needs local filesystem access.

A restricted local MCP server using **`stdio`** is a reasonable fit.

### Central claims platform

The claims service is centrally deployed, independently operated and used by many analysts.

A remote MCP server using **Streamable HTTP** is the natural deployment model.

### Writing the review note

Most interactions require only claim-reading access.

Therefore the assistant should not begin every session with broad write privileges.

A stronger design starts with read permissions and obtains the narrowly required write permission when the analyst chooses to submit the review note. Authentication, authorization and explicit approval remain separate controls.

The result is not “one MCP architecture.”

It is several deliberately chosen boundaries working together:

```text
Claims Assistant Host
│
├── selected claim resources
├── Claims Review prompt
│
├── MCP client ── stdio ── Local Attachment Server
│
└── MCP client ── Streamable HTTP ── Claims Platform Server
                                  │
                                  ├── fraud_check tool
                                  └── submit_review_note tool
```

---

# 6. Checkpoint questions

These are **practice-derived questions**, not authentic Anthropic certification questions.

## Question 1

The company proposes sending the complete Claude conversation to the local attachment server so that the server can decide which files Claude might need.

What is the strongest architectural objection?

**A.** MCP servers cannot process text.

**B.** The host should control context disclosure and provide the server only the information needed for its bounded capability.

**C.** Local servers must use HTTP.

**D.** Claude cannot work with multiple MCP servers.

### Answer: **B**

The attachment server requires enough information to process selected files; it does not automatically need the full conversation. The host is the coordination boundary and should minimise unnecessary context disclosure.

**Spot the clue:** *“complete conversation.”*

---

## Question 2 — Select TWO

Which two mappings best fit the scenario?

**A.** Policy wording → resource
**B.** Fraud-risk lookup → prompt
**C.** Standard Claims Review procedure → prompt
**D.** Submit-review-note operation → resource
**E.** Local attachment processing → reusable system prompt

### Answers: **A and C**

Policy wording supplies context, so a resource fits naturally.

The Claims Review procedure is an explicitly selected reusable workflow, so a prompt is appropriate.

The fraud lookup and note submission perform operations and would therefore be better represented as tools. Official MCP guidance characterises tools as model-invoked functions, resources as application-provided contextual data and prompts as user-controlled reusable templates.

**Spot the clue:** Distinguish **context**, **workflow** and **operation** before looking at the technology.

---

## Question 3

Analysts read approximately fifty claims for every claim on which they submit a review note.

The security team wants to minimise the impact of compromised credentials.

Which design is strongest?

**A.** Give every session permanent read/write claims access because submission is eventually required.

**B.** Use read access for normal review and obtain narrowly scoped write authorization when a note is actually submitted.

**C.** Give Claude write access but tell it through the system prompt never to use it unnecessarily.

**D.** Remove authorization because only employees can access the application.

### Answer: **B**

The common workflow needs only read access. Write privilege should therefore be introduced only when the protected operation requires it.

This reduces the blast radius of a compromised credential and makes the relationship between user intent and privileged action clearer. MCP guidance recommends authorization for enterprise and user-specific services, while the latest specification further hardens authorization behaviour.

### Strongest distractor: A

A is operationally convenient because it avoids another authorization step.

But it optimises for convenience by widening the security boundary across **every** session, even though write access is rarely necessary.

### What additional fact could change the decision?

If claims analysts spent essentially every session entering approved review decisions and the permission were already narrowly limited to writing review notes—not modifying claim payments, claimant details or policy data—a short-lived role-specific write permission at login could be reasonable.

The principle remains the same:

> Grant what the normal job genuinely requires, not everything the surrounding system can do.

---

## 7. Spot the pattern

When MCP questions become complicated, look for these clue types:

* **“Selected document”, “reference material”, “context”** → think **resource**.
* **“Perform”, “query”, “update”, “create”, “submit”** → think **tool**.
* **“User explicitly launches a standard workflow”** → think **prompt**.
* **“Only on this workstation”, “host starts the process”** → think **`stdio`**.
* **“Shared centrally”, “many clients”, “independently scaled”** → think **Streamable HTTP**.
* **“Rare privileged operation”, “minimise blast radius”** → think **least privilege / step-up authorization**.
* **“Who sees what?”** → return to the **host-controlled security boundary**.

---

## 8. One-line architect rule

> **For MCP, place each responsibility at the narrowest sensible boundary: host for coordination, resources for context, tools for operations, prompts for deliberate workflows, and privileges only where the current task requires them.**

---

## 9. Source basis

* Official MCP architecture and server-concept documentation for host/client/server responsibilities and tools, resources and prompts.
* Official MCP authorization guidance for protected remote services.
* Official **July 28, 2026 MCP specification release** for the current stateless protocol model, authorization hardening and deprecations.
* Practice-derived checkpoint scenarios based on official architectural patterns; they are **not authentic certification questions**.
