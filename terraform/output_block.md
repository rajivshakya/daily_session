# Terraform Output Block

# Overview

An **Output Block** in Terraform is used to display values after infrastructure has been created or updated.

Outputs help us retrieve important information from Terraform resources and make that information available for:

- Users
- Other Terraform modules
- CI/CD pipelines
- Automation scripts
- Remote state consumers

Think of outputs as the **return values of Terraform**.

Just like a function returns a value, Terraform outputs return resource information.

---

# Why Do We Need Output Blocks?

Suppose Terraform creates a Resource Group:

```hcl
resource "azurerm_resource_group" "rg" {
  name     = "rg-dev"
  location = "Central India"
}
```

After deployment, you may want to know:

- Resource Group Name
- Resource Group ID
- Resource Location

Instead of manually checking Azure Portal, Terraform can display these values automatically.

---

# Basic Syntax

```hcl
output "output_name" {
  value = resource.attribute
}
```

Example:

```hcl
output "resource_group_name" {
  value = azurerm_resource_group.rg.name
}
```

---

# Example 1: Output Resource Group Name

## Resource

```hcl
resource "azurerm_resource_group" "rg" {
  name     = "rg-dev"
  location = "Central India"
}
```

## Output

```hcl
output "resource_group_name" {
  value = azurerm_resource_group.rg.name
}
```

## Result

After:

```bash
terraform apply
```

Terraform displays:

```text
Outputs:

resource_group_name = "rg-dev"
```

---

# Example 2: Output Resource Group ID

```hcl
output "resource_group_id" {
  value = azurerm_resource_group.rg.id
}
```

Result:

```text
Outputs:

resource_group_id = "/subscriptions/xxxx/resourceGroups/rg-dev"
```

---

# Example 3: Multiple Outputs

```hcl
output "resource_group_name" {
  value = azurerm_resource_group.rg.name
}

output "resource_group_location" {
  value = azurerm_resource_group.rg.location
}

output "resource_group_id" {
  value = azurerm_resource_group.rg.id
}
```

Result:

```text
Outputs:

resource_group_name     = "rg-dev"
resource_group_location = "Central India"
resource_group_id       = "/subscriptions/xxxx/resourceGroups/rg-dev"
```

---

# Common Resource Attributes Used in Outputs

## Virtual Machine

```hcl
output "vm_name" {
  value = azurerm_linux_virtual_machine.vm.name
}
```

```hcl
output "vm_private_ip" {
  value = azurerm_linux_virtual_machine.vm.private_ip_address
}
```

---

## Storage Account

```hcl
output "storage_account_name" {
  value = azurerm_storage_account.stg.name
}
```

---

## AKS Cluster

```hcl
output "aks_cluster_name" {
  value = azurerm_kubernetes_cluster.aks.name
}
```

---

## Public IP

```hcl
output "public_ip" {
  value = azurerm_public_ip.pip.ip_address
}
```

---

# Output Blocks in Modules

One of the most important uses of outputs is module communication.

---

# Child Module

## modules/rg/main.tf

```hcl
resource "azurerm_resource_group" "rg" {
  name     = var.rg_name
  location = var.location
}
```

## modules/rg/outputs.tf

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

```hcl
module "rg_module" {
  source   = "./modules/rg"
  rg_name  = "rg-dev"
  location = "Central India"
}
```

Access module outputs:

```hcl
output "resource_group_id" {
  value = module.rg_module.rg_id
}
```

Result:

```text
Outputs:

resource_group_id = "/subscriptions/xxxx/resourceGroups/rg-dev"
```

---

# Why Are Outputs Important in Modules?

Without outputs:

```text
Child Module
    ↓
Resource Created
    ↓
No Information Shared
```

With outputs:

```text
Child Module
    ↓
Output Value
    ↓
Root Module
    ↓
Other Modules
```

Outputs allow modules to share data.

---

# Passing Data Between Modules

## Network Module

Creates a VNet.

```hcl
output "vnet_id" {
  value = azurerm_virtual_network.vnet.id
}
```

## AKS Module

Uses the VNet ID.

```hcl
module "aks" {
  source  = "./modules/aks"
  vnet_id = module.network.vnet_id
}
```

This is one of the most common real-world use cases.

---

# Sensitive Outputs

Sometimes outputs contain secrets.

Examples:

- Passwords
- Connection Strings
- Access Keys

Use:

```hcl
output "storage_key" {
  value     = azurerm_storage_account.stg.primary_access_key
  sensitive = true
}
```

Terraform hides the value:

```text
storage_key = <sensitive>
```

---

