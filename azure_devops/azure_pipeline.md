# Azure DevOps Pipeline Complete Guide

## Overview

Azure DevOps Pipeline is a CI/CD service used to automate:

- Build
- Validation
- Security Scanning
- Testing
- Infrastructure Provisioning
- Application Deployment

Azure DevOps supports two major pipeline types:

1. Classic Pipeline
2. YAML Pipeline

---

# 1. Classic Pipeline

Classic Pipeline is GUI based.

## Architecture

```text
Developer
    |
    v
Azure DevOps
    |
    v
Classic Pipeline (GUI)
    |
    v
Agent
```

## Advantages

- Easy to create
- Beginner friendly

## Disadvantages

- Difficult to maintain
- No version control
- Not Infrastructure as Code

## Industry Usage

Low

---

# 2. YAML Pipeline

Pipeline configuration is stored in source code repository.

## Architecture

```text
Git Repository
      |
      v
azure-pipelines.yml
      |
      v
Azure DevOps
      |
      v
Agent
```

## Advantages

- Version Controlled
- Reusable
- Auditable
- Infrastructure as Code

## Industry Usage

Very High

---

# Azure YAML Pipeline Structures

There are three common YAML pipeline structures:

1. Step Based Pipeline
2. Job Based Pipeline
3. Multi Stage Pipeline

---

# 3. Step Based Pipeline

Smallest pipeline structure.

## Architecture

```text
Pipeline
   |
   +-- Steps
```

## Example

```yaml
trigger:
- main

pool:
  name: SelfHostedPool

steps:

- script: terraform init

- script: terraform validate

- script: terraform plan
```

## Use Cases

- Learning
- POC
- Small Automation

## Advantages

- Simple

## Disadvantages

- Difficult to scale
## Example: Step Based YAML Pipeline
```yaml
trigger:
- main

pool:
  name: SelfHostedPool

steps:

# Terraform Format Check
- script: terraform fmt -check
  displayName: 'Terraform Format Check'

# Terraform Initialization
- script: terraform init
  displayName: 'Terraform Init'

# Terraform Validation
- script: terraform validate
  displayName: 'Terraform Validate'

# TFLint Scan
- script: tflint
  displayName: 'TFLint Scan'

# TFSec Scan
- script: tfsec .
  displayName: 'TFSec Scan'

# Terraform Plan
- script: terraform plan -out=tfplan
  displayName: 'Terraform Plan'

# Terraform Apply
- script: terraform apply -auto-approve tfplan
  displayName: 'Terraform Apply'
```
---

# 4. Job Based Pipeline

Multiple jobs inside same pipeline.

## Architecture

```text
Pipeline
    |
    +-- Job1
    |
    +-- Job2
    |
    +-- Job3
```

## Example

```yaml
trigger:
- main

pool:
  name: SelfHostedPool

jobs:

- job: Validation

  steps:
  - script: terraform validate

- job: SecurityScan

  steps:
  - script: tfsec .
```

## Use Cases

- Independent tasks
- Parallel execution

Example:

```text
Job1 -> TFLint

Job2 -> TFSec

Job3 -> Checkov
```

## Advantages

- Parallel execution
- Faster pipeline

## Disadvantages

- Jobs are isolated
- Workspace not automatically shared
## Example: Job based YAML Pipeline
```yaml
trigger:
- main

pool:
  name: SelfHostedPool

jobs:

# Validation Job
- job: Validation

  steps:

  - script: terraform fmt -check
    displayName: 'Terraform Format Check'

  - script: terraform init
    displayName: 'Terraform Init'

  - script: terraform validate
    displayName: 'Terraform Validate'


# Security Scan Job
- job: SecurityScan

  dependsOn: Validation

  steps:

  - script: terraform init
    displayName: 'Terraform Init'

  - script: tflint
    displayName: 'TFLint Scan'

  - script: tfsec .
    displayName: 'TFSec Scan'


# Plan Job
- job: Plan

  dependsOn: SecurityScan

  steps:

  - script: terraform init
    displayName: 'Terraform Init'

  - script: terraform plan -out=tfplan
    displayName: 'Terraform Plan'


# Apply Job
- job: Apply

  dependsOn: Plan

  steps:

  - script: terraform init
    displayName: 'Terraform Init'

  - script: terraform apply -auto-approve
    displayName: 'Terraform Apply'
```

---

# 5. Multi Stage Pipeline

Most common production pipeline.

## Architecture

```text
Pipeline
   |
   +-- Validation Stage
   |
   +-- Security Scan Stage
   |
   +-- Plan Stage
   |
   +-- Approval Stage
   |
   +-- Apply Stage
```

