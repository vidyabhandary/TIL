# AWS Control Tower guardrails

AWS Control Tower implements guardrails using two primary mechanisms, resulting in different _types_ based on their function and enforcement behavior.

Here are the different types of guardrails in AWS Control Tower:

**1. Based on Function (How they work):**

- **Preventive Guardrails:**

  - **Purpose:** To _prevent_ actions that would lead to policy violations. They enforce policies _before_ resources are created or modified.
  - **Mechanism:** Implemented using **AWS Organizations Service Control Policies (SCPs)**. SCPs act like allow/deny lists for AWS API actions for principals (users/roles) within affected accounts or Organizational Units (OUs).
  - **Effect:** If a user tries to perform an action forbidden by a preventive guardrail (SCP), the action simply fails with an access denied error.
  - **Example:** A guardrail preventing users from disabling AWS CloudTrail logging, deleting Control Tower essential resources, or launching resources outside of approved regions.

- **Detective Guardrails:**
  - **Purpose:** To _detect_ non-compliance of resources _after_ they have been created or modified. They monitor the state of your resources and alert you to policy violations.
  - **Mechanism:** Implemented using **AWS Config Rules**. These rules continuously evaluate the configuration of your AWS resources against desired settings.
  - **Effect:** When a resource is found to be non-compliant (e.g., an S3 bucket is publicly accessible when it shouldn't be), the AWS Config rule flags it as non-compliant, and this status is visible in Control Tower and AWS Config dashboards. It doesn't stop the resource from being created, but it raises an alert for remediation.
  - **Example:** A guardrail detecting if MFA is enabled for the root user, checking if S3 buckets have public read/write access disabled, verifying if EBS volumes are encrypted.

**2. Based on Enforcement Behavior (How strongly they are applied):**

- **Mandatory Guardrails:**

  - **Purpose:** Essential for establishing and maintaining the baseline security and governance provided by Control Tower. They enforce AWS best practices critical for managing a multi-account environment.
  - **Application:** Automatically enabled and enforced by Control Tower on the relevant OUs (e.g., the Security OU).
  - **Can you disable them?** No, these cannot be disabled by customers.
  - **Implementation:** Can be either Preventive (using SCPs) or Detective (using Config Rules).
  - **Example:** Preventing modification of Control Tower core accounts/roles/resources, ensuring baseline logging configurations.

- **Strongly Recommended Guardrails:**

  - **Purpose:** Based on common AWS best practices for securing workloads and operating efficiently. They significantly improve security posture.
  - **Application:** Enabled by default when you set up or update your landing zone, but you _can_ choose to disable them (though generally not advised unless you have a specific reason and understand the risks).
  - **Can you disable them?** Yes, these can be disabled on specific OUs if needed.
  - **Implementation:** Can be either Preventive (using SCPs) or Detective (using Config Rules).
  - **Example:** Disallowing public read access for S3 buckets (Detective), requiring MFA for the root user (Detective), notifying if encryption is not enabled on EBS volumes (Detective).

- **Elective Guardrails:**
  - **Purpose:** Optional controls that you can choose to enable to manage specific compliance requirements or address risks beyond the mandatory/strongly recommended sets.
  - **Application:** Disabled by default. You must explicitly enable them on the OUs where you want them applied.
  - **Can you disable them?** Yes, they are disabled by default and can be enabled/disabled as needed.
  - **Implementation:** Can be either Preventive (using SCPs) or Detective (using Config Rules).
  - **Example:** Checking for specific resource tags, enforcing encryption on specific database types, restricting specific instance types.

**In Summary:**

| Guardrail Type           | Function | Mechanism        | Enforcement     | Default State | Can Disable? |
| :----------------------- | :------- | :--------------- | :-------------- | :------------ | :----------- |
| **Preventive**           | Prevents | SCPs             | Varies          | Varies        | Varies       |
| **Detective**            | Detects  | AWS Config Rules | Varies          | Varies        | Varies       |
| **Mandatory**            | Varies   | SCPs / Config    | **Mandatory**   | **Enabled**   | **No**       |
| **Strongly Recommended** | Varies   | SCPs / Config    | **Recommended** | **Enabled**   | **Yes**      |
| **Elective**             | Varies   | SCPs / Config    | **Optional**    | **Disabled**  | **Yes**      |

Control Tower uses this combination of functional types (Preventive/Detective) and enforcement levels (Mandatory/Strongly Recommended/Elective) to provide a flexible yet robust governance framework for your multi-account AWS environment.