# Output Description

You can document outputs.

```hcl
output "resource_group_name" {
  description = "Name of the Resource Group"
  value       = azurerm_resource_group.rg.name
}
```

This improves readability.

---

# Access Outputs After Deployment

## Show All Outputs

```bash
terraform output
```

Example:

```text
resource_group_name = "rg-dev"
resource_group_id   = "/subscriptions/xxxx/resourceGroups/rg-dev"
```

---

## Show Specific Output

```bash
terraform output resource_group_name
```

Result:

```text
rg-dev
```

---

## JSON Format

```bash
terraform output -json
```

Useful for automation and CI/CD pipelines.

---

# Real-World CI/CD Example

Pipeline creates infrastructure:

```bash
terraform apply
```

Terraform outputs:

```text
aks_cluster_name
aks_cluster_id
resource_group_name
```

Next pipeline stage consumes these outputs.

Example:

```bash
terraform output -json > outputs.json
```

The deployment pipeline reads the values and performs further actions.

---

# What Can We Achieve Using Output Blocks?

## Infrastructure Visibility

Display important resource information after deployment.

---

## Module Communication

Share values from child modules to root modules.

---

## Inter-Module Dependency

Pass values from one module to another.

Example:

```text
Network Module
      ↓
VNet ID
      ↓
AKS Module
```

---

## CI/CD Integration

Provide resource details to pipelines.

---

## Automation

Allow scripts to consume infrastructure information.

---

## Remote State Sharing

Other Terraform configurations can consume outputs from remote state.

---

# Interview Question

## What is an Output Block in Terraform?

### Answer

An Output Block is used to expose resource attributes after infrastructure deployment. It helps display useful information, share data between modules, integrate with CI/CD pipelines, and enable communication between different Terraform configurations.

---

# Quick Revision Table

| Feature | Purpose |
|----------|----------|
| value | Defines what to return |
| description | Documents the output |
| sensitive | Hides sensitive values |
| module outputs | Share data from child modules |
| terraform output | Displays output values |
| terraform output -json | Provides machine-readable output |

---

# One-Line Interview Definition

Terraform Output Blocks are used to expose resource information, share values between modules, and provide deployment data to users, automation tools, and CI/CD pipelines.

# Common Uses of Terraform Output Blocks

## 1. Display Resource Information

Used to display important resource details after deployment.

Examples:

- Resource Group Name
- Resource Group ID
- VM Public IP
- Storage Account Name
- AKS Cluster Name

Example:

```hcl
output "vm_public_ip" {
  value = azurerm_public_ip.vm_pip.ip_address
}
```

---

## 2. Share Data from Child Module to Root Module

Outputs are commonly used to expose values created inside a child module.

Example:

```hcl
output "rg_id" {
  value = azurerm_resource_group.rg.id
}
```

Accessed in root module as:

```hcl
module.rg_module.rg_id
```

---

## 3. Pass Values Between Modules

Used when one module needs information created by another module.

Example:

```text
Network Module
      ↓
    VNet ID
      ↓
AKS Module
```

```hcl
vnet_id = module.network.vnet_id
```

---

## 4. Provide Data to CI/CD Pipelines

Outputs can be consumed by Azure DevOps, GitHub Actions, Jenkins, etc.

Example:

```bash
terraform output -json
```

Used to retrieve:

- Resource IDs
- Cluster Names
- Public IPs
- Connection Endpoints

---

## 5. Support Automation Scripts

Shell scripts, PowerShell scripts, and Python scripts can read Terraform outputs.

Example:

```bash
terraform output vm_public_ip
```

---

## 6. Expose Sensitive Information Securely

Used to expose secrets while hiding them from console output.

Example:

```hcl
output "storage_key" {
  value     = azurerm_storage_account.stg.primary_access_key
  sensitive = true
}
```

---

## 7. Enable Remote State Sharing

Other Terraform projects can consume outputs from a remote state file.

Example:

```text
Project A
    ↓
Outputs VNet ID
    ↓
Project B
    ↓
Uses VNet ID
```

---

## 8. Simplify Troubleshooting

Quickly verify what Terraform created without opening Azure Portal.

Examples:

- Resource IDs
- IP Addresses
- DNS Names
- Cluster Names

---

# Interview Answer (Short)

Terraform Output Blocks are mainly used to:

1. Display resource information after deployment.
2. Share values from child modules to root modules.
3. Pass values between modules.
4. Provide data to CI/CD pipelines.
5. Support automation scripts.
6. Expose sensitive information securely.
7. Share data through remote state.
8. Simplify troubleshooting and validation.