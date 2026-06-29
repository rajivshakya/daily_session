# Terraform Modules - Complete Guide for Beginners

# What is a Terraform Module?

A Terraform Module is a container for multiple Terraform resources that are used together.

Think of a module as a reusable package of Terraform code.

Instead of writing the same infrastructure code again and again, we create it once inside a module and reuse it wherever needed.

---

# Why Do We Need Modules?

## Without Modules

Suppose you need to create Resource Groups for:

- Development
- Test
- UAT
- Production

You may write the same code multiple times:

```hcl
resource "azurerm_resource_group" "dev" {
  name     = "rg-dev"
  location = "Central India"
}

resource "azurerm_resource_group" "test" {
  name     = "rg-test"
  location = "Central India"
}

resource "azurerm_resource_group" "uat" {
  name     = "rg-uat"
  location = "Central India"
}
```

This creates:

- Duplicate code
- Difficult maintenance
- Higher chances of mistakes

---

## With Modules

Create the Resource Group code once:

```hcl
module "dev_rg" {
  source   = "./modules/resource_group"
  rg_name  = "rg-dev"
  location = "Central India"
}

module "test_rg" {
  source   = "./modules/resource_group"
  rg_name  = "rg-test"
  location = "Central India"
}

module "uat_rg" {
  source   = "./modules/resource_group"
  rg_name  = "rg-uat"
  location = "Central India"
}
```

Benefits:

- Less code
- Better maintenance
- Standardization
- Reusability

---

# What Does Reusable Mean?

Reusable means:

Write once → Use many times.

Example:

You create a Resource Group module once.

```hcl
resource "azurerm_resource_group" "rg" {
  name     = var.rg_name
  location = var.location
}
```

Now you can use it:

```hcl
module "dev" {
  source   = "./modules/resource_group"
  rg_name  = "rg-dev"
}
```

```hcl
module "test" {
  source   = "./modules/resource_group"
  rg_name  = "rg-test"
}
```

```hcl
module "prod" {
  source   = "./modules/resource_group"
  rg_name  = "rg-prod"
}
```

Same code.

Different inputs.

Different resources.

This is reusability.

---

# Module Components

A module generally contains:

## main.tf

Contains resource definitions.

## variables.tf

Contains input variables.

## outputs.tf

Contains output values.

---

# Basic Module Structure

```text
terraform-project/
│
├── main.tf
│
└── modules/
    │
    └── resource_group/
        │
        ├── main.tf
        ├── variables.tf
        └── outputs.tf
```

---

# Step-by-Step Module Example

# Child Module

## modules/resource_group/main.tf

```hcl
resource "azurerm_resource_group" "rg" {
  name     = var.rg_name
  location = var.location
}
```

---

## modules/resource_group/variables.tf

```hcl
variable "rg_name" {}

variable "location" {}
```

---

## modules/resource_group/outputs.tf

```hcl
output "rg_id" {
  value = azurerm_resource_group.rg.id
}

output "rg_name" {
  value = azurerm_resource_group.rg.name
}
```

---

# Root Module

## main.tf

```hcl
module "dev_rg" {

  source   = "./modules/resource_group"

  rg_name  = "rg-dev"
  location = "Central India"
}
```

---

# How Terraform Uses Modules

```text
Root Module
      │
      ▼
Child Module
      │
      ▼
Resources Created
      │
      ▼
Outputs Returned
```

---

# Input Variables in Modules

Variables allow modules to accept dynamic values.

Example:

```hcl
variable "rg_name" {}
```

Root module:

```hcl
module "rg" {

  source  = "./modules/resource_group"

  rg_name = "rg-dev"
}
```

Value is passed into the module.

---

# Output Values in Modules

Outputs return values from the child module.

Child module:

```hcl
output "rg_id" {
  value = azurerm_resource_group.rg.id
}
```

Root module:

```hcl
output "resource_group_id" {
  value = module.rg.rg_id
}
```

---

# Types of Modules

## 1. Local Module

Stored within the same repository.

Example:

```hcl
module "rg" {
  source = "./modules/resource_group"
}
```

