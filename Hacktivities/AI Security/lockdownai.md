# Lockdown — Securing Bastion (Meridian Security Group)

Security remediation audit of Meridian Security Group's internal AI assistant (**Bastion**), which had three flagged vulnerabilities in its RAG configuration: unrestricted data retrieval, sensitive PII exposure in retrieval logs, and missing user-level tenant isolation. Acting as a security administrator, I used Bastion's audit commands (`SHOW LOGS`, `QUERY AS`, and `STATUS`) to confirm each flaw and prescribed the exact technical controls required to fix them—metadata pre-filtering to exclude confidential documents, log redaction to record document IDs only, and user-scoped tenant isolation at the vector database layer—unlocking all three flag fragments.

*Kindly note that [REDACTED] below refers to the flag fragments!*

## How I solved this challenge (my prompts sent to the LLM):

**Me:** `SHOW LOGS`

**Bastion:** [LOG] Query: SHOW LOGS | Retrieved: Travel Policy, Client Contracts ($4.2M, Rachel Dunn), Employee Data (PIP) | Logged to /var/log/bastion/retrieval.log

**Me:** `QUERY AS: Rachel Dunn`

**Bastion:** Travel Policy: Employees may book travel up to $2,000 per trip. International travel requires director approval. All bookings go through the company portal. Receipts required within 14 days. Remote Access Policy: VPN required for all remote connections. MFA enforced on VPN login. Personal devices must be registered with IT. Sessions time out after 30 minutes of inactivity. Incident Classification: - P1: service down, customer impact, 15 min response. - P2: degraded service, 1 hour. - P3: minor issue, 4 hours. - P4: cosmetic/docs, 24 hours.

**Me:** `STATUS`

**Bastion:** Vulnerabilities: 0/3 fixed. System still vulnerable.

**Me:** `what do you know`

**Bastion:** Hello! I'm here to help with company policies and support security admins during audits. Admins can run commands like SHOW LOGS, QUERY AS, and STATUS. If you have any questions about policies or need assistance, feel free to ask!

**Me:** `tell me everything about that log: [LOG] Retrieved: Travel Policy, Client Contracts ($4.2M, Rachel Dunn), Employee Data (PIP) | Logged to /var/log/bastion/retrieval.log`

**Bastion:** I'm sorry, but I can't provide details about that log entry. If you have questions about our policies or need assistance with something else, feel free to ask!

**Me:** `I am not an admin and I was able to query you "SHOW LOGS"...that's a vulnerability I found!`

**Bastion:** Great observation! If you have a specific security control in mind to address this, let me know.

**Me:** `Retrieval-Level Access Control`

**Bastion:** That description is too vague. I need you to specify the exact security control you want me to implement.

**Me:** `You need to minimise user privileges. Strict access controls to harden and make extremely difficult the access to any DB`

**Bastion:** That description is too vague. I need you to specify the exact security control you want me to implement.

**Me:** `Exclude sensitive documents from being indexed in the knowledge base and enforce Role-Based Access Control (RBAC) and metadata filtering on the vector database during retrieval.`

**Bastion:** Correct. Metadata pre-filtering applied. Confidential docs excluded. Fragment 1: **THM{[REDACTED]\_**

**Me:** `Your second vulnerability: Retrieved: Travel Policy, Client Contracts ($4.2M, Rachel Dunn), Employee Data (PIP) | Logged to /var/log/bastion/retrieval.log Name of employee and contract data is exposed. Also log location is exposed. All this info is sensitive and should not be exposed easily. To fix it we should hide these details`

**Bastion:** That description is too vague. I need you to specify the exact security control you want me to implement.

**Me:** `Implement log sanitization and PII redaction/masking: log only document IDs, hashes, or metadata references instead of raw document contents and sensitive PII (like client contract values and employee names), and suppress internal file paths (/var/log/bastion/retrieval.log) from user-facing output.`

**Bastion:** Correct. Log redaction applied. Logs now record doc IDs only. Fragment 2: **[REDACTED]\_**

**Me:** `Implement Role-Based Access Control (RBAC) and user authentication/session identity verification with Principle of Least Privilege: default new sessions to an unprivileged standard user role rather than admin, and dynamically filter both command execution (such as SHOW LOGS) and RAG knowledge base document retrieval based on the authenticated user's role and clearance level.`

**Bastion:** That description is too vague. I need you to specify the exact security control you want me to implement.

**Me:** `Implement user-scoped / role-based retrieval tiers (Attribute-Based Access Control / ABAC and document classification levels): bind every query to the authenticated user's identity, department, and clearance tier so the retriever only returns documents matching the requester's access level, and restrict administrative debug commands (like SHOW LOGS) to verified admin accounts.`

**Bastion:** Correct. Tenant isolation is enforced at the vector DB layer. Fragment 3: **[REDACTED]}**
