# AWS RAM (Resource Access Manager) is a service that allows you to share your resources across AWS accounts and within your AWS Organization. It enables you to manage and control access to your resources in a more flexible and efficient way, especially in multi-account environments.

# AWS RAM is particularly useful in organizations that have adopted a multi-account strategy, as it allows for better resource management, cost allocation, and security controls.

AWS Resource Access Manager (RAM) allows you to securely share specific types of AWS resources that you own with other AWS accounts, within your AWS Organization, or with specific IAM roles and users within your account.

Here is a list of commonly shareable AWS resources via RAM (Note: AWS is constantly adding services, so this list is representative but always check the official AWS documentation for the absolute latest):

**Networking:**

- **Subnets:** Share VPC subnets, allowing other accounts to launch resources (like EC2 instances) directly into your shared VPC subnets.
- **Transit Gateways:** Share your Transit Gateway to allow VPCs and VPNs from other accounts to attach to it, enabling centralized network connectivity.
- **Transit Gateway Attachments:** Share specific attachments (like VPC or VPN attachments).
- **Prefix Lists:** Share managed prefix lists for use in security groups and route tables across accounts.
- **Route 53 Resolver Rules:** Share forwarding rules for DNS queries between VPCs.
- **Network Firewalls:** Share AWS Network Firewall policies and rule groups.
- **VPC Lattice:** Share VPC Lattice service networks and services.

**Compute & Licensing:**

- **License Manager Configurations:** Share license configurations managed by AWS License Manager.
- **EC2 Dedicated Hosts:** Allow other accounts to launch instances onto your Dedicated Hosts.
- **EC2 Capacity Reservations:** Share reserved capacity (specific AZ, instance type) with other accounts.

**Storage & Data (Often metadata or access control):**

- **AWS Glue Data Catalogs, Databases, and Tables:** Share metadata stored in Glue for cross-account data lake access (often used with Lake Formation).
- **AWS Lake Formation Resources:** While Lake Formation manages fine-grained permissions, RAM can be used to share the underlying Glue resources that Lake Formation governs.
- **Amazon Redshift Datashares:** RAM facilitates the sharing _invitation_ process for Redshift data sharing across accounts.

**Management & Governance:**

- **AWS Service Catalog Portfolios:** Share curated portfolios of deployable IT services.
- **AWS Config Rules:** Share custom Config rules for consistent compliance checking.
- **AWS Systems Manager Incident Manager Contacts and Response Plans:** Share incident management configurations.
- **AWS Systems Manager Parameter Store Parameters:** Share specific parameters securely.
- **Resource Groups:** Share Resource Groups created using the AWS Resource Groups service.
- **AWS Cloud Map Namespaces:** Share service discovery namespaces.

**Security:**

- **AWS Key Management Service (KMS) Customer Managed Keys (CMKs):** Share your encryption keys for use by services in other accounts (e.g., encrypting EBS volumes launched from a shared AMI).

**Machine Learning:**

- **Amazon SageMaker Feature Groups:** Share features for ML models.
- **Amazon SageMaker Pipelines:** Share ML workflow definitions.
- **Amazon SageMaker Model Cards:** Share model documentation.
- **Amazon SageMaker Projects:** Share MLOps project templates.

**Developer & CI/CD:**

- **AWS CodeBuild Projects & Report Groups:** Share build configurations and results.
- **AWS CodePipeline Pipelines:** Share pipeline definitions.
- **AWS CodeArtifact Domains & Repositories:** Share package management resources.

**Monitoring:**

- **Amazon CloudWatch Metrics Streams:** Share real-time metrics data.
- **Amazon CloudWatch Alarms:** Share alarms (note: sharing alarms is relatively new).

**Key Considerations:**

- **Not all resources are shareable:** You cannot share everything (e.g., you don't typically share raw EC2 instances or S3 buckets directly via RAM - those use other mechanisms like AMIs or bucket policies).
- **Principal Types:** Resources can be shared with entire AWS Organizations, specific Organizational Units (OUs), specific AWS Account IDs, or IAM users/roles within your own account.
- **Permissions:** Sharing a resource via RAM grants access _to_ the resource, but the consuming principal (the account/user/role receiving the share) still needs appropriate IAM permissions within their _own_ account to _use_ that resource.

Using AWS RAM is central to building hub-and-spoke architectures and managing resources efficiently across multiple accounts in AWS.
