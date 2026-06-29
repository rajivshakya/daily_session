# Terraform Lifecycle Block

## Overview

The **lifecycle block** in Terraform is used to control how Terraform creates, updates, and destroys resources.

By default, Terraform follows its own behavior for resource management. The lifecycle block allows us to customize that behavior to protect resources, avoid downtime, or ignore specific changes.

---

# Why Do We Use the Lifecycle Block?

Suppose we have a production Resource Group:

```hcl
resource "azurerm_resource_group" "prod_rg" {
  name     = "rg-prod"
  location = "Central India"
}
```

If someone accidentally removes the resource from the code or runs:

```bash
terraform destroy
```

Terraform will delete the Resource Group.

For critical production resources, this can be dangerous.

The lifecycle block helps prevent such situations.

---

# Syntax

```hcl
resource "azurerm_resource_group" "rg" {
  name     = "rg-dev"
  location = "Central India"

  lifecycle {
    # lifecycle options
  }
}
```

---

# Lifecycle Options

The most commonly used lifecycle options are:

```hcl
lifecycle {
  create_before_destroy = true
  prevent_destroy       = true
  ignore_changes        = []
  replace_triggered_by  = []
}
```

---

# 1. prevent_destroy

## Purpose

Prevents a resource from being accidentally deleted.

### Example

```hcl
resource "azurerm_resource_group" "rg" {
  name     = "rg-prod"
  location = "Central India"

  lifecycle {
    prevent_destroy = true
  }
}
```

### Result

If someone runs:

```bash
terraform destroy
```

Terraform will return an error:

```text
Error: Instance cannot be destroyed
```

### When to Use

Production resources such as:

- Resource Groups
- Databases
- Storage Accounts
- Key Vaults
- Production AKS Clusters

### Interview Answer

> The `prevent_destroy` argument protects critical resources from accidental deletion by Terraform.

---

# 2. create_before_destroy

## Purpose

Creates the new resource first and destroys the old resource afterward.

### Problem

Suppose a resource attribute requires replacement.

Terraform's default behavior is:

```text
Destroy old resource
Create new resource
```

This can cause downtime.

### Example

```hcl
resource "azurerm_resource_group" "rg" {
  name     = "rg-dev"
  location = "Central India"

  lifecycle {
    create_before_destroy = true
  }
}
```

### Result

Terraform performs:

```text
Create new resource
Destroy old resource
```

### Benefits

- Reduces downtime
- Safer deployments
- Better availability

### When to Use

Commonly used with:

- Virtual Machines
- VM Scale Sets
- App Services
- Load Balancers
- Networking Components

### Interview Answer

> `create_before_destroy` minimizes downtime by creating the replacement resource before removing the existing one.

---

# 3. ignore_changes

## Purpose

Tells Terraform to ignore changes made to specific attributes.

### Problem

Terraform manages tags:

```hcl
tags = {
  Environment = "Dev"
}
```

Later, an administrator adds a tag manually through the Azure Portal:

```text
Owner = Rajiv
```

Terraform will detect a drift and attempt to remove that tag during the next apply.

### Solution

```hcl
resource "azurerm_resource_group" "rg" {
  name     = "rg-dev"
  location = "Central India"

  tags = {
    Environment = "Dev"
  }

  lifecycle {
    ignore_changes = [
      tags
    ]
  }
}
```

### Result

Terraform ignores changes to tags and does not attempt to modify them.

### When to Use

Common examples:

- Tags
- Metadata
- Monitoring settings
- Policy-managed attributes
- Attributes managed by another team

### Ignore Specific Attributes

```hcl
lifecycle {
  ignore_changes = [
    tags,
    location
  ]
}
```

### Interview Answer

> `ignore_changes` prevents Terraform from managing specific attributes that may be modified outside Terraform.

---

# 4. replace_triggered_by

## Purpose

Forces resource replacement when another resource changes.

### Example

```hcl
resource "azurerm_virtual_machine" "vm" {

  lifecycle {
    replace_triggered_by = [
      azurerm_resource_group.rg
    ]
  }
}
```

### Result

If the Resource Group is replaced, Terraform will automatically replace the VM as well.

### When to Use

Useful when resources have dependencies and must be recreated together.

Examples:

- Virtual Machines
- AKS Clusters
- App Services
- VM Scale Sets

### Interview Answer

