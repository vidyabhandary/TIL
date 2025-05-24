Comparison of **CNAME** vs **NS** DNS records:

| Feature                 | **CNAME (Canonical Name)**                         | **NS (Name Server)**                                           |
| ----------------------- | -------------------------------------------------- | -------------------------------------------------------------- |
| **Purpose**             | Alias one domain name to another                   | Delegates a domain (or subdomain) to a DNS server              |
| **Use Case**            | `www.example.com → example.com`                    | Point `example.com` to DNS servers like `ns1.host.com`         |
| **Data points to**      | Another **domain name**                            | A **name server** (authoritative DNS)                          |
| **Used for**            | Simplifying DNS management and aliasing subdomains | Delegating DNS authority to specific servers                   |
| **Cannot coexist with** | Other record types (like A) for same name          | Can coexist with other NS records                              |
| **Common Use**          | For CDNs, external services (e.g., GitHub Pages)   | For switching DNS providers or setting up subdomain delegation |

### In short:

- **CNAME** is an **alias** to another domain.
- **NS** specifies the **DNS servers** authoritative for a domain.

Example:

- `www.example.com` has a **CNAME** pointing to `host.example.net`
- `example.com` has **NS** records pointing to `ns1.dnsprovider.com`, etc.
