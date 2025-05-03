S3 Gateway Endpoints and S3 Interface Endpoints

Table summarizing the key differences between S3 Gateway Endpoints and S3 Interface Endpoints:

**S3 Gateway Endpoint vs. S3 Interface Endpoint Comparison**

| Feature                | S3 Gateway Endpoint                                                                                                      | S3 Interface Endpoint (AWS PrivateLink)                                                                                                    |
| :--------------------- | :----------------------------------------------------------------------------------------------------------------------- | :----------------------------------------------------------------------------------------------------------------------------------------- |
| **Connectivity Type**  | Uses route table entries and AWS Prefix Lists.                                                                           | Uses Elastic Network Interfaces (ENIs) within your VPC subnets.                                                                            |
| **Resource in VPC**    | A logical gateway associated with the VPC route table.                                                                   | One or more ENIs with private IP addresses in your subnets.                                                                                |
| **Routing Method**     | Modifies VPC Route Tables to direct S3 traffic to the endpoint.                                                          | Traffic directed via DNS resolution to the private IPs of the ENIs. No route table changes needed for the endpoint itself.                 |
| **DNS Resolution**     | Uses public S3 DNS names. Resolution is public, but routing is private via the endpoint.                                 | Can use Private DNS to resolve public S3 DNS names to private ENI IPs _within the VPC_. Also provides endpoint-specific private DNS names. |
| **Accessibility**      | **Only** accessible from resources within the **same VPC** where the endpoint is created.                                | Accessible from within the VPC, peered VPCs, VPN/Direct Connect (on-premises networks).                                                    |
| **Security Control**   | Controlled via Endpoint Policies and S3 Bucket Policies. **Cannot** use Security Groups directly on the endpoint itself. | Controlled via Security Groups attached to the ENIs, Endpoint Policies, and S3 Bucket Policies. Offers finer network-level control.        |
| **On-Premises Access** | **No.** Cannot be accessed directly from on-premises networks via VPN or Direct Connect.                                 | **Yes.** Can be accessed from on-premises networks via VPN or Direct Connect (requires appropriate DNS resolution).                        |
| **Cost**               | **No** hourly charge for the endpoint itself. Data transfer _within the same region_ is typically free.                  | **Incurs costs:** Hourly charge per Availability Zone the endpoint is in (per ENI), plus a per-GB data processing charge.                  |
| **Supported Services** | Only **S3** and **DynamoDB**.                                                                                            | Supports **S3** and a **wide range of other AWS services** (SQS, Kinesis, API Gateway, etc.).                                              |
| **Setup Complexity**   | Generally simpler; primarily involves route table updates.                                                               | Slightly more complex; involves selecting subnets, configuring Security Groups, and managing DNS (if using Private DNS).                   |
| **IP Address Type**    | No private IP addresses assigned within your VPC.                                                                        | Assigns private IP addresses from your subnet CIDR ranges to the ENIs.                                                                     |

**In Summary:**

- **Gateway Endpoints** are simpler, free, but limited to S3/DynamoDB and only accessible from within their own VPC. They work by modifying route tables.
- **Interface Endpoints** are more flexible, support many services, can be accessed from on-premises/peered VPCs, and offer finer security control via Security Groups, but they come with hourly and data processing costs. They work using ENIs and private IPs within your VPC.