> `replace_triggered_by` forces a resource replacement whenever a specified dependent resource changes.

---

# Real-World Production Example

```hcl
resource "azurerm_resource_group" "prod_rg" {

  name     = "rg-prod"
  location = "Central India"

  tags = {
    Environment = "Production"
  }

  lifecycle {

    prevent_destroy = true

    ignore_changes = [
      tags
    ]
  }
}
```

### What This Configuration Does

- Prevents accidental deletion of the Resource Group.
- Ignores tag modifications made outside Terraform.
- Improves production safety and governance.

---

# Interview Definition

## What is the Terraform Lifecycle Block?

> The lifecycle block is used to customize Terraform's behavior during resource creation, modification, and destruction. It helps prevent accidental deletions, reduce downtime during replacements, ignore external changes, and control resource dependencies.

---

# Quick Revision Table

| Option | Purpose | Common Use Cases |
|----------|----------|----------|
| prevent_destroy | Prevent resource deletion | Resource Groups, Databases, Storage Accounts |
| create_before_destroy | Create replacement before deleting original resource | VMs, App Services, Load Balancers |
| ignore_changes | Ignore changes to selected attributes | Tags, Metadata, Monitoring Settings |
| replace_triggered_by | Replace resource when another resource changes | VMs, AKS, VMSS, App Services |

---

# Most Commonly Used in Industry

The two lifecycle options most commonly used in production environments are:

- **prevent_destroy** – to protect critical resources.
- **ignore_changes** – to handle changes made outside Terraform and avoid unnecessary drift corrections.

---

# Key Interview Points

1. Lifecycle block customizes resource behavior.
2. It is defined inside a resource block.
3. Common options:
   - prevent_destroy
   - create_before_destroy
   - ignore_changes
   - replace_triggered_by
4. Frequently used in production environments.
5. Helps improve safety, governance, and deployment reliability.

##########################################
# Terraform Lifecycle Block - Scenario Based Interview Questions

## Question 1

### Scenario

You have configured a production Resource Group with the following lifecycle block:

```hcl
resource "azurerm_resource_group" "prod_rg" {
  name     = "rg-prod"
  location = "Central India"

  lifecycle {
    prevent_destroy = true
  }
}
```

A team member accidentally runs:

```bash
terraform destroy
```

### Question

What will happen and why?

### Answer

Terraform will fail and the Resource Group will not be deleted.

The `prevent_destroy` argument instructs Terraform to block any destroy operation against the resource.

Terraform will return an error similar to:

```text
Error: Instance cannot be destroyed

Resource has lifecycle.prevent_destroy set
```

### Explanation

The purpose of `prevent_destroy` is to protect critical resources from accidental deletion.

### Real-World Use Cases

- Production Resource Groups
- Databases
- Storage Accounts
- Key Vaults
- AKS Clusters

### Interview Tip

`prevent_destroy` protects resources from deletion through Terraform only. It does not protect resources from deletion through Azure Portal or Azure CLI.

---

# Question 2

## Scenario

You have the following configuration:

```hcl
resource "azurerm_resource_group" "prod_rg" {
  name     = "rg-prod"
  location = "Central India"

  lifecycle {
    prevent_destroy = true
  }
}
```

Later, someone completely removes the Resource Group block from the Terraform configuration and runs:

```bash
terraform apply
```

## Question

Will the Resource Group be deleted?

## Answer

No.

Terraform will detect that the resource exists in the state but no longer exists in the configuration.

Terraform will therefore plan to destroy the resource.

However, before executing the destroy operation, Terraform checks the lifecycle configuration stored in the state and finds:

```hcl
prevent_destroy = true
```

Terraform then stops the operation and returns an error.

```text
Error: Instance cannot be destroyed

Resource has lifecycle.prevent_destroy set
```

## Explanation

Even if the resource block is removed from the configuration, Terraform still knows about the resource because it exists in the state file.

The lifecycle protection remains active and prevents the deletion.

## Interview Tip

This is one of the most commonly asked lifecycle interview scenarios.

---

# Question 3

## Scenario

A production Resource Group contains the following configuration:

```hcl
resource "azurerm_resource_group" "prod_rg" {
  name     = "rg-prod"
  location = "Central India"

  lifecycle {
    prevent_destroy = true
  }
}
```

A cloud administrator deletes the Resource Group directly from the Azure Portal.