Most common for learning.

---

## 2. Git Module

Stored in GitHub or Git repositories.

Example:

```hcl
module "rg" {
  source = "git::https://github.com/company/terraform-modules.git//resource_group"
}
```

Common in enterprises.

---

## 3. Terraform Registry Module

Downloaded from Terraform Registry.

Example:

```hcl
module "vnet" {
  source  = "Azure/vnet/azurerm"
  version = "4.0.0"
}
```

Common when using public modules.

---

# Common Module Structures

# Structure 1 - Beginner / Small Project

```text
project/
│
├── main.tf
├── variables.tf
├── outputs.tf
│
└── modules/
    │
    ├── rg/
    ├── vnet/
    └── vm/
```

### Use When

- Learning Terraform
- Small projects
- Lab environments

---

# Structure 2 - Environment-Based

```text
terraform/
│
├── modules/
│   ├── rg/
│   ├── vnet/
│   ├── aks/
│   └── storage/
│
├── dev/
│   ├── main.tf
│   └── terraform.tfvars
│
├── test/
│   ├── main.tf
│   └── terraform.tfvars
│
└── prod/
    ├── main.tf
    └── terraform.tfvars
```

### Use When

- Multiple environments exist.
- Dev, Test, UAT, Prod must be separated.

Very common in industry.

---

# Structure 3 - Enterprise Structure

```text
terraform/
│
├── modules/
│   ├── network/
│   ├── aks/
│   ├── storage/
│   ├── sql/
│   ├── monitoring/
│   └── security/
│
├── environments/
│   ├── dev/
│   ├── qa/
│   ├── uat/
│   └── prod/
│
├── pipelines/
│   ├── ci.yaml
│   └── cd.yaml
│
└── backend/
```

### Use When

- Large organizations
- Multiple teams
- CI/CD pipelines
- Production workloads

Most common enterprise structure.

---

# Industry Best Practices

## Create Small Focused Modules

Good:

```text
VNet Module
AKS Module
Storage Module
SQL Module
```

Bad:

```text
One module creates everything
```

---

## Use Variables

Avoid hardcoded values.

Bad:

```hcl
name = "rg-prod"
```

Good:

```hcl
name = var.rg_name
```

---

## Use Outputs

Expose important values.

Examples:

```hcl
output "vnet_id"
output "aks_id"
output "storage_id"
```

---

## Version Control Modules

Store modules in Git repositories.

Example:

```text
GitHub
Azure Repos
GitLab
Bitbucket
```

---

## Reuse Across Projects

Same VNet module can be used in:

- Project A
- Project B
- Project C

without rewriting code.

---

# Real Industry Example

Company Standard AKS Deployment:

```text
AKS Module
│
├── AKS Cluster
├── Node Pools
├── Monitoring
├── RBAC
└── Diagnostics
```

Every application team uses the same AKS module:

```hcl
module "aks" {
  source = "git::https://company.com/modules/aks.git"

  cluster_name = "aks-prod"
}
```

Benefits:

- Consistency
- Security
- Governance
- Standardization

---

# Advantages of Modules

- Code Reusability
- Reduced Duplication
- Easier Maintenance
- Standardization
- Better Collaboration
- Faster Deployments
- Easier Troubleshooting
- Better Scalability

---

# Interview Question

## What is a Terraform Module?

### Answer

A Terraform Module is a reusable container of Terraform resources. It allows infrastructure code to be written once and reused multiple times across environments and projects. Modules improve maintainability, consistency, scalability, and reduce code duplication.

---

# Quick Revision

| Concept | Purpose |
|----------|----------|
| Module | Reusable Terraform code |
| Variables | Accept inputs |
| Outputs | Return values |
| Local Module | Stored locally |
| Git Module | Stored in Git repository |
| Registry Module | Downloaded from Terraform Registry |
| Reusability | Write once, use many times |
| Industry Use | Standardized infrastructure deployment |

---

# One-Line Interview Definition

Terraform Modules are reusable building blocks that package infrastructure code into manageable, maintainable, and standardized components that can be used across multiple environments and projects.