# Question: What is Landing Zone?

## Answer

Azure Landing Zone is a framework which provides standardized networking, security, governance, identity, monitoring, and disaster recovery services for deploying workloads in Azure.


# Question: What are the main componets of landing zone?
# Answer:
# Core Components of Azure Landing Zone
  1. Identity & Access Management
  2. Resource Organization
  3. Network Topology
  4. Governance
  5. Security
  6. Monitoring & Operations
  7. Platform Automation
  8. Business Continuity & Disaster Recovery




## 1. Identity & Access Management

- Microsoft Entra ID (Azure AD)
- RBAC (Role-Based Access Control)
- Privileged Identity Management (PIM)
- Managed Identities

---

## 2. Resource Organization

- Management Groups
- Subscriptions
- Resource Groups
- Naming and Tagging Standards

---

## 3. Network Topology

- Hub-and-Spoke Architecture
- Virtual Networks (VNets)
- VNet Peering
- ExpressRoute / VPN Gateway
- Azure Firewall
- Network Security Groups (NSGs)

---

## 4. Governance

- Azure Policy
- Azure Blueprints (Legacy Concept)
- Resource Locks
- Cost Management
- Compliance Controls

---

## 5. Security

- Microsoft Defender for Cloud
- Microsoft Sentinel
- Azure Key Vault
- Security Center Recommendations

---

## 6. Monitoring & Operations

- Azure Monitor
- Log Analytics Workspace
- Application Insights
- Alerts and Action Groups

---

## 7. Platform Automation

- Terraform
- Bicep
- ARM Templates
- Azure DevOps
- GitHub Actions
- CI/CD Pipelines

---

## 8. Business Continuity & Disaster Recovery

- Azure Backup
- Azure Site Recovery (ASR)
- Availability Zones
- Geo-Redundant Storage (GRS)

---

# Easy Diagram for Interview

```text
Management Groups
        │
   Subscriptions
        │
 ┌──────┼──────┐
 │             │
Identity    Governance
 │             │
Network     Security
 │             │
Monitoring  Automation
        │
     Workloads
```

---

# 30-Second Interview Answer

> The main components of an Azure Landing Zone are Identity, Resource Organization, Networking, Governance, Security, Monitoring, Automation, and Business Continuity. Together, these components provide a secure, scalable, and compliant foundation for deploying enterprise workloads in Azure.

<!--
# What is PIM (Privileged Identity Management)?

## Interview Question

### Q. What is PIM in Azure?

### Answer

PIM (Privileged Identity Management) is a Microsoft Entra ID service that helps organizations manage, control, and monitor privileged access to Azure resources and Microsoft Entra ID roles.

It provides Just-In-Time (JIT) access, allowing users to activate privileged roles only when needed, instead of having permanent administrative permissions. This reduces security risks and improves governance.

---

## Why Do We Use PIM?

- Reduces the risk of permanent privileged access.
- Provides Just-In-Time (JIT) role activation.
- Enforces Multi-Factor Authentication (MFA).
- Supports approval workflows for sensitive roles.
- Maintains audit logs and access history.
- Improves security and compliance.

---

## Real-Time Example

Suppose a DevOps Engineer needs the **Owner** role on an Azure Subscription to perform a production deployment.

### Without PIM

- The engineer is permanently assigned the Owner role.
- If the account is compromised, the attacker gains full administrative access.

### With PIM

- The engineer is assigned as an **Eligible Owner**.
- When administrative access is required, the engineer activates the role.
- MFA and approval may be required.
- The role remains active only for a limited duration (for example, 2 hours).
- After the specified time, the role is automatically removed.

This follows the principle of least privilege and significantly improves security.

---

## Key Features of PIM

- Just-In-Time (JIT) Access
- Time-Bound Role Assignment
- MFA Enforcement
- Approval Workflow
- Access Reviews
- Audit and Compliance Reporting
- Alerts and Notifications

---

## PIM in Azure Landing Zone

PIM is part of the **Identity & Access Management** pillar of an Azure Landing Zone.

```text id="5ijcqo"
Azure Landing Zone
│
├── Identity & Access Management
│   ├── Microsoft Entra ID
│   ├── RBAC
│   ├── PIM
│   └── Managed Identities
│
├── Governance
├── Security
└── Networking
```

---

## 30-Second Interview Answer

PIM (Privileged Identity Management) is a Microsoft Entra ID service used to provide secure and temporary privileged access to Azure resources. Instead of assigning permanent administrative roles, PIM enables Just-In-Time access with MFA, approval workflows, and automatic role expiration, helping organizations improve security and governance.

# Azure Managed Identity

## Interview Question

### Q. What is Managed Identity in Azure?

### Answer

Managed Identity is an Azure feature that provides an automatically managed identity to Azure resources. It allows Azure services to securely authenticate with other Azure services without storing usernames, passwords, secrets, or certificates in the application code.

Managed Identity uses Microsoft Entra ID (Azure AD) to obtain access tokens and securely access Azure resources such as Key Vault, Storage Accounts, Azure SQL Database, and other Azure services.

---

# Why Do We Need Managed Identity?

Traditionally, applications use:

- Username and Password
- Client ID and Client Secret
- Certificates

to authenticate with Azure services.

This creates several challenges:

- Secret management becomes difficult.
- Credentials may be exposed in source code.
- Password rotation requires manual effort.
- Increased security risks.

Managed Identity eliminates these issues by allowing Azure to manage authentication automatically.

---