## Question

Will Terraform prevent the deletion?

## Answer

No.

Terraform cannot stop actions performed directly through Azure Portal, Azure CLI, PowerShell, or ARM APIs.

The Resource Group will be deleted successfully.

## Explanation

The lifecycle block only affects Terraform operations.

It does not create any protection within Azure itself.

### Terraform Protection

```hcl
lifecycle {
  prevent_destroy = true
}
```

Protects against:

```bash
terraform destroy
```

### Azure Protection

To prevent deletion from Azure Portal and Azure CLI, use an Azure Resource Lock:

```hcl
resource "azurerm_management_lock" "rg_lock" {
  name       = "rg-lock"
  scope      = azurerm_resource_group.prod_rg.id
  lock_level = "CanNotDelete"
}
```

## Interview Tip

Lifecycle Block = Terraform Protection

Azure Resource Lock = Azure Protection

---

# Question 4

## Scenario

A Storage Account is managed by Terraform.

```hcl
resource "azurerm_storage_account" "stg" {
  name                     = "prodstorage001"
  resource_group_name      = "rg-prod"
  location                 = "Central India"
  account_tier             = "Standard"
  account_replication_type = "LRS"

  tags = {
    Environment = "Production"
  }
}
```

An administrator adds a tag manually through Azure Portal:

```text
Owner = Rajiv
```

After that, Terraform Plan is executed.

## Question

How can you prevent Terraform from removing the manually added tag?

## Answer

Use the `ignore_changes` lifecycle argument.

```hcl
resource "azurerm_storage_account" "stg" {

  tags = {
    Environment = "Production"
  }

  lifecycle {
    ignore_changes = [
      tags
    ]
  }
}
```

## Explanation

Terraform normally tries to make the infrastructure match the configuration exactly.

Without `ignore_changes`, Terraform will detect that the tag differs from the code and attempt to remove it.

With `ignore_changes`, Terraform ignores changes made outside Terraform.

## Common Use Cases

- Tags
- Monitoring settings
- Metadata
- Policy-managed attributes

## Interview Tip

`ignore_changes` is one of the most frequently used lifecycle options in enterprise environments.

---

# Question 5

## Scenario

A Virtual Machine must be recreated whenever the associated Resource Group is recreated.

```hcl
resource "azurerm_virtual_machine" "vm" {
  ...
}
```

## Question

How can you automatically force the VM to be recreated when another resource changes?

## Answer

Use `replace_triggered_by`.

```hcl
resource "azurerm_virtual_machine" "vm" {

  lifecycle {
    replace_triggered_by = [
      azurerm_resource_group.rg
    ]
  }
}
```

## Explanation

Normally Terraform only replaces resources when their own configuration changes.

Using `replace_triggered_by` creates an explicit dependency.

If the Resource Group is replaced, Terraform will automatically replace the Virtual Machine as well.

## Common Use Cases

- Virtual Machines
- AKS Clusters
- App Services
- VM Scale Sets

## Interview Tip

`replace_triggered_by` is an advanced lifecycle feature used to control resource replacement based on changes in other resources.

---

# Bonus Question

## Scenario

You have a production VM configured as follows:

```hcl
resource "azurerm_linux_virtual_machine" "vm" {

  lifecycle {
    create_before_destroy = true
  }
}
```

A configuration change requires the VM to be replaced.

## Question

What will Terraform do?

## Answer

Terraform will first create the new VM and then destroy the old VM.

### Default Behavior

```text
Destroy Old VM
Create New VM
```

### With create_before_destroy

```text
Create New VM
Destroy Old VM
```

## Explanation

This helps reduce downtime during deployments.

## Common Use Cases

- Virtual Machines
- Load Balancers
- App Services
- VM Scale Sets

## Interview Tip

`create_before_destroy` is commonly used when minimizing downtime is important.

---

# Quick Interview Revision

| Lifecycle Option | Purpose |
|-----------------|---------|
| prevent_destroy | Prevent accidental resource deletion |
| create_before_destroy | Create replacement before destroying original resource |
| ignore_changes | Ignore selected attribute changes |
| replace_triggered_by | Force replacement when another resource changes |

---

# One-Line Interview Definition

The Terraform lifecycle block is used to customize resource creation, modification, replacement, and deletion behavior to improve safety, reduce downtime, and handle infrastructure changes more effectively.