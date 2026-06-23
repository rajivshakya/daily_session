# Azure DevOps Overview

## What is Azure DevOps?

Azure DevOps (ADO) is a cloud-based DevOps platform provided by Microsoft that helps organizations plan, develop, build, test, and deploy applications efficiently. It provides an integrated set of tools to support the complete Software Development Lifecycle (SDLC).

Azure DevOps enables collaboration between Development, Operations, QA, and Project Management teams by automating software delivery processes and maintaining source code, work items, testing, and deployments in a centralized platform.

---

# Key Features of Azure DevOps

- Agile Project Management
- Source Code Management
- Continuous Integration (CI)
- Continuous Deployment (CD)
- Automated Testing
- Package Management
- Infrastructure as Code (IaC)
- Release Management
- Reporting and Dashboards
- Security and Compliance Integration

---

# Azure DevOps Services

## 1. Azure Boards

Used for Agile project management and work tracking.

### Features

- Epics
- Features
- User Stories
- Tasks
- Bugs
- Sprint Planning
- Kanban Boards
- Backlogs

---

## 2. Azure Repos

Git-based source code management service.

### Features

- Git Repositories
- Branches
- Pull Requests
- Code Reviews
- Version Control

---

## 3. Azure Pipelines

Provides Continuous Integration and Continuous Deployment (CI/CD).

### Features

- Automated Build
- Automated Testing
- Security Scanning
- Application Deployment
- Multi-stage Pipelines
- YAML Pipelines

### Supported Platforms

- Azure
- AWS
- GCP
- Kubernetes
- On-Premises Environments

---

## 4. Azure Test Plans

Used by QA teams for test management.

### Features

- Manual Testing
- Test Cases
- Test Suites
- Test Execution
- Test Reporting

---

## 5. Azure Artifacts

Package repository service.

### Supported Package Types

- NuGet
- npm
- Maven
- Python Packages

Used to store and share build artifacts and application packages.

---

# Azure DevOps Hierarchy

```text
Azure DevOps Organization
          │
          ▼
        Project
          │
 ┌────────┼────────────┬────────────┬────────────┐
 │        │            │            │            │
 ▼        ▼            ▼            ▼            ▼
Boards   Repos     Pipelines   Test Plans   Artifacts
```

---

# Typical Azure DevOps Workflow

```text
Boards
   │
   ▼
Repos
   │
   ▼
Pull Request
   │
   ▼
CI Pipeline
   │
   ▼
Build & Test
   │
   ▼
Artifact Creation
   │
   ▼
CD Pipeline
   │
   ▼
Deployment
   │
   ▼
Testing & Validation
```

---

# Common Azure DevOps Components

## Organization

Top-level container that manages:

- Users
- Permissions
- Projects
- Billing
- Agent Pools

## Project

Logical container that contains:

- Boards
- Repos
- Pipelines
- Test Plans
- Artifacts

## Dashboard

Provides graphical visibility into:

- Sprint Progress
- Build Status
- Deployment Status
- Work Items
- Bugs
- Releases

## Service Connection

Secure authentication mechanism used by pipelines to connect with external services.

### Examples

- Azure Subscription
- AWS Account
- GitHub
- Kubernetes Cluster
- Docker Hub

## Agent

Machine responsible for executing pipeline jobs.

### Types

#### Microsoft Hosted Agent

- Managed by Microsoft
- Temporary VM for each pipeline run
- No maintenance required

#### Self Hosted Agent

- Installed on your own VM or server
- Suitable for private network access
- Supports custom tools and software

## Agent Pool

Collection of agents available for pipeline execution.

```text
Agent Pool
   │
   ├── Agent-1
   ├── Agent-2
   └── Agent-3
```

---

# Benefits of Azure DevOps

- Faster Software Delivery
- Improved Team Collaboration
- Automated CI/CD
- Better Code Quality
- Centralized Project Management
- Enhanced Security
- Multi-Cloud Support
- Reduced Manual Effort
- Scalable and Flexible Architecture

---

# Summary

Azure DevOps is an end-to-end DevOps platform that provides integrated services for planning, source code management, CI/CD automation, testing, package management, and deployment. It helps organizations implement DevOps practices efficiently while improving collaboration, quality, and delivery speed.