## Example-1:

```yaml
trigger:
- main

pool:
  name: SelfHostedPool

stages:

- stage: Validation

  jobs:

  - job: ValidationJob

    steps:

    - script: terraform fmt -check

    - script: terraform init

    - script: terraform validate

- stage: Plan

  jobs:

  - job: PlanJob

    steps:

    - script: terraform plan
```
## Advantages

- Better visibility
- Environment promotion
- Approval support
- Enterprise ready

## Industry Usage

Very High

---

# Pipeline Hierarchy

```text
Pipeline
|
+-- Stage
      |
      +-- Job
             |
             +-- Step
                    |
                    +-- Script
                    +-- Task
```
# Example: Multi Stage YAML Pipeline
```yaml
trigger:
- main

pool:
  name: SelfHostedPool

stages:

# ==========================
# Stage 1 - Validation
# ==========================

- stage: Validation
  displayName: 'Terraform Validation'

  jobs:

  - job: ValidationJob
    displayName: 'Terraform Validation Job'

    steps:

    - script: terraform fmt -check
      displayName: 'Terraform Format Check'

    - script: terraform init
      displayName: 'Terraform Init'

    - script: terraform validate
      displayName: 'Terraform Validate'


# ==========================
# Stage 2 - Security Scan
# ==========================

- stage: SecurityScan
  displayName: 'Terraform Security Scan'

  dependsOn: Validation

  jobs:

  - job: SecurityScanJob
    displayName: 'Security Scan Job'

    steps:

    - script: terraform init
      displayName: 'Terraform Init'

    - script: tflint
      displayName: 'TFLint Scan'

    - script: tfsec .
      displayName: 'TFSec Scan'


# ==========================
# Stage 3 - Plan
# ==========================

- stage: Plan
  displayName: 'Terraform Plan'

  dependsOn: SecurityScan

  jobs:

  - job: PlanJob
    displayName: 'Terraform Plan Job'

    steps:

    - script: terraform init
      displayName: 'Terraform Init'

    - script: terraform plan -out=tfplan
      displayName: 'Terraform Plan'


# ==========================
# Stage 4 - Apply
# ==========================

- stage: Apply
  displayName: 'Terraform Apply'

  dependsOn: Plan

  jobs:

  - job: ApplyJob
    displayName: 'Terraform Apply Job'

    steps:

    - script: terraform init
      displayName: 'Terraform Init'

    - script: terraform apply -auto-approve
      displayName: 'Terraform Apply'
```
---

# Stage vs Job

## Stage

Represents a major phase.

Examples:

- Validation
- Security Scan
- Build
- Test
- Deploy

Example:

```yaml
- stage: Validation
```

---

## Job

Represents a unit of execution.

Example:

```yaml
- job: TerraformValidation
```

---

## Comparison

| Feature | Stage | Job |
|----------|----------|----------|
| Level | Higher | Lower |
| Contains | Jobs | Steps |
| Approval Support | Yes | No |
| Environment Promotion | Yes | No |
| Parallel Execution | Rare | Common |

---

# Script vs Task

## Script

Custom commands.

Example:

```yaml
- script: terraform validate
```

## Task

Pre-built Azure DevOps component.

Example:

```yaml
- task: TerraformInstaller@1
```

---

# Self Hosted Agent

A self-hosted agent is installed and managed by your organization.

## Architecture

```text
Developer
     |
     v
Azure DevOps
     |
     v
Self Hosted Agent Pool
     |
     +-- Agent01
     |
     +-- Agent02
     |
     +-- Agent03
```

---

# Agent Pool Example

```yaml
pool:
  name: SelfHostedPool
```

---

# Specific Agent Example

```yaml
pool:
  name: SelfHostedPool

demands:
- Agent.Name -equals Agent01
```

---

# Understanding Job Isolation

Many beginners create:

```yaml
jobs:

- job: Init

  steps:
  - script: terraform init

- job: Validate

  steps:
  - script: terraform validate
```

This may fail.

Reason:

```text
Job1 -> Agent01

Job2 -> Agent02
```

Terraform initialization output may not exist on second agent.

---

# Best Practice

Keep dependent commands in same job.

Good Example:

```yaml
jobs:

- job: Validation

  steps:

  - script: terraform fmt -check

  - script: terraform init

  - script: terraform validate
```

Bad Example:

```yaml
jobs:

- job: Init

- job: Validate
```

---

# dependsOn

Used to control execution sequence.

Example:

