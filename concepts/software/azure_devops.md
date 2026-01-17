# Azure DevOps — Platform Features & Best Practices

**Scope**: Azure DevOps platform services, features, configuration, and enterprise best practices.

**Purpose**: Platform-specific features and configuration. For general DevOps concepts, see [DevOps Practices](devops.md).

## Table of Contents

- [1. Azure DevOps Overview](#1-azure-devops-overview)
- [2. Azure Repos](#2-azure-repos)
- [3. Azure Pipelines](#3-azure-pipelines)
- [4. Azure Boards](#4-azure-boards)
- [5. Azure Artifacts](#5-azure-artifacts)
- [6. Azure Test Plans](#6-azure-test-plans)
- [7. Security & Compliance](#7-security--compliance)
- [8. Monitoring & Analytics](#8-monitoring--analytics)
- [9. Enterprise Best Practices](#9-enterprise-best-practices)

---

## 1. Azure DevOps Overview

### Core Services
- **Azure Repos**: Private Git repositories
- **Azure Pipelines**: CI/CD (YAML or classic)
- **Azure Boards**: Work item tracking, sprints
- **Azure Artifacts**: Package management (NuGet, npm, Maven, Python)
- **Azure Test Plans**: Test management

### Organization Structure
- **Organization**: Top-level container (`contoso.visualstudio.com`)
- **Project**: Repos, pipelines, boards, artifacts
- **Team**: User groups for work items
- **Area Paths**: Organize by product/feature
- **Iteration Paths**: Sprint planning

### Authentication & Authorization
- **Azure AD**: SSO, group management
- **Project Permissions**: Reader, Contributor, Build Administrator, Project Administrator
- **Pipeline Permissions**: Service connections, variable groups, secure files
- **Branch Policies**: PR reviews, build validation, status checks

---

## 2. Azure Repos

### Git Repository Features
- Private repositories, branch policies, pull requests, webhooks, forking

### Branch Policies
- **PR reviews**: Minimum/required reviewers
- **Build validation**: Pipeline before merge
- **Status checks**: External validations (security, tests)
- **Merge strategies**: Squash, rebase, merge
- **Auto-complete**: Auto-merge when policies pass
- **Comment requirements**: Resolve comments before merge

### Repository Settings
- Default branch, branch permissions, file size limits, retention policies

---

## 3. Azure Pipelines

### Pipeline Types
- **YAML Pipelines**: Declarative YAML
- **Classic Pipelines**: Visual designer
- **Multi-stage**: Build, test, deploy
- **Release Pipelines**: Classic release management

### YAML Pipeline Structure
```yaml
trigger:
  branches:
    include: [main, develop]

pool:
  vmImage: 'ubuntu-latest'

stages:
  - stage: Build
    jobs:
      - job: Build
        steps:
          - task: DotNetCoreCLI@2
            inputs:
              command: 'build'
  
  - stage: Test
    dependsOn: Build
    jobs:
      - job: UnitTests
        steps:
          - task: DotNetCoreCLI@2
            inputs:
              command: 'test'
  
  - stage: Deploy
    dependsOn: Test
    condition: succeeded()
    jobs:
      - deployment: DeployStaging
        environment: 'staging'
        strategy:
          runOnce:
            deploy:
              steps:
                - script: echo "Deploy to staging"
```

### Pipeline Components

#### Agents & Pools
- **Microsoft-hosted**: Pre-configured (Windows, Linux, macOS)
- **Self-hosted**: Custom agents
- **Agent pools**: Organize by capability/environment
- **Parallel jobs**: Multiple concurrent jobs

#### Tasks
- **Built-in**: DotNetCoreCLI, PublishBuildArtifacts, AzureWebApp@1
- **Marketplace**: Extensions
- **Custom tasks**: Create for specific needs
- **Task groups**: Reusable task groups

#### Variables
- **Variable groups**: Shared across pipelines
- **Library variables**: Secure, encrypted
- **Runtime variables**: Set at queue time
- **Secret variables**: Encrypted, masked in logs
- **System variables**: Built-in (Build.SourcesDirectory, etc.)

#### Pipeline Templates
- **Template files**: Reusable pipeline logic
- **Template parameters**: Customize per pipeline
- **Template libraries**: Centralized patterns
- **Include templates**: From other repositories

### Pipeline Patterns

#### Multi-Stage Pipelines
- **Build**: Compile, package artifacts
- **Test**: Unit, integration, code quality
- **Deploy**: Dev → Test → Staging → Production

#### Environment Management
- **Environments**: Define deployment targets
- **Approvals**: Require before deployment
- **Checks**: Security scans, tests before deployment
- **Deployment history**: Track per environment

### Service Connections
- **Azure Resource Manager**: Azure subscriptions
- **Docker Registry**: Container registries
- **GitHub**: GitHub repositories
- **Generic**: External services (REST APIs)

---

## 4. Azure Boards

### Work Item Types
- **Epic**: Large feature/initiative
- **Feature**: Significant functionality
- **User Story**: User-facing functionality
- **Task**: Work item
- **Bug**: Defect
- **Issue**: General work item

### Work Item Tracking
- **Backlog**: Prioritized work items
- **Sprints**: Time-boxed iterations
- **Kanban Boards**: Visualize workflow
- **Queries**: Custom work item queries
- **Dashboards**: Work item metrics

### Integration
- **Git Integration**: Link commits to work items
- **Pull Request Integration**: Link PRs
- **Pipeline Integration**: Link deployments
- **Automation Rules**: Auto-update work items

---

## 5. Azure Artifacts

### Package Management
- **NuGet**: .NET packages
- **npm**: Node.js packages
- **Maven**: Java packages
- **Python**: PyPI packages
- **Universal Packages**: Any file type

### Artifact Feeds
- **Organization feeds**: Shared across org
- **Project feeds**: Project-scoped
- **Public feeds**: Public packages
- **Upstream sources**: Connect to public registries

### Package Publishing
- **Pipeline publishing**: Publish from pipelines
- **Versioning**: Semantic versioning, auto-increment
- **Retention policies**: Package retention
- **Package promotion**: Promote between feeds

---

## 6. Azure Test Plans

### Test Management
- **Test Plans**: Organize test cases
- **Test Suites**: Group related cases
- **Test Cases**: Individual scenarios
- **Test Runs**: Execute cases
- **Test Results**: Track execution

### Manual Testing
- **Test Runner**: Execute manual tests
- **Test Attachments**: Screenshots, logs, files
- **Exploratory Testing**: Ad-hoc sessions
- **Bug Creation**: Create from failures

### Automated Testing
- **Test Integration**: Automated frameworks
- **Test Results**: Publish to pipelines
- **Code Coverage**: Track coverage

---

## 7. Security & Compliance

### Secrets Management
- **Azure Key Vault**: Secrets, certificates, keys
- **Variable Groups**: Encrypted pipeline variables
- **Service Connections**: Secure Azure connections
- **Secure Files**: Encrypted files for pipelines
- **Never commit secrets**: Use Key Vault or secure variables

### Pipeline Security
- **Least Privilege**: Minimum permissions
- **Service Principals**: Managed identities
- **Audit Logging**: Track executions and changes
- **Branch Protection**: PR reviews, prevent force push
- **Pipeline Permissions**: Control run/modify access

### Compliance
- **Policy Enforcement**: Azure Policy
- **Security Scanning**: Vulnerability scanning
- **Compliance Gates**: Block on failed checks
- **Audit Trails**: Track changes and deployments
- **Retention Policies**: Data retention

### Security Scanning
- **Dependency Scanning**: Vulnerable packages
- **Container Scanning**: Docker image vulnerabilities
- **Infrastructure Scanning**: IaC misconfigurations
- **SAST/DAST**: Static and dynamic testing

---

## 8. Monitoring & Analytics

### Pipeline Analytics
- **Build Analytics**: Build times, success rates
- **Deployment Analytics**: Frequency, lead time
- **Failure Analysis**: Common failure patterns
- **Trend Analysis**: Metrics over time
- **Agent Utilization**: Agent pool usage

### Application Monitoring
- **Application Insights**: APM
- **Log Analytics**: Centralized logging
- **Azure Monitor**: Metrics, alerts, dashboards
- **Distributed Tracing**: Cross-service requests

### Deployment Monitoring
- **Health Checks**: Verify deployment success
- **Smoke Tests**: Validate critical functionality
- **Rollback Triggers**: Auto-rollback on failure
- **Deployment Notifications**: Notify teams

### Dashboards & Reports
- **Project Dashboards**: Custom dashboards
- **Widgets**: Custom widgets
- **Analytics Views**: Query work item data
- **Power BI Integration**: Export to Power BI

---

## 9. Enterprise Best Practices

### Multi-Tenant Pipelines
- **Template Reuse**: Shared templates across teams
- **Environment Isolation**: Separate per tenant
- **Configuration Management**: Tenant-specific configs
- **Resource Naming**: Consistent conventions

### Governance Patterns
- **Pipeline Approval**: Require for production
- **Change Management**: Link to work items
- **Compliance Gates**: Enforce security/compliance
- **Audit Requirements**: Maintain audit trails
- **Project Structure**: Organize by team/product

### Scaling Patterns
- **Self-Service**: Teams create own pipelines
- **Golden Paths**: Templates and best practices
- **Centralized Governance**: Policy enforcement
- **Federated Ownership**: Teams own pipelines
- **Extension Marketplace**: Common patterns

### Cost Management
- **Pipeline Optimization**: Reduce build times, parallel execution
- **Resource Tagging**: Track costs by project/team
- **Budget Alerts**: Monitor spending
- **Right-Sizing**: Appropriate VM sizes
- **Parallel Jobs**: Optimize usage

### Best Practices Summary
- Use YAML pipelines (prefer over classic)
- Version control pipelines in Git
- Use templates for reuse
- Secure secrets (Key Vault or secure variables)
- Monitor continuously (metrics, logs, traces)
- Document everything (pipelines, runbooks)
- Security first (scanning, secrets, compliance)
- Optimize costs (right-size agents, parallel jobs)

---

> **Note**: For general DevOps concepts and methodologies, see [DevOps Practices](devops.md). For cloud-specific deployment patterns and architecture, see [Cloud Architecture](../cloud/architecture.md).
