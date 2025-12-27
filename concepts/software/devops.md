# DevOps & Azure DevOps — Enterprise Practices

**Scope**: DevOps practices, CI/CD pipelines, infrastructure as code, and Azure DevOps implementation for enterprise development.

**Purpose**: Best practices and patterns for implementing DevOps in enterprise environments using Azure DevOps.

## Table of Contents

- [1. DevOps Principles](#1-devops-principles)
- [2. Azure DevOps Overview](#2-azure-devops-overview)
- [3. Source Control Strategy](#3-source-control-strategy)
- [4. CI/CD Pipeline Patterns](#4-cicd-pipeline-patterns)
- [5. Infrastructure as Code (IaC)](#5-infrastructure-as-code-iac)
- [6. Deployment Strategies](#6-deployment-strategies)
- [7. Testing in Pipelines](#7-testing-in-pipelines)
- [8. Security & Compliance](#8-security--compliance)
- [9. Monitoring & Observability](#9-monitoring--observability)
- [10. Enterprise DevOps Patterns](#10-enterprise-devops-patterns)

---

## 1. DevOps Principles

### Core Principles

- **Culture**: Collaboration between development and operations
- **Automation**: Automate repetitive tasks (build, test, deploy)
- **Measurement**: Metrics and monitoring for continuous improvement
- **Sharing**: Knowledge sharing, documentation, blameless postmortems

### DevOps Practices

- **Continuous Integration (CI)**: Merge code frequently, build and test automatically
- **Continuous Delivery (CD)**: Automate deployment to staging/production
- **Infrastructure as Code (IaC)**: Version control infrastructure
- **Monitoring & Logging**: Real-time visibility into systems
- **Communication**: Shared responsibility, cross-functional teams

### Benefits

- **Faster Delivery**: Reduce time from code to production
- **Higher Quality**: Automated testing catches issues early
- **Improved Reliability**: Consistent, repeatable deployments
- **Better Collaboration**: Shared ownership and responsibility

---

## 2. Azure DevOps Overview

### Core Services

- **Azure Repos**: Git repositories (private, cloud-hosted)
- **Azure Pipelines**: CI/CD pipelines (YAML or classic)
- **Azure Boards**: Work item tracking, sprint planning
- **Azure Artifacts**: Package management (NuGet, npm, Maven, etc.)
- **Azure Test Plans**: Test management and execution

### Organization Structure

- **Organization**: Top-level container (e.g., `contoso.visualstudio.com`)
- **Project**: Collection of repos, pipelines, boards, artifacts
- **Team**: Group of users working on related work items
- **Area Paths**: Organize work items by product/feature
- **Iteration Paths**: Sprint/iteration planning

### Authentication & Authorization

- **Azure AD Integration**: Single sign-on, group management
- **Project Permissions**: Reader, Contributor, Build Administrator, Project Administrator
- **Pipeline Permissions**: Service connections, variable groups, secure files
- **Branch Policies**: Require PR reviews, build validation, status checks

---

## 3. Source Control Strategy

### Git Workflow Patterns

#### Git Flow

- **main/master**: Production-ready code
- **develop**: Integration branch for features
- **feature/***: Feature branches from develop
- **release/***: Prepare for production release
- **hotfix/***: Emergency fixes to production

#### GitHub Flow (Simplified)

- **main**: Always deployable
- **feature/***: Feature branches from main
- **Pull Requests**: Review and merge to main
- **Deploy from main**: Continuous deployment

#### Trunk-Based Development

- **main**: Single branch, frequent commits
- **Short-lived feature branches**: Merge quickly (< 1 day)
- **Feature flags**: Enable/disable features without branching

### Branch Policies

- **Require PR reviews**: Minimum reviewers, required reviewers
- **Build validation**: Run pipeline before merge
- **Status checks**: External validations (security scans, tests)
- **Merge strategies**: Squash, rebase, or merge commits
- **Auto-complete**: Auto-merge when policies pass

### Repository Structure

- **Monorepo**: Single repository for multiple projects
- **Multi-repo**: Separate repository per project/service
- **Hybrid**: Core libraries in monorepo, services in separate repos

---

## 4. CI/CD Pipeline Patterns

### Pipeline Structure

```yaml
# Example Azure Pipelines YAML structure
trigger:
  branches:
    include:
      - main
      - develop

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

### Pipeline Patterns

#### Multi-Stage Pipelines

- **Build Stage**: Compile, package artifacts
- **Test Stage**: Unit tests, integration tests, code quality
- **Deploy Stages**: Dev → Test → Staging → Production

#### Pipeline Templates

- **Reusable templates**: Shared pipeline logic
- **Template parameters**: Customize behavior per pipeline
- **Template libraries**: Centralized pipeline patterns

#### Pipeline Variables

- **Variable groups**: Shared variables across pipelines
- **Library variables**: Secure, encrypted variables
- **Runtime variables**: Set at queue time
- **Secret variables**: Encrypted, masked in logs

### CI Patterns

- **Build on commit**: Trigger on push to branch
- **Pull request validation**: Build and test PRs before merge
- **Scheduled builds**: Nightly builds, dependency updates
- **Matrix builds**: Test across multiple configurations

### CD Patterns

- **Continuous Deployment**: Auto-deploy on successful build
- **Continuous Delivery**: Manual approval gates
- **Blue-Green Deployment**: Switch traffic between environments
- **Canary Deployment**: Gradual rollout to subset of users
- **Feature Flags**: Enable features without deployment

---

## 5. Infrastructure as Code (IaC)

### Azure Resource Manager (ARM) Templates

```json
{
  "$schema": "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "storageAccountName": {
      "type": "string"
    }
  },
  "resources": [
    {
      "type": "Microsoft.Storage/storageAccounts",
      "apiVersion": "2021-09-01",
      "name": "[parameters('storageAccountName')]",
      "location": "[resourceGroup().location]",
      "sku": {
        "name": "Standard_LRS"
      },
      "kind": "StorageV2"
    }
  ]
}
```

### Terraform

```hcl
# Example Terraform configuration
resource "azurerm_resource_group" "example" {
  name     = "example-resources"
  location = "West Europe"
}

resource "azurerm_storage_account" "example" {
  name                     = "examplestorageaccount"
  resource_group_name      = azurerm_resource_group.example.name
  location                 = azurerm_resource_group.example.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
}
```

### Bicep

```bicep
// Example Bicep template
param storageAccountName string
param location string = resourceGroup().location

resource storageAccount 'Microsoft.Storage/storageAccounts@2021-09-01' = {
  name: storageAccountName
  location: location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
}
```

### IaC Best Practices

- **Version control**: Store templates in Git
- **Parameterization**: Make templates reusable
- **Modularization**: Break large templates into modules
- **Validation**: Test templates before deployment
- **Idempotency**: Templates should be safe to run multiple times
- **State management**: Track deployed resources (Terraform state)

---

## 6. Deployment Strategies

### Deployment Patterns

#### Manual Deployment

- **Approval gates**: Require manual approval before deployment
- **Run-on-demand**: Deploy when triggered manually
- **Use case**: Production deployments requiring sign-off

#### Automated Deployment

- **Continuous deployment**: Auto-deploy on successful build
- **Conditional deployment**: Deploy based on branch, tag, or condition
- **Use case**: Dev/test environments, feature branches

### Environment Strategy

- **Dev**: Developer testing, rapid iteration
- **Test**: Integration testing, QA validation
- **Staging**: Production-like, final validation
- **Production**: Live environment, users

### Deployment Methods

#### Azure App Service

- **Zip Deploy**: Upload and extract zip file
- **Git Deploy**: Push to deployment slot
- **Container Deploy**: Deploy Docker containers
- **Deployment Slots**: Blue-green deployment

#### Azure Kubernetes Service (AKS)

- **kubectl apply**: Apply Kubernetes manifests
- **Helm Charts**: Package and deploy applications
- **GitOps**: ArgoCD, Flux for declarative deployments

#### Azure Functions

- **Zip Deploy**: Upload function code
- **Container Deploy**: Deploy function containers
- **Source Control**: Direct deployment from Git

---

## 7. Testing in Pipelines

### Test Types

#### Unit Tests

- **Fast execution**: Run in build stage
- **Isolated**: No external dependencies
- **High coverage**: Test business logic

#### Integration Tests

- **External dependencies**: Databases, APIs, services
- **Test environment**: Deploy to test environment first
- **Slower execution**: Run after unit tests

#### End-to-End Tests

- **Full system**: Test complete workflows
- **Production-like**: Use staging environment
- **Smoke tests**: Critical path validation

### Test Execution

```yaml
# Example test stage
- stage: Test
  jobs:
    - job: UnitTests
      steps:
        - task: DotNetCoreCLI@2
          displayName: 'Run Unit Tests'
          inputs:
            command: 'test'
            projects: '**/*Tests.csproj'
    
    - job: IntegrationTests
      dependsOn: DeployTest
      steps:
        - task: DotNetCoreCLI@2
          displayName: 'Run Integration Tests'
          inputs:
            command: 'test'
            projects: '**/*IntegrationTests.csproj'
```

### Code Quality

- **Static Analysis**: SonarQube, CodeQL, ESLint
- **Code Coverage**: Measure test coverage
- **Security Scanning**: Dependency scanning, SAST/DAST
- **License Compliance**: Check open-source licenses

---

## 8. Security & Compliance

### Security Practices

#### Secrets Management

- **Azure Key Vault**: Store secrets, certificates, keys
- **Variable Groups**: Encrypted pipeline variables
- **Service Connections**: Secure connections to Azure resources
- **Never commit secrets**: Use Key Vault or secure variables

#### Pipeline Security

- **Least Privilege**: Minimum permissions for pipelines
- **Service Principals**: Managed identities for Azure resources
- **Audit Logging**: Track pipeline executions and changes
- **Branch Protection**: Require PR reviews, prevent force push

#### Compliance

- **Policy Enforcement**: Azure Policy for resource compliance
- **Security Scanning**: Vulnerability scanning in pipelines
- **Compliance Gates**: Block deployment if compliance checks fail
- **Audit Trails**: Track all changes and deployments

### Security Scanning

- **Dependency Scanning**: Check for vulnerable packages
- **Container Scanning**: Scan Docker images for vulnerabilities
- **Infrastructure Scanning**: Scan IaC templates for misconfigurations
- **SAST/DAST**: Static and dynamic application security testing

---

## 9. Monitoring & Observability

### Pipeline Monitoring

- **Pipeline Analytics**: Track build times, success rates
- **Deployment Analytics**: Track deployment frequency, lead time
- **Failure Analysis**: Identify common failure patterns
- **Trend Analysis**: Monitor metrics over time

### Application Monitoring

- **Application Insights**: Application performance monitoring
- **Log Analytics**: Centralized logging and querying
- **Azure Monitor**: Metrics, alerts, dashboards
- **Distributed Tracing**: Track requests across services

### Deployment Monitoring

- **Health Checks**: Verify deployment success
- **Smoke Tests**: Validate critical functionality
- **Rollback Triggers**: Auto-rollback on failure
- **Deployment Notifications**: Notify teams of deployments

---

## 10. Enterprise DevOps Patterns

### Multi-Tenant Pipelines

- **Template Reuse**: Shared pipeline templates across teams
- **Environment Isolation**: Separate environments per tenant
- **Configuration Management**: Tenant-specific configurations
- **Resource Naming**: Consistent naming conventions

### Governance Patterns

- **Pipeline Approval**: Require approvals for production deployments
- **Change Management**: Link deployments to work items
- **Compliance Gates**: Enforce security and compliance checks
- **Audit Requirements**: Maintain audit trails

### Scaling Patterns

- **Self-Service**: Enable teams to create their own pipelines
- **Golden Paths**: Provide templates and best practices
- **Centralized Governance**: Policy enforcement, standards
- **Federated Ownership**: Teams own their pipelines

### Cost Management

- **Pipeline Optimization**: Reduce build times, parallel execution
- **Resource Tagging**: Track costs by project/team
- **Budget Alerts**: Monitor and alert on spending
- **Right-Sizing**: Use appropriate VM sizes for agents

### Best Practices Summary

- **Automate Everything**: Build, test, deploy, infrastructure
- **Version Control**: Infrastructure, configuration, pipelines
- **Test Early**: Run tests as early as possible in pipeline
- **Fail Fast**: Catch issues early, provide quick feedback
- **Monitor Continuously**: Track metrics, logs, traces
- **Iterate Quickly**: Short feedback loops, frequent deployments
- **Document Everything**: Pipeline documentation, runbooks
- **Security First**: Security scanning, secrets management, compliance

---

> **Note**: For cloud-specific deployment patterns and architecture, see [Architecture](cloud_architecture.md). For troubleshooting deployment issues, see [OSI Model - Troubleshooting Lens](cloud_osi.md).