```yaml
- stage: SecurityScan
  dependsOn: Validation

- stage: Plan
  dependsOn: SecurityScan

- stage: Apply
  dependsOn: Plan
```

Flow:

```text
Validation
    |
    v
Security Scan
    |
    v
Plan
    |
    v
Apply
```

---

# Terraform Pipeline Best Practice

## Stage 1 - Validation

```text
terraform fmt -check

terraform init

terraform validate
```

---

## Stage 2 - Security Scan

```text
tflint

tfsec

checkov
```

---

## Stage 3 - Plan

```text
terraform plan -out=tfplan
```

Publish:

```text
tfplan
```

as pipeline artifact.

---

## Stage 4 - Approval

Manual approval before deployment.

---

## Stage 5 - Apply

```text
terraform apply tfplan
```

---

# Production Architecture

```text
Developer
    |
    v
Git Repository
    |
    v
Azure DevOps Pipeline
    |
    +--> Validation
    |
    +--> Security Scan
    |
    +--> Plan
    |
    +--> Approval
    |
    +--> Apply
    |
    v
Azure Infrastructure
```

---

# Complete Production Terraform Pipeline

```yaml
trigger:
- main

pool:
  name: SelfHostedPool

stages:

- stage: Validation

  jobs:

  - job: Validation

    steps:

    - script: terraform fmt -check

    - script: terraform init

    - script: terraform validate

- stage: SecurityScan

  dependsOn: Validation

  jobs:

  - job: Scan

    steps:

    - script: tflint

    - script: tfsec .

    - script: checkov -d .

- stage: Plan

  dependsOn: SecurityScan

  jobs:

  - job: Plan

    steps:

    - script: terraform plan -out=tfplan

- stage: Approval

  dependsOn: Plan

  jobs:

  - job: Approval

- stage: Apply

  dependsOn: Approval

  jobs:

  - job: Apply

    steps:

    - script: terraform apply tfplan
```

---

# Interview Summary

Pipeline Types:

1. Classic Pipeline
2. YAML Pipeline

YAML Structures:

1. Step Based Pipeline
2. Job Based Pipeline
3. Multi Stage Pipeline

Industry Best Practice:

- YAML Pipeline
- Multi Stage Pipeline
- Self Hosted Agents
- Validation Stage
- Security Scan Stage
- Plan Stage
- Manual Approval
- Apply Stage
- Artifact Based Deployment
- Git Version Control
- Infrastructure as Code

# Azure DevOps Agent Pool Scope

## What is an Agent Pool?

An **Agent Pool** is a collection of one or more agents (Microsoft-hosted or Self-hosted) that execute pipeline jobs.

Example:

```text
Terraform-Agent-Pool
    ├── Agent-01
    ├── Agent-02
    └── Agent-03
```

Whenever a pipeline runs, Azure DevOps allocates one available agent from the specified pool to execute the job.

---

# Pool Scope

An Agent Pool can be defined at three different levels.

1. Pipeline Level
2. Stage Level
3. Job Level

Priority:

```text
Job Level
    ↑
Stage Level
    ↑
Pipeline Level
```

The more specific scope always overrides the broader scope.

For example:

- Job Level overrides Stage Level.
- Stage Level overrides Pipeline Level.

---

# 1. Pipeline Level Pool

```yaml
pool:
  name: Terraform-Agent-Pool

stages:

- stage: Validation

  jobs:

  - job: Validate

    steps:
    - script: terraform validate
```

## Explanation

The pool is defined once at the pipeline level.

Every stage and every job automatically uses **Terraform-Agent-Pool**, unless another pool is explicitly defined.

This is the most common approach when every stage requires the same Self-hosted Agent.

---

# 2. Stage Level Pool

```yaml
stages:

- stage: Validation

  pool:
    name: Linux-Agent-Pool

  jobs:

  - job: Validate

    steps:
    - script: terraform validate
```

## Explanation

The pool applies only to the jobs inside the **Validation** stage.

Other stages can use different pools.

Example:

```text
Validation Stage
        │
        ▼
Linux-Agent-Pool


Deployment Stage
        │
        ▼
Windows-Agent-Pool
```

---

# 3. Job Level Pool

```yaml
stages:

- stage: Deployment

  jobs:

  - job: Apply

    pool:
      name: Terraform-Agent-Pool

    steps:

    - script: terraform apply
```

## Explanation

Only this job uses **Terraform-Agent-Pool**.

Other jobs inside the same stage can use different pools.

Job Level has the highest priority.

---

# Why do we use `pool: server`?

Consider the following example.

