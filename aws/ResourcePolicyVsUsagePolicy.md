# API Gateway Resource vs. Usage Plans

Difference between Resource Policies and Usage Plans in AWS API Gateway. They both control access and usage, but they operate at different levels and serve distinct purposes.

Here's a comparison:

**API Gateway Resource Policy**

1.  **Purpose:** **Authorization** - Determines _who_ or _what_ (principals like AWS accounts, IAM users/roles, IP addresses, VPC endpoints) is allowed to _invoke_ your API Gateway API endpoints. It acts as a primary access control mechanism at the API level.
2.  **Mechanism:** Uses an **IAM-style JSON policy document** attached directly to the API Gateway API itself.
3.  **Focus:** Controlling access based on the **source** of the request (network location, AWS identity). It answers the question: "Is this entity allowed to even _talk_ to my API?"
4.  **Granularity:** Applies to the **entire API** or specific resources/methods within it, based on the policy rules. It's generally _not_ about individual end-users or client applications unless they map directly to IAM roles or specific IPs.
5.  **Common Use Cases:**
    - Creating **private APIs** accessible only from specific VPC Endpoints.
    - Allowing **cross-account access** to your API.
    - Restricting access to specific **IP address ranges**.
    - Allowing specific **AWS services** to invoke your API.
6.  **Analogy:** Think of it as the **main gatekeeper or bouncer** for the entire venue (your API). They check if you are on the guest list (e.g., coming from the right VPC, AWS account, or IP address) before letting you in at all.

**API Gateway Usage Plan**

1.  **Purpose:** **Governance, Metering, and Throttling** - Manages _how_ authorized clients can _use_ the API after they've been granted initial access. It controls consumption levels.
2.  **Mechanism:** A configuration that specifies **throttling limits** (rate limits like requests per second) and **quotas** (total requests over a period like a day or month). Usually associated with **API Keys**.
3.  **Focus:** Controlling the **rate and volume** of requests from specific clients (identified by API Keys). It answers the question: "Now that you're allowed in, _how much_ and _how fast_ can you use the service?"
4.  **Granularity:** Applied at the **API Stage** level and enforced per **API Key**. Allows you to define different tiers of service for different clients.
5.  **Common Use Cases:**
    - Protecting backend services from being overwhelmed (**throttling**).
    - Implementing different service tiers (e.g., Free, Basic, Premium) with varying limits (**quotas and throttling**).
    - **Monetizing** APIs by charging based on usage tiers.
    - Tracking API usage per customer/application (**API Keys**).
6.  **Analogy:** Think of it as different **ticket types or access levels** _inside_ the venue. Once the bouncer (Resource Policy) lets you in, your ticket type (Usage Plan + API Key) determines how many rides you can go on per hour (throttling) and how many total rides you get for the day (quota).

**Here's a table summarizing the key differences:**

| Feature          | Resource Policy                       | Usage Plan                            |
| :--------------- | :------------------------------------ | :------------------------------------ |
| **Primary Goal** | Authorization (Who can access?)       | Governance/Metering (How much/fast?)  |
| **Mechanism**    | IAM-style JSON policy                 | Throttling & Quota limits             |
| **Focus**        | Source of request (IP, VPC, AWS Acct) | Rate & Volume of requests             |
| **Association**  | Attached to the API                   | Associated with API Stages & API Keys |
| **Controls**     | Invocation permission                 | Request rate & total request count    |
| **Granularity**  | API-level or Resource/Method level    | Per API Key / Client level            |
| **Key Use Case** | Private APIs, Cross-account access    | Throttling, Quotas, Tiered Access     |

**How they work together:**

A request to your API Gateway endpoint might need to satisfy _both_ if both are configured:

1.  **Resource Policy Check:** API Gateway first checks if the source of the request (IP, VPC, etc.) is allowed by the Resource Policy. If denied, the request fails immediately (often with a `403 Forbidden` error, potentially mentioning `{"message":"Forbidden"}`).
2.  **Authentication/Authorization Check:** (e.g., IAM, Cognito, Lambda Authorizer, API Key). If the API requires authentication/authorization _in addition_ to the resource policy, this is checked next. Note that API Keys used for Usage Plans can _also_ be used for basic authorization if configured in the method settings.
3.  **Usage Plan Check:** If the request is associated with an API Key linked to a Usage Plan, API Gateway checks if the request would exceed the throttling limits or quotas defined in the plan. If it exceeds the limits, the request fails (often with a `429 Too Many Requests` error).

In essence, the Resource Policy is a broad gatekeeper, while the Usage Plan provides fine-grained control over the consumption patterns of _already permitted_ clients.