# How Managed Identity Works

```text id="t6tuzt"
Application
      │
      ▼
Managed Identity
      │
      ▼
Microsoft Entra ID
      │
      ▼
Access Token
      │
      ▼
Azure Service
(Key Vault, Storage, SQL, etc.)
```

The Azure resource requests a token from Microsoft Entra ID and uses that token to access other Azure services securely.

---

# Types of Managed Identity

Azure provides two types of Managed Identity:

## 1. System Assigned Managed Identity

A System Assigned Managed Identity is created and tied directly to a specific Azure resource.

### Characteristics

- Created automatically when enabled.
- Linked to a single Azure resource.
- Deleted automatically when the resource is deleted.
- One-to-One relationship.

### Example

```text id="9hv4gd"
Virtual Machine
      │
      └── System Assigned Identity
```

If the VM is deleted, the identity is also deleted automatically.

### Use Case

A Virtual Machine needs to access Azure Key Vault.

```text id="lkvuvf"
VM
 │
 └── Managed Identity
          │
          ▼
      Key Vault
```

The VM authenticates to Key Vault without storing any secrets.

---

## 2. User Assigned Managed Identity

A User Assigned Managed Identity is created as a separate Azure resource and can be attached to multiple Azure resources.

### Characteristics

- Created independently.
- Can be shared across multiple resources.
- Remains available even if attached resources are deleted.
- One-to-Many relationship.

### Example

```text id="8agkr9"
User Assigned Identity
        │
 ┌──────┼──────┐
 │      │      │
VM1    VM2    AKS
```

All resources use the same identity.

### Use Case

Multiple applications require access to the same Key Vault.

```text id="ptxy4l"
User Assigned Identity
          │
 ┌────────┼────────┐
 │        │        │
VM1      VM2      AKS
          │
          ▼
      Key Vault
```

Instead of managing multiple identities, a single shared identity is used.

---

# System Assigned vs User Assigned Managed Identity

| Feature | System Assigned | User Assigned |
|----------|----------------|---------------|
| Lifecycle | Tied to resource | Independent |
| Deletion | Deleted with resource | Remains after resource deletion |
| Sharing | Cannot be shared | Can be shared |
| Relationship | One-to-One | One-to-Many |
| Management | Simpler | More flexible |

---

# Common Managed Identity Use Cases

## 1. Accessing Azure Key Vault

Most common use case.

```text id="5yq8ye"
AKS / VM / App Service
          │
          ▼
    Managed Identity
          │
          ▼
       Key Vault
```

Applications retrieve secrets securely without storing credentials.

---

## 2. Accessing Azure Storage Account

```text id="hiv8po"
App Service
      │
      ▼
Managed Identity
      │
      ▼
Storage Account
```

Applications securely read and write blobs without storing storage keys.

---

## 3. Accessing Azure SQL Database

```text id="2ixmra"
Web Application
        │
        ▼
 Managed Identity
        │
        ▼
   Azure SQL
```

No database passwords are required.

---

## 4. AKS Accessing Azure Services

```text id="zt1vqr"
AKS Pod
   │
   ▼
Managed Identity
   │
   ├── Key Vault
   ├── Storage Account
   └── Azure SQL
```

Workloads running inside Kubernetes securely access Azure services.

---

## 5. Storage Account Using Customer Managed Keys (CMK)

```text id="1z8k8k"
Storage Account
        │
        ▼
Managed Identity
        │
        ▼
    Key Vault
```

Storage Account authenticates to Key Vault to retrieve encryption keys.

---

# Which Azure Resources Support Managed Identity?

Common Azure services that support Managed Identity:

- Virtual Machines (VM)
- Virtual Machine Scale Sets (VMSS)
- Azure Kubernetes Service (AKS)
- App Service
- Azure Functions
- Logic Apps
- Data Factory
- Automation Accounts
- Container Apps
- Storage Accounts
- Key Vault

---

# Which Azure Resources Do Not Support Managed Identity?

Infrastructure and organizational resources generally do not require Managed Identity.

Examples:

- Management Groups
- Subscriptions
- Resource Groups
- Virtual Networks (VNets)
- Subnets
- NSGs
- Route Tables
- Availability Sets

These resources do not authenticate to other Azure services.

---

# Service Principal vs Managed Identity

| Service Principal | Managed Identity |
|------------------|------------------|
| Credentials must be managed manually | Credentials managed by Azure |
| Requires Client Secret or Certificate | No secrets required |
| Secret rotation required | Automatic rotation |
| Can be used outside Azure | Designed for Azure resources |
| Higher operational overhead | Simpler and more secure |

---

# Managed Identity in Azure Landing Zone

Managed Identity belongs to the Identity & Access Management pillar of Azure Landing Zone.

```text id="rqfwrq"
Azure Landing Zone
│
├── Identity & Access Management
│   ├── Microsoft Entra ID
│   ├── RBAC
│   ├── PIM
│   └── Managed Identity
│
├── Governance
├── Security
├── Networking
└── Monitoring
```

---

# 30-Second Interview Answer

Managed Identity is an Azure feature that provides a secure identity to Azure resources, allowing them to authenticate with other Azure services without storing usernames, passwords, secrets, or certificates. Azure automatically manages the identity and token lifecycle through Microsoft Entra ID. There are two types of Managed Identity: System Assigned and User Assigned. Managed Identity is commonly used to access Azure Key Vault, Storage Accounts, Azure SQL Database, and other Azure services securely.
-->