```yaml
trigger:
  branches:
    include:
      - main

stages:

- stage: Approval

  jobs:

  - job: WaitForApproval

    pool: server

    steps:

    - task: ManualValidation@0
      inputs:
        instructions: "Please review and approve deployment."
```

Many engineers become confused because the pipeline also contains a Self-hosted Agent Pool.

Example:

```yaml
pool:
  name: Terraform-Agent-Pool
```

So why is another pool specified?

The reason is that the **Manual Validation** task does not execute any deployment commands.

It only waits for someone to approve or reject the deployment.

During this stage:

- No Terraform commands are executed.
- No Azure CLI commands are executed.
- No Bash scripts are executed.
- No PowerShell scripts are executed.
- No Git checkout is required.

Therefore, a Self-hosted Agent is not required.

Instead, Azure DevOps executes this job internally using:

```yaml
pool: server
```

---

# What happens if we do NOT use `pool: server`?

Suppose the pipeline is written like this.

```yaml
pool:
  name: Terraform-Agent-Pool

stages:

- stage: Approval

  jobs:

  - job: WaitForApproval

    steps:

    - task: ManualValidation@0
```

Since no Job Level pool is specified, Azure DevOps automatically uses the Pipeline Level pool.

Execution:

```text
Terraform-Agent-Pool

        │
        ▼

Self-hosted Agent Allocated

        │
        ▼

Waiting for Approval...

        │
        ▼

Waiting...

        │
        ▼

Waiting...

        │
        ▼

User Clicks Approve
```

During the entire approval period, the Self-hosted Agent remains occupied even though it is not executing any commands.

This is considered inefficient because another pipeline cannot use that agent.

---

# Recommended Production Design

```yaml
stages:

- stage: Approval

  jobs:

  - job: WaitForApproval

    pool: server

    steps:

    - task: ManualValidation@0


- stage: Deploy

  jobs:

  - job: TerraformApply

    pool:
      name: Terraform-Agent-Pool

    steps:

    - checkout: self

    - script: terraform init

    - script: terraform apply tfplan
```

Execution Flow:

```text
Merge to Main
      │
      ▼

Approval Stage
      │
      ▼

Azure DevOps Server
(No Agent Required)

      │
      ▼

Manager Approves Deployment

      │
      ▼

Deploy Stage

      │
      ▼

Self-hosted Agent Allocated

      │
      ▼

Checkout Source Code

      │
      ▼

Terraform Init

      │
      ▼

Terraform Apply
```

---

# Best Practice

For production environments:

- Use **Pipeline Level Pool** when all jobs require the same agent.
- Use **Stage Level Pool** when different stages require different agents.
- Use **Job Level Pool** when only a specific job requires a different agent.
- Use **`pool: server`** for Manual Validation because it does not require an agent.
- Allocate a Self-hosted Agent only when actual work such as Terraform, Azure CLI, Bash, or PowerShell execution is required.

---

# Interview Answer

**Question: Why do we use `pool: server` for Manual Validation?**

**Answer:**

The Manual Validation task does not execute any deployment commands. It simply waits for an approval or rejection. Using `pool: server` allows Azure DevOps to handle the approval internally without reserving a Self-hosted Agent. This improves resource utilization because the Self-hosted Agent is allocated only when the deployment actually starts.


# Agent Configuration
# Azure CLi Install
```bash
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
az login --use-device-code
```
# install terraform
```bash
sudo apt update
sudo apt install -y gnupg software-properties-common curl

wget -O- https://apt.releases.hashicorp.com/gpg | \
gpg --dearmor | \
sudo tee /usr/share/keyrings/hashicorp-archive-keyring.gpg > /dev/null

echo "deb [signed-by=/usr/share/keyrings/hashicorp-archive-keyring.gpg] https://apt.releases.hashicorp.com $(lsb_release -cs) main" | \
sudo tee /etc/apt/sources.list.d/hashicorp.list

sudo apt update

sudo apt install -y terraform

terraform version
```

# Tflint Installation
```bash
wget https://github.com/terraform-linters/tflint/releases/latest/download/tflint_linux_amd64.zip

sudo apt install -y unzip
unzip tflint_linux_amd64.zip
sudo mv tflint /usr/local/bin/
chmod +x /usr/local/bin/tflint
tflint --version
```


# TfSec Installation
```bash
wget https://github.com/aquasecurity/tfsec/releases/latest/download/tfsec-linux-amd64

chmod +x tfsec-linux-amd64
sudo mv tfsec-linux-amd64 /usr/local/bin/tfsec
tfsec --version
```
