## MCP Least Privilege

### Concept

# Least Privilege: Grant Access Only When Needed

## 1. Level - **Foundation**

## 2. Today’s concept

An MCP connection should receive only the permissions needed for the task currently being performed.

This is the **principle of least privilege**.

A weak design grants a broad permission such as:

```text
hr:full-access
```

when the assistant initially connects.

A stronger design begins with limited access:

```text
employees:read
```

and requests an additional permission only when the user attempts a protected operation:

```text
payroll:write
```

This second pattern is called **step-up authorization**:

1. Begin with low-risk permissions.
2. Attempt a protected operation.
3. The server identifies the additional permission required.
4. The user authorizes the increased access.
5. The client retries the operation with the appropriate permission.

The current MCP authorization specification recommends that clients request only the scopes needed for their intended operations. It defines runtime scope challenges and a step-up flow for obtaining additional permissions when a protected operation is attempted.

The architectural objective is not to eliminate authorization prompts. It is to place them at the point where the user can understand **why the extra access is required**.

### Authorization is not the same as approval

These controls answer different questions:

| Control                 | Question answered                                            |
| ----------------------- | ------------------------------------------------------------ |
| **Authentication**      | Who is the user or client?                                   |
| **Authorization**       | Is that identity permitted to access this capability?        |
| **Action approval**     | Does the user intend to perform this specific operation now? |
| **Business validation** | Is this operation valid under organisational rules?          |

For example, a user may be authorised to delete documents but still need to confirm the deletion of a particular document.

A valid access token does not mean:

> “Execute every operation Claude requests.”

It means:

> “This identity may request operations within this defined permission boundary.”

The host and server must still enforce consent, operation-specific permissions and business rules. The MCP specification states that users should understand and explicitly consent to data access and tool operations, while hosts should provide clear review and authorisation controls.

---

## 3. Why an architect cares

Broad permissions often appear convenient because users authenticate only once.

However, convenience creates risk when the granted access is much larger than the task requires.

An excessively privileged token can increase:

* the impact of credential theft;
* the number of systems an attacker can reach;
* the damage caused by an incorrect tool invocation;
* the difficulty of determining user intent from audit logs;
* the disruption caused when the token must be revoked.

Official MCP security guidance warns that broad wildcard scopes can expand the blast radius of token compromise, enable privilege chaining and make audit records less meaningful. It recommends minimal initial scopes followed by targeted elevation when privileged operations are first attempted.

Least privilege also improves business defensibility.

An auditor can understand:

```text
User read employee record EMP-128
```

followed by:

```text
User granted payroll:write
User confirmed bank-account change for EMP-128
```

more easily than:

```text
User granted HR administrator access at login
```

The first design links permission, intent and action. The second only proves that broad access existed.

---

## 4. Architect’s lens

Ask these three questions:

1. **What is the minimum access required for the current step?**
   Do not grant write, delete or administrative access merely because a later workflow might need it.

2. **Does permission alone justify execution?**
   Consequential actions may require a clear confirmation screen even when the user already possesses the required role.

3. **Where is each control enforced?**
   The host presents consent and approval; the authorization system issues appropriate access; the MCP server validates permissions and business rules before executing the operation.

A prompt can tell Claude not to misuse a tool, but it cannot replace these controls.

---

## 5. Real-life example

An organisation creates an HR assistant with these capabilities:

* search the employee directory;
* read benefits information;
* view payroll status;
* change an employee’s salary-payment bank account.

Most users only ask informational questions. Bank-account changes are rare and sensitive.

### Weak architecture

When the assistant connects, it requests:

```text
employees:read
benefits:read
payroll:read
payroll:write
```

Every authenticated session possesses write access, even when the user only asks:

> “When does my health-insurance coverage begin?”

This reduces prompts but unnecessarily exposes a sensitive operation.

### Stronger architecture

The assistant initially requests:

```text
employees:read
benefits:read
```

The user can browse ordinary HR information without receiving an alarming request for payroll modification access.

Later, the user asks:

> “Change my salary-payment account to this new bank account.”

The flow becomes:

1. Claude requests the appropriate payroll tool.
2. The MCP server determines that `payroll:write` is required.
3. The server returns an insufficient-scope response identifying the required permission.
4. The client begins step-up authorization.
5. The host clearly displays the requested permission and intended operation.
6. The user approves the access and confirms the specific bank-account change.
7. The server validates the user, required scope, account ownership and organisational rules.
8. The operation is executed and recorded in the audit trail.

The MCP authorization specification supports this pattern through precise scope challenges and runtime step-up authorization. Servers should communicate the permissions needed for the current operation rather than simply requesting every available scope.

### What about a local `stdio` server?

The HTTP-oriented MCP authorization flow is not intended for `stdio`; local implementations normally obtain credentials through their environment. Least privilege still applies.

A local server should be launched with restricted filesystem, network and operating-system access. Additional access—such as permission to read a particular repository directory—should be granted explicitly rather than inheriting unrestricted access to the workstation. Official MCP guidance recommends sandboxing local servers and launching them with minimal default privileges.

The mechanism changes, but the architectural rule does not.

---

## 6. Exam-style question

This is a **practice-derived scenario**, not an authentic certification question.

A financial-services company builds an MCP server for a customer-service assistant.

The assistant must:

* read customer contact details during nearly every interaction;
* occasionally update a mailing address;
* never transfer funds;
* minimise the consequences of a stolen access token;
* provide clear evidence of user intent for data changes.

Which architecture best satisfies the requirements?

**A.** Grant one broad `customer:full-access` scope at initial login so subsequent operations do not interrupt the user.

**B.** Grant `customer:read` initially, request `customer:address-write` only when an address change is attempted, require confirmation of the specific change and enforce the permission on the server.

**C.** Grant read and write access initially but instruct Claude through the system prompt to use write operations only when necessary.

**D.** Expose the server through `stdio` so authorization and confirmation controls are no longer required.

---

## 7. Spot the clue

Three conditions drive the decision:

> **“Occasionally update”**

Write access is not needed for the normal interaction.

> **“Minimise the consequences of a stolen access token”**

The initial token should not contain unnecessary privileges.

> **“Clear evidence of user intent”**

Authorization alone is insufficient; the specific update should also be confirmed.

---

## 8. Answer reasoning

**Correct answer: B**

The design begins with the minimum permission needed for the common use case. It introduces write access only when the user attempts the protected operation.

It also separates three controls:

* the scope permits address updates;
* the confirmation records intent for this specific update;
* the server enforces the permission and business rules.

This reduces the blast radius of a compromised initial token while preserving a usable workflow. It follows MCP guidance on minimal initial scopes, precise scope challenges and incremental authorization.

### Why A is tempting but weaker

Granting broad access at login creates a smoother experience because the user sees fewer permission requests.

However, `customer:full-access` contains capabilities that most interactions do not need. It increases the impact of token theft and makes it less clear whether the user intended a particular data-changing operation.

The simplest user experience is not always the safest sufficient architecture.

### Why C is weaker

The prompt may influence Claude’s behaviour, but it does not restrict what a stolen token, compromised host or defective application can do.

Permission enforcement must occur outside the model.

### What additional fact could change the decision?

Suppose the application were used exclusively by authorised address-management specialists whose entire working session consisted of processing approved address changes.

A short-lived, narrowly defined `customer:address-write` permission might reasonably be granted at login because write access is required for the principal workflow.

The application should still:

* exclude unrelated capabilities such as fund transfers;
* confirm or display the specific record being changed;
* enforce permissions and validation on the server;
* retain a meaningful audit trail.

The correct decision may reduce authorization friction, but it should not expand the permission boundary beyond the user’s actual role.

---

## 9. One-line architect rule

> **Begin with the minimum permission, elevate only for the required operation, and never confuse access with approval.**

---

## 10. Source basis

* Current official MCP specification on user consent, tool safety and application-enforced controls.
* Current official MCP authorization specification on scope minimisation, insufficient-scope responses and step-up authorization.
* Official MCP security guidance on progressive least-privilege scopes and restricted local-server execution.
* Practice-derived exam scenario based on official architectural principles; it is not an authentic certification question.
