# IT Project Management — Enterprise Tools

**Purpose**: Enterprise tools, platforms, and utilities commonly used in Microsoft-centric IT project management environments.

**Related Documentation**: See [IT Project Management](../concepts/project_management/itpm.md) for implementation patterns and practices. For standards and research, see [RESEARCH.md](RESEARCH.md).

---

## Table of Contents

- [Code Security & Vulnerability Scanning](#code-security--vulnerability-scanning)
- [Security & Compliance](#security--compliance)
- [Development & DevOps](#development--devops)
- [Documentation & Collaboration](#documentation--collaboration)
- [Diagramming & Architecture](#diagramming--architecture)
- [Analytics & Reporting](#analytics--reporting)
- [Infrastructure & IaC](#infrastructure--iac)
- [Container & Orchestration](#container--orchestration)

---

## Code Security & Vulnerability Scanning

- **GitHub Advanced Security**
  - [GitHub Advanced Security](https://github.com/features/security)
  - [GitHub Dependabot](https://github.com/dependabot)
  - [GitHub Code Scanning](https://github.com/features/security/code-scanning)
  - [GitHub Secret Scanning](https://github.com/features/security/secret-scanning)

- **Microsoft Defender for Cloud**
  - [Azure Defender](https://azure.microsoft.com/services/azure-defender/)
  - [Defender for DevOps](https://learn.microsoft.com/azure/defender-for-cloud/defender-for-devops-introduction)

- **SonarQube / SonarCloud**
  - [SonarCloud](https://sonarcloud.io/) - Azure DevOps integration
  - [SonarQube](https://www.sonarqube.org/)

- **Snyk** (Common in Microsoft environments)
  - [Snyk Open Source Security](https://snyk.io/)
  - [Snyk Code (SAST)](https://snyk.io/product/snyk-code/)
  - [Snyk Container Security](https://snyk.io/product/container-security/)

---

## Security & Compliance

- **Microsoft Sentinel (SIEM/SOAR)**
  - [Azure Sentinel](https://azure.microsoft.com/services/azure-sentinel/)
  - [Sentinel SOAR capabilities](https://learn.microsoft.com/azure/sentinel/)

- **Microsoft Defender for Cloud**
  - [Defender for Cloud](https://azure.microsoft.com/services/defender-for-cloud/)
  - [Cloud Security Posture Management](https://learn.microsoft.com/azure/defender-for-cloud/concept-cloud-security-posture-management)

- **Azure Key Vault**
  - [Azure Key Vault](https://azure.microsoft.com/services/key-vault/)
  - [Key Vault documentation](https://learn.microsoft.com/azure/key-vault/)

- **Microsoft Purview**
  - [Microsoft Purview](https://www.microsoft.com/security/business/risk-management/microsoft-purview)
  - [Data governance and compliance](https://www.microsoft.com/microsoft-365/purview)

---

## Development & DevOps

- **Azure DevOps**
  - [Azure DevOps](https://azure.microsoft.com/services/devops/)
  - [Azure Pipelines](https://azure.microsoft.com/services/devops/pipelines/)
  - [Azure Repos (Git)](https://azure.microsoft.com/services/devops/repos/)
  - [Azure Artifacts](https://azure.microsoft.com/services/devops/artifacts/)
  - [Azure Boards](https://azure.microsoft.com/services/devops/boards/)
  - [Azure Test Plans](https://azure.microsoft.com/services/devops/test-plans/)

- **Azure Repos (Git) Features**
  - [Azure Repos documentation](https://learn.microsoft.com/azure/devops/repos/)
  - [Branch policies](https://learn.microsoft.com/azure/devops/repos/git/branch-policies)
  - [Pull requests](https://learn.microsoft.com/azure/devops/repos/git/pull-requests)
  - [Code search](https://learn.microsoft.com/azure/devops/project/search)
  - [File history and annotations](https://learn.microsoft.com/azure/devops/repos/git/history)
  - [Webhooks](https://learn.microsoft.com/azure/devops/service-hooks/overview)

- **Azure DevOps Security & Scanning**
  - [Defender for DevOps](https://learn.microsoft.com/azure/defender-for-cloud/defender-for-devops-introduction)
  - [Azure DevOps security scanning](https://learn.microsoft.com/azure/devops/organizations/security/)
  - [Service connections security](https://learn.microsoft.com/azure/devops/pipelines/library/service-endpoints)
  - [Variable groups (secrets)](https://learn.microsoft.com/azure/devops/pipelines/library/variable-groups)
  - [Secure files](https://learn.microsoft.com/azure/devops/pipelines/library/secure-files)

- **Azure DevOps Extensions & Marketplace**
  - [Azure DevOps Marketplace](https://marketplace.visualstudio.com/azuredevops)
  - [SonarCloud extension](https://marketplace.visualstudio.com/items?itemName=SonarSource.sonarcloud)
  - [Snyk extension](https://marketplace.visualstudio.com/items?itemName=snyk-security.snyk-vulnerability-scanner)
  - [WhiteSource extension](https://marketplace.visualstudio.com/items?itemName=whitesource.whiteSource-bolt-azure-devops)
  - [OWASP ZAP extension](https://marketplace.visualstudio.com/items?itemName=CSE-DevOps.zap-scanner)

- **Visual Studio / Visual Studio Code**
  - [Visual Studio](https://visualstudio.microsoft.com/)
  - [Visual Studio Code](https://code.visualstudio.com/)
  - [VS Code Extensions](https://marketplace.visualstudio.com/vscode)
  - [Azure Repos extension for VS Code](https://marketplace.visualstudio.com/items?itemName=ms-vscode.azure-repos)

- **GitHub**
  - [GitHub](https://github.com/)
  - [GitHub Actions](https://github.com/features/actions)
  - [GitHub Copilot](https://github.com/features/copilot)

---

## Documentation & Collaboration

- **Microsoft 365**
  - [Microsoft Teams](https://www.microsoft.com/microsoft-teams/group-chat-software)
  - [SharePoint](https://www.microsoft.com/microsoft-365/sharepoint/collaboration)
  - [OneDrive](https://www.microsoft.com/microsoft-365/onedrive/online-cloud-storage)
  - [Microsoft Word](https://www.microsoft.com/microsoft-365/word)

- **Confluence** (Common in enterprise)
  - [Confluence](https://www.atlassian.com/software/confluence)

- **Markdown Tools**
  - [Markdown](https://daringfireball.net/projects/markdown/)
  - [GitHub Flavored Markdown](https://github.github.com/gfm/)

---

## Diagramming & Architecture

- **Microsoft Visio**
  - [Microsoft Visio](https://www.microsoft.com/microsoft-365/visio/flowchart-software)
  - [Visio Online](https://www.microsoft.com/microsoft-365/visio/flowchart-software)

- **Draw.io / diagrams.net**
  - [diagrams.net](https://www.diagrams.net/)
  - [Azure integration](https://marketplace.visualstudio.com/items?itemName=hediet.vscode-drawio)

- **Lucidchart**
  - [Lucidchart](https://www.lucidchart.com/)
  - [Azure architecture diagrams](https://www.lucidchart.com/pages/templates/azure-architecture)

- **PlantUML**
  - [PlantUML](https://plantuml.com/)
  - [VS Code extension](https://marketplace.visualstudio.com/items?itemName=jebbs.plantuml)

---

## Analytics & Reporting

- **Power BI**
  - [Power BI](https://powerbi.microsoft.com/)
  - [Power BI Desktop](https://powerbi.microsoft.com/desktop/)
  - [Power BI Service](https://powerbi.microsoft.com/service/)

- **Azure Monitor**
  - [Azure Monitor](https://azure.microsoft.com/services/monitor/)
  - [Application Insights](https://azure.microsoft.com/services/monitor/#application-insights-section)

- **Microsoft Excel**
  - [Microsoft Excel](https://www.microsoft.com/microsoft-365/excel)
  - [Power Query](https://learn.microsoft.com/power-query/)

---

## Infrastructure & IaC

- **Azure Resource Manager (ARM)**
  - [ARM Templates](https://learn.microsoft.com/azure/azure-resource-manager/templates/)

- **Bicep**
  - [Bicep](https://learn.microsoft.com/azure/azure-resource-manager/bicep/)

- **Terraform**
  - [Terraform Azure Provider](https://registry.terraform.io/providers/hashicorp/azurerm/latest/docs)

- **Pulumi**
  - [Pulumi Azure](https://www.pulumi.com/docs/clouds/azure/)

---

## Container & Orchestration

- **Azure Container Registry (ACR)**
  - [Azure Container Registry](https://azure.microsoft.com/services/container-registry/)

- **Azure Kubernetes Service (AKS)**
  - [Azure Kubernetes Service](https://azure.microsoft.com/services/kubernetes-service/)

- **Azure Container Instances**
  - [Container Instances](https://azure.microsoft.com/services/container-instances/)

---

> **Related Documentation**: See [IT Project Management](../concepts/project_management/itpm.md) for implementation patterns and best practices. For standards and research, see [RESEARCH.md](RESEARCH.md).

