# Azure Governance, Security & Compliance Guide for Landing Zone Preparation

## Introduction

Before deploying infrastructure, applications, or workloads in Microsoft Azure, enterprises first prepare a secure and governed Azure environment. This preparation is commonly known as:

- Azure Governance Setup
- Cloud Governance Framework
- Landing Zone Preparation
- Enterprise Azure Foundation

The purpose is to ensure:

- Security
- Compliance
- Cost Control
- Standardization
- Monitoring
- Access Control
- Operational Stability

This document explains:

- Azure Governance concepts
- Security concepts
- Compliance concepts
- Azure Policy
- Azure Initiative
- Real industry use cases
- Hands-on examples
- Step-by-step configuration

---

# 1. What is Azure Governance?

Azure Governance means controlling and standardizing the Azure environment according to company requirements.

Simple Meaning:

> Governance = Cloud environment ko controlled, organized aur secure banana.

Governance helps organizations:

- Prevent security risks
- Control cloud costs
- Enforce standards
- Maintain compliance
- Control user access
- Standardize infrastructure

---

# 2. Governance vs Security vs Compliance

| Area | Meaning | Example |
|---|---|---|
| Governance | Cloud ko manage aur standardize karna | Naming standards, tags |
| Security | Infrastructure ko secure banana | Public IP deny |
| Compliance | Rules aur regulations follow karna | Encryption mandatory |

---

# 3. Important Governance Areas in Azure

## 3.1 Resource Organization

Examples:

- Management Groups
- Subscriptions
- Resource Groups
- Shared Services Architecture

Example Structure:

```text
Management Group
 ├── Production Subscription
 ├── Non-Production Subscription
 └── Shared Services Subscription
```

---

## 3.2 Naming Convention

Example:

```text
vm-prod-web-01
rg-network-prod
vnet-prod-centralindia
```

Purpose:

- Easy management
- Standardization
- Easier troubleshooting

---

## 3.3 Tagging Strategy

Common Tags:

```text
Environment
Owner
Project
CostCenter
Application
BusinessUnit
```

Purpose:

- Cost tracking
- Ownership tracking
- Automation
- Governance

---

## 3.4 Cost Governance

Examples:

- Restrict VM sizes
- Restrict regions
- Budget alerts
- Auto shutdown

---

## 3.5 Security Governance

Examples:

- Public IP deny
- NSG restrictions
- Encryption mandatory
- Key Vault usage

---

## 3.6 Monitoring Governance

Examples:

- Diagnostic logs mandatory
- Log Analytics integration
- Alerts
- Monitoring agents

---

## 3.7 Backup & Disaster Recovery Governance

Examples:

- VM backup mandatory
- Geo-redundancy
- Recovery planning

---

## 3.8 Access Governance (RBAC)

Examples:

- Reader
- Contributor
- Owner
- Custom Roles

Purpose:

```text
WHO can do WHAT
```

---

# 4. What is Azure Policy?

Azure Policy is a governance service used to enforce organizational standards and compliance rules.

Simple Meaning:

> Azure Policy = Azure environment par rules lagana.

Examples:

- Only approved regions allowed
- Public IP not allowed
- Tags mandatory
- Approved VM sizes only
- Encryption required

---

# 5. Azure Policy Components

## 5.1 Policy Definition

Defines the rule.

Example:

```text
Allowed locations
Require tags
Allowed VM sizes
```

---

## 5.2 Policy Assignment

Defines where policy will apply.

Assignment scopes:

- Management Group
- Subscription
- Resource Group
- Resource

---

## 5.3 Initiative

Collection of multiple policies.

Example:

```text
Production Governance Initiative
```

Containing:

- Region restriction
- Mandatory tags
- Public IP deny
- VM size restriction

---

# 6. Azure Policy Effects

| Effect | Purpose |
|---|---|
| Deny | Block deployment |
| Audit | Detect violation |
| Disabled | Disable evaluation |
| Append | Add properties |
| Modify | Modify resources |
| DeployIfNotExists | Auto deploy configuration |

---

# 7. Policies Implemented During Hands-on

The following policies were implemented practically.

---

# 7.1 Allowed Locations Policy

## Purpose

Restrict resource deployment to approved regions.

Example:

```text
Central India
South India
```

---

## Industry Use Case

- Geo compliance
- Cost optimization
- Data residency control

---

## Built-in Policy Name

```text
Allowed locations
```

---

## Step-by-Step Creation

### Step 1

Open Azure Portal.

---

### Step 2

Search:

```text
Policy
```

---

### Step 3

Open:

```text
Definitions
```

---

### Step 4

Search:

```text
Allowed locations
```

---

### Step 5

Open policy and click:

```text
Assign
```

---

### Step 6

Select Scope:

```text
Subscription
```

---

### Step 7

In Parameters select:

```text
Central India
South India
```

---

### Step 8

Set Effect:

```text
Deny
```

---

### Step 9

Click:

```text
Review + Create
```

---

## Result

| Region | Result |
|---|---|
| Central India | Allowed |
| South India | Allowed |
| North Europe | Denied |

---

# 7.2 Mandatory Tags Policy

## Purpose

Ensure resources contain required tags.

---

## Industry Use Cases

- Cost management
- Ownership tracking
- Automation
- Governance

---

## Built-in Policy Name

```text
Require a tag on resources
```

---

## Example Tag

```text
Environment
```

---

## Step-by-Step Creation

### Step 1

Policy → Definitions

---

### Step 2

Search:

```text
Require a tag on resources
```

---

### Step 3

Click:

```text
Assign
```

---

### Step 4

Select Scope.

---

### Step 5

In Parameters:

```text
Tag Name = Environment
```

---

### Step 6

Effect:

```text
Deny
```

---

### Step 7

Create Policy.

---

## Result

Resources without tag:

```text
Environment
```

will be denied.

---

# 7.3 Public IP Restriction Policy

## Purpose

Prevent internet exposure.

---

## Industry Use Cases

- Security governance
- Reduce attack surface
- Production hardening

---

## Built-in Policy Name

```text
Not allowed resource types
```

---

## Resource Type Used

```text
Microsoft.Network/publicIPAddresses
```

---

## Important Note

This policy blocks:

- Public IP
- VM with Public IP
- Bastion
- NAT Gateway
- Public Load Balancer

unless excluded.

---

## Step-by-Step Creation

### Step 1

Policy → Definitions

---

### Step 2

Search:

```text
Not allowed resource types
```

---

### Step 3

Click:

```text
Assign
```

---

### Step 4

Select Scope.

---

### Step 5

In Parameters add:

```text
Microsoft.Network/publicIPAddresses
```

---

### Step 6

Effect:

```text
Deny
```

---

### Step 7

Create Policy.

---

# 7.4 Exclusion Example

## Real Industry Requirement

Some resources still need Public IP:

- Bastion
- NAT Gateway
- Firewall
- VPN Gateway

---

## Industry Best Practice

Create dedicated network Resource Group.

Example:

```text
rg-network-shared
```

Exclude this Resource Group from policy.

---

## How to Add Exclusion

### Step 1

Open Policy Assignment.

---

### Step 2

Click:

```text
Edit Assignment
```

---

### Step 3

In Exclusions select:

```text
rg-network-shared
```

---

### Step 4

Save Assignment.

---

## Result

| Resource Group | Public IP |
|---|---|
| rg-prod-apps | Denied |
| rg-network-shared | Allowed |

---

# 7.5 Allowed VM Sizes Policy

## Purpose

Restrict VM sizes.

---

## Industry Use Cases

- Cost optimization
- Standardization
- Prevent oversized VMs

---

## Built-in Policy Name

```text
Allowed virtual machine size SKUs
```

---

## Example Allowed Size

```text
Standard_B2s
```

---

## Step-by-Step Creation

### Step 1

Policy → Definitions

---

### Step 2

Search:

```text
Allowed virtual machine size SKUs
```

---

### Step 3

Assign policy.

---

### Step 4

In Parameters select:

```text
Standard_B2s
```

---

### Step 5

Effect:

```text
Deny
```

---

## Result

| VM Size | Result |
|---|---|
| Standard_B2s | Allowed |
| E64 | Denied |

---

# 8. What is Azure Initiative?

Initiative is a collection of multiple Azure Policies.

Simple Meaning:

> Initiative = Multiple policies ka package.

---

# 9. Why Use Initiatives?

Instead of assigning many policies separately, enterprises bundle policies into initiatives.

Benefits:

- Centralized governance
- Easier management
- Better compliance tracking
- Standardization

---

# 10. Example Initiative

## Initiative Name

```text
Production Governance Initiative
```

---

## Policies Included

- Allowed locations
- Mandatory tags
- Public IP deny
- VM size restriction

---

# 11. How to Create Initiative

## Step 1

Go to:

```text
Policy → Definitions
```

---

## Step 2

Click:

```text
+ Initiative definition
```

---

## Step 3

Fill:

| Field | Value |
|---|---|
| Name | Production Governance Initiative |
| Category | General |
| Scope | Subscription |

---

## Step 4

Click:

```text
Add policy definition
```

---

## Step 5

Add Policies:

```text
Allowed locations
Require a tag on resources
Not allowed resource types
Allowed virtual machine size SKUs
```

---

## Step 6

Configure Parameters:

### Allowed locations

```text
Central India
South India
```

### Required Tag

```text
Environment
```

### Public IP Restriction

```text
Microsoft.Network/publicIPAddresses
```

### VM Sizes

```text
Standard_B2s
```

---

## Step 7

Click:

```text
Create
```

---

# 12. How to Assign Initiative

## Step 1

Policy → Definitions

---

## Step 2

Search Initiative Name.

---

## Step 3

Open Initiative.

---

## Step 4

Click:

```text
Assign
```

---

## Step 5

Select:

```text
Subscription
```

---

## Step 6

Configure Parameters.

---

## Step 7

(Optional) Add Exclusions.

---

## Step 8

Click:

```text
Review + Create
```

---

# 13. Important Industry Governance Best Practices

## 13.1 Use Management Groups

For enterprise hierarchy.

---

## 13.2 Separate Production and Non-Production

Example:

```text
Prod Subscription
Dev Subscription
```

---

## 13.3 Use Dedicated Shared Network Resource Group

For:

- Bastion
- Firewall
- NAT Gateway
- VPN Gateway

---

## 13.4 Use Centralized Logging

Examples:

- Log Analytics
- Azure Monitor
- Sentinel

---

## 13.5 Use RBAC Properly

Avoid giving:

```text
Owner
```

to everyone.

---

## 13.6 Use IaC

Examples:

- Terraform
- Bicep
- ARM Templates

---

## 13.7 Enable Security Services

Examples:

- Microsoft Defender for Cloud
- Azure Security Center
- Sentinel

---

# 14. Real Enterprise Landing Zone Example

```text
Management Group
 ├── Production
 │    ├── Prod Subscription
 │    └── Shared Services
 │
 ├── Non-Production
 │    ├── Dev Subscription
 │    └── Test Subscription
 │
 └── Security
      ├── Log Analytics
      ├── Sentinel
      └── Security Tools
```

Policies Applied:

- Allowed regions
- Public IP restrictions
- Encryption mandatory
- Monitoring mandatory
- Approved VM sizes
- Mandatory tags

---

# 15. Important Interview Questions

## Q1. Difference Between Azure Policy and RBAC?

| Azure Policy | RBAC |
|---|---|
| WHAT is allowed | WHO can access |
| Governance | Permissions |
| Compliance | Authorization |

---

## Q2. What is Initiative?

Initiative is a collection of multiple Azure Policies grouped together for centralized governance and compliance management.

---

## Q3. What is Deny Effect?

Deny effect blocks resource deployment when policy conditions are violated.

---

## Q4. Why Use Tags?

Tags help with:

- Cost tracking
- Ownership
- Governance
- Automation

---

## Q5. Why Public IP Restriction is Important?

To reduce internet exposure and improve cloud security.

---

# 16. Conclusion

Azure Governance, Security, and Compliance are critical parts of enterprise cloud architecture.

Before deploying infrastructure or applications, organizations first establish:

- Governance
- Security baselines
- Compliance rules
- Access controls
- Monitoring
- Cost management

Azure Policies and Initiatives help enterprises standardize and secure their cloud environments at scale.

The hands-on implementations covered in this document represent real-world enterprise governance patterns used in production environments.

