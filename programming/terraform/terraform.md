# Terraform for Azure Infrastructure as Code

**Focus**: Terraform HCL syntax, Azure provider configuration, common Azure resources, state management, modules, and enterprise IaC patterns.

## Table of Contents

- [Core Syntax & HCL](#core-syntax--hcl)
- [Variables & Outputs](#variables--outputs)
- [Resources & Data Sources](#resources--data-sources)
- [Modules](#modules)
- [State Management](#state-management)
- [Azure Provider Configuration](#azure-provider-configuration)
- [Common Azure Resources](#common-azure-resources)
- [Azure Networking](#azure-networking)
- [Azure Compute](#azure-compute)
- [Azure Storage & Data](#azure-storage--data)
- [Azure Identity & Security](#azure-identity--security)
- [Azure Integration Services](#azure-integration-services)
- [Best Practices](#best-practices)

---

## Core Syntax & HCL

**HCL (HashiCorp Configuration Language)**: Declarative language - describes desired state, Terraform plans and applies changes

**Blocks**: `resource "type" "name" { }`, `variable "name" { }`, `output "name" { }`, `module "name" { }`, `data "type" "name" { }`, `provider "name" { }`, `terraform { }`, `locals { }`

**Attributes**: `key = "value"` (string), `key = 42` (number), `key = true` (boolean), `key = ["item1", "item2"]` (list), `key = { k = "v" }` (map)

**Multi-line strings**: `<<EOF ... EOF` (heredoc), `<<-EOF ... EOF` (strip leading whitespace)

**Comments**: `# single line`, `// single line`, `/* multi-line */`

**Interpolation**: `var.name` (preferred), `"${var.name}"` (legacy), `resource.type.name.attribute`

**Functions**: `length()`, `upper()`, `lower()`, `split()`, `join()`, `merge()`, `lookup()`, `file()`, `templatefile()`, `jsonencode()`, `jsondecode()`, `replace()`, `substr()`

**Conditionals**: `condition ? true_value : false_value` (ternary), `var.enable ? 1 : 0`

**For expressions**: `[for item in list : item * 2]` (list), `{for k, v in map : k => upper(v)}` (map), `[for item in list : item if item > 0]` (filtered)

**Splat expressions**: `resource.type.name[*].id` (all instances), `resource.type.name[*].attribute` (attribute)

**Dynamic blocks**: `dynamic "tag" { for_each = var.tags; content { key = tag.key; value = tag.value } }`

**Dependencies**: Implicit (references), explicit `depends_on = [resource.name]`

---

## Variables & Outputs

**Variables**:
```hcl
variable "name" {
  type        = string
  description = "Description"
  default     = "value"
  sensitive   = false
  validation {
    condition     = length(var.name) > 0
    error_message = "Name cannot be empty."
  }
}
variable "tags" {
  type    = map(string)
  default = { Environment = "dev" }
}
variable "enabled" {
  type    = bool
  default = true
}
```

**Variable types**: `string`, `number`, `bool`, `list(type)`, `map(type)`, `set(type)`, `object({})`, `tuple([])`

**Variable files**: `terraform.tfvars`, `*.auto.tfvars` (auto-loaded), `-var-file=prod.tfvars` (CLI)

**Outputs**:
```hcl
output "id" {
  value       = resource.type.name.id
  description = "Description"
  sensitive   = false
}
```

**Locals**:
```hcl
locals {
  prefix = "prod-${var.project}"
  tags = {
    Environment = var.env
    ManagedBy   = "Terraform"
  }
}
```

---

## Resources & Data Sources

**Resources** (create/manage):
```hcl
resource "azurerm_resource_group" "example" {
  name     = "rg-${var.env}-${var.project}"
  location = var.location
  tags     = local.tags
}
```

**Data sources** (read existing):
```hcl
data "azurerm_client_config" "current" {}
data "azurerm_resource_group" "existing" { name = "rg-existing" }
data "azurerm_subscription" "current" {}
```

**Resource lifecycle**:
```hcl
lifecycle {
  create_before_destroy = true
  prevent_destroy      = false
  ignore_changes        = [tags]
  replace_triggered_by  = [resource.name]
}
```

**Count** (multiple instances):
```hcl
resource "azurerm_storage_account" "example" {
  count = 3
  name  = "st${var.project}${count.index}"
}
```

**For_each** (map/set-based):
```hcl
resource "azurerm_storage_account" "example" {
  for_each = { prod = "eastus"; dev = "westus" }
  name     = "st${var.project}-${each.key}"
  location = each.value
}
```

---

## Modules

**Module structure**: `modules/name/main.tf`, `variables.tf`, `outputs.tf`

**Module definition**:
```hcl
# modules/storage-account/main.tf
resource "azurerm_storage_account" "this" {
  name                = var.storage_account_name
  resource_group_name = var.resource_group_name
  location            = var.location
  tags                = var.tags
}
```

**Module usage**:
```hcl
module "storage" {
  source = "./modules/storage-account"
  storage_account_name = "stexample"
  resource_group_name  = azurerm_resource_group.example.name
  location             = var.location
  tags                 = local.tags
}
output "storage_id" {
  value = module.storage.storage_account_id
}
```

**Module sources**: Local (`./modules/name`), Git (`git::https://...`), Registry (`azurerm/storage-account/azurerm`)

---

## State Management

**State file**: `terraform.tfstate` - Tracks resource mapping, metadata, dependencies

**Backend configuration** (remote state):
```hcl
terraform {
  backend "azurerm" {
    resource_group_name  = "rg-terraform-state"
    storage_account_name = "stterraformstate"
    container_name       = "tfstate"
    key                  = "prod.terraform.tfstate"
  }
}
```

**Backend types**: `azurerm` (Azure Storage), `s3` (AWS), `gcs` (GCP), `remote` (Terraform Cloud), `local` (default)

**State commands**: `terraform state list`, `terraform state show resource.type.name`, `terraform state mv`, `terraform state rm`, `terraform import`

**State locking**: Automatic with remote backends

**Workspaces**:
```hcl
terraform workspace new prod
terraform workspace select prod
# In config: name = "rg-${terraform.workspace}-${var.project}"
```

---

## Azure Provider Configuration

**Provider block**:
```hcl
terraform {
  required_version = ">= 1.0"
  required_providers {
    azurerm = {
      source  = "hashicorp/azurerm"
      version = "~> 3.0"
    }
  }
}
provider "azurerm" {
  features {
    resource_group {
      prevent_deletion_if_contains_resources = false
    }
    key_vault {
      purge_soft_delete_on_destroy = true
    }
  }
  # Auth: Azure CLI (az login), Managed Identity, Service Principal, Env vars
}
```

**Multiple providers** (subscriptions):
```hcl
provider "azurerm" {
  alias           = "production"
  subscription_id = var.prod_subscription_id
}
resource "azurerm_resource_group" "prod" {
  provider = azurerm.production
}
```

**Service Principal**:
```hcl
provider "azurerm" {
  client_id       = var.client_id
  client_secret   = var.client_secret
  tenant_id       = var.tenant_id
  subscription_id = var.subscription_id
}
```

---

## Common Azure Resources

**Resource Group**:
```hcl
resource "azurerm_resource_group" "example" {
  name     = "rg-${var.env}-${var.project}"
  location = var.location
  tags     = local.tags
}
```

**Storage Account**:
```hcl
resource "azurerm_storage_account" "example" {
  name                     = "st${replace(var.project, "-", "")}${var.env}"
  resource_group_name      = azurerm_resource_group.example.name
  location                 = azurerm_resource_group.example.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
  min_tls_version          = "TLS1_2"
  tags                     = local.tags
}
resource "azurerm_storage_container" "example" {
  name                  = "container"
  storage_account_name  = azurerm_storage_account.example.name
  container_access_type = "private"
}
```

**App Service Plan**:
```hcl
resource "azurerm_service_plan" "example" {
  name                = "asp-${var.project}-${var.env}"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  os_type             = "Linux"
  sku_name            = "B1"
}
```

**App Service**:
```hcl
resource "azurerm_linux_web_app" "example" {
  name                = "app-${var.project}-${var.env}"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_service_plan.example.location
  service_plan_id     = azurerm_service_plan.example.id
  site_config {
    application_stack {
      dotnet_version = "8.0"
    }
    always_on = true
  }
  app_settings = {
    "APPINSIGHTS_KEY" = azurerm_application_insights.example.instrumentation_key
  }
  tags = local.tags
}
```

**Key Vault**:
```hcl
resource "azurerm_key_vault" "example" {
  name                = "kv-${var.project}-${var.env}"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  tenant_id           = data.azurerm_client_config.current.tenant_id
  sku_name            = "standard"
  access_policy {
    tenant_id = data.azurerm_client_config.current.tenant_id
    object_id = data.azurerm_client_config.current.object_id
    secret_permissions = ["Get", "List", "Set", "Delete"]
  }
  network_acls {
    default_action = "Deny"
    bypass         = "AzureServices"
  }
  tags = local.tags
}
resource "azurerm_key_vault_secret" "example" {
  name         = "connection-string"
  value        = azurerm_storage_account.example.primary_connection_string
  key_vault_id = azurerm_key_vault.example.id
}
```

**Application Insights**:
```hcl
resource "azurerm_application_insights" "example" {
  name                = "appi-${var.project}-${var.env}"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  application_type    = "web"
  tags                = local.tags
}
```

---

## Azure Networking

**Virtual Network**:
```hcl
resource "azurerm_virtual_network" "example" {
  name                = "vnet-${var.project}-${var.env}"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  address_space       = ["10.0.0.0/16"]
  tags                = local.tags
}
```

**Subnet**:
```hcl
resource "azurerm_subnet" "example" {
  name                 = "snet-${var.project}-app"
  resource_group_name  = azurerm_resource_group.example.name
  virtual_network_name = azurerm_virtual_network.example.name
  address_prefixes     = ["10.0.1.0/24"]
  delegation {
    name = "Microsoft.Web.serverFarms"
    service_delegation {
      name    = "Microsoft.Web/serverFarms"
      actions = ["Microsoft.Network/virtualNetworks/subnets/action"]
    }
  }
}
```

**Network Security Group**:
```hcl
resource "azurerm_network_security_group" "example" {
  name                = "nsg-${var.project}-app"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  security_rule {
    name                       = "AllowHTTPS"
    priority                   = 100
    direction                  = "Inbound"
    access                     = "Allow"
    protocol                   = "Tcp"
    source_port_range           = "*"
    destination_port_range     = "443"
    source_address_prefix      = "*"
    destination_address_prefix = "*"
  }
  tags = local.tags
}
resource "azurerm_subnet_network_security_group_association" "example" {
  subnet_id                 = azurerm_subnet.example.id
  network_security_group_id = azurerm_network_security_group.example.id
}
```

**Public IP**:
```hcl
resource "azurerm_public_ip" "example" {
  name                = "pip-${var.project}-${var.env}"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  allocation_method   = "Static"
  sku                 = "Standard"
  tags                = local.tags
}
```

**Load Balancer**:
```hcl
resource "azurerm_lb" "example" {
  name                = "lb-${var.project}-${var.env}"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  sku                 = "Standard"
  frontend_ip_configuration {
    name                 = "PublicIPAddress"
    public_ip_address_id = azurerm_public_ip.example.id
  }
  tags = local.tags
}
```

**Private Endpoint**:
```hcl
resource "azurerm_private_endpoint" "example" {
  name                = "pe-${var.project}-storage"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  subnet_id           = azurerm_subnet.example.id
  private_service_connection {
    name                           = "psc-storage"
    private_connection_resource_id = azurerm_storage_account.example.id
    subresource_names              = ["blob"]
    is_manual_connection           = false
  }
  tags = local.tags
}
```

---

## Azure Compute

**Virtual Machine**:
```hcl
resource "azurerm_linux_virtual_machine" "example" {
  name                = "vm-${var.project}-${var.env}"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  size                = "Standard_B2s"
  admin_username      = "adminuser"
  network_interface_ids = [azurerm_network_interface.example.id]
  admin_ssh_key {
    username   = "adminuser"
    public_key = file("~/.ssh/id_rsa.pub")
  }
  os_disk {
    caching              = "ReadWrite"
    storage_account_type = "Standard_LRS"
  }
  source_image_reference {
    publisher = "Canonical"
    offer     = "0001-com-ubuntu-server-jammy"
    sku       = "22_04-lts"
    version   = "latest"
  }
  tags = local.tags
}
```

**Virtual Machine Scale Set**:
```hcl
resource "azurerm_linux_virtual_machine_scale_set" "example" {
  name                = "vmss-${var.project}-${var.env}"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  sku                 = "Standard_B2s"
  instances           = 2
  admin_username      = "adminuser"
  admin_ssh_key {
    username   = "adminuser"
    public_key = file("~/.ssh/id_rsa.pub")
  }
  network_interface {
    name    = "nic"
    primary = true
    ip_configuration {
      name      = "internal"
      primary   = true
      subnet_id = azurerm_subnet.example.id
    }
  }
  os_disk {
    caching              = "ReadWrite"
    storage_account_type = "Standard_LRS"
  }
  tags = local.tags
}
```

**Container Instances**:
```hcl
resource "azurerm_container_group" "example" {
  name                = "aci-${var.project}-${var.env}"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  os_type             = "Linux"
  ip_address_type     = "Public"
  dns_name_label      = "aci-${var.project}"
  container {
    name   = "app"
    image  = "nginx:latest"
    cpu    = "0.5"
    memory = "1.5"
    ports {
      port     = 80
      protocol = "TCP"
    }
  }
  tags = local.tags
}
```

---

## Azure Storage & Data

**Storage Blob Container**:
```hcl
resource "azurerm_storage_container" "data" {
  name                  = "data"
  storage_account_name  = azurerm_storage_account.example.name
  container_access_type = "private"
}
resource "azurerm_storage_blob" "example" {
  name                   = "file.txt"
  storage_account_name   = azurerm_storage_account.example.name
  storage_container_name = azurerm_storage_container.data.name
  type                   = "Block"
  source                 = "local-file.txt"
}
```

**SQL Database**:
```hcl
resource "azurerm_mssql_server" "example" {
  name                         = "sql-${var.project}-${var.env}"
  resource_group_name          = azurerm_resource_group.example.name
  location                     = azurerm_resource_group.example.location
  version                      = "12.0"
  administrator_login          = var.sql_admin_login
  administrator_login_password = var.sql_admin_password
  azuread_administrator {
    login_username = "AzureAD Admin"
    object_id      = data.azurerm_client_config.current.object_id
  }
  tags = local.tags
}
resource "azurerm_mssql_database" "example" {
  name           = "sqldb-${var.project}"
  server_id      = azurerm_mssql_server.example.id
  collation      = "SQL_Latin1_General_CP1_CI_AS"
  license_type   = "LicenseIncluded"
  max_size_gb    = 2
  sku_name       = "Basic"
  tags           = local.tags
}
resource "azurerm_mssql_firewall_rule" "example" {
  name             = "AllowAzureServices"
  server_id        = azurerm_mssql_server.example.id
  start_ip_address = "0.0.0.0"
  end_ip_address   = "0.0.0.0"
}
```

**Cosmos DB**:
```hcl
resource "azurerm_cosmosdb_account" "example" {
  name                = "cosmos-${var.project}-${var.env}"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  offer_type          = "Standard"
  kind                = "GlobalDocumentDB"
  consistency_policy {
    consistency_level = "Session"
  }
  geo_location {
    location          = azurerm_resource_group.example.location
    failover_priority = 0
  }
  tags = local.tags
}
resource "azurerm_cosmosdb_sql_database" "example" {
  name                = "cosmosdb-${var.project}"
  resource_group_name = azurerm_resource_group.example.name
  account_name        = azurerm_cosmosdb_account.example.name
}
resource "azurerm_cosmosdb_sql_container" "example" {
  name                = "container"
  resource_group_name = azurerm_resource_group.example.name
  account_name        = azurerm_cosmosdb_account.example.name
  database_name       = azurerm_cosmosdb_sql_database.example.name
  partition_key_path  = "/id"
}
```

**Data Factory**:
```hcl
resource "azurerm_data_factory" "example" {
  name                = "adf-${var.project}-${var.env}"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  identity {
    type = "SystemAssigned"
  }
  tags = local.tags
}
```

---

## Azure Identity & Security

**User-Assigned Managed Identity**:
```hcl
resource "azurerm_user_assigned_identity" "example" {
  name                = "id-${var.project}-${var.env}"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  tags                = local.tags
}
```

**Role Assignment**:
```hcl
resource "azurerm_role_assignment" "example" {
  scope                = azurerm_storage_account.example.id
  role_definition_name = "Storage Blob Data Contributor"
  principal_id         = azurerm_user_assigned_identity.example.principal_id
}
```

**Azure AD Application**:
```hcl
resource "azuread_application" "example" {
  display_name = "app-${var.project}-${var.env}"
}
resource "azuread_service_principal" "example" {
  application_id = azuread_application.example.application_id
}
resource "azuread_service_principal_password" "example" {
  service_principal_id = azuread_service_principal.example.id
}
```

---

## Azure Integration Services

**Service Bus**:
```hcl
resource "azurerm_servicebus_namespace" "example" {
  name                = "sb-${var.project}-${var.env}"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  sku                 = "Standard"
  tags                = local.tags
}
resource "azurerm_servicebus_queue" "example" {
  name         = "queue-${var.project}"
  namespace_id = azurerm_servicebus_namespace.example.id
}
```

**Event Hub**:
```hcl
resource "azurerm_eventhub_namespace" "example" {
  name                = "eh-${var.project}-${var.env}"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  sku                 = "Standard"
  capacity            = 1
  tags                = local.tags
}
resource "azurerm_eventhub" "example" {
  name                = "hub-${var.project}"
  namespace_name      = azurerm_eventhub_namespace.example.name
  resource_group_name = azurerm_resource_group.example.name
  partition_count     = 2
  message_retention   = 1
}
```

**API Management**:
```hcl
resource "azurerm_api_management" "example" {
  name                = "apim-${var.project}-${var.env}"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  publisher_name      = "Example"
  publisher_email     = "admin@example.com"
  sku_name            = "Developer_1"
  tags                = local.tags
}
```

**Function App**:
```hcl
resource "azurerm_storage_account" "functions" {
  name                     = "stfunc${replace(var.project, "-", "")}"
  resource_group_name      = azurerm_resource_group.example.name
  location                 = azurerm_resource_group.example.location
  account_tier             = "Standard"
  account_replication_type = "LRS"
}
resource "azurerm_service_plan" "functions" {
  name                = "asp-func-${var.project}"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  os_type             = "Linux"
  sku_name            = "Y1"  # Consumption plan
}
resource "azurerm_linux_function_app" "example" {
  name                = "func-${var.project}-${var.env}"
  resource_group_name = azurerm_resource_group.example.name
  location            = azurerm_resource_group.example.location
  service_plan_id     = azurerm_service_plan.functions.id
  storage_account_name       = azurerm_storage_account.functions.name
  storage_account_access_key = azurerm_storage_account.functions.primary_access_key
  site_config {
    application_stack {
      dotnet_version = "8.0"
    }
  }
  tags = local.tags
}
```

---

## Best Practices

**File organization**: `main.tf` (core resources), `variables.tf`, `outputs.tf`, `terraform.tfvars`, `providers.tf`, `backend.tf`, `modules/` (reusable modules)

**Naming conventions**: `rg-{env}-{project}`, `st{project}{env}`, `kv-{project}-{env}`, `vnet-{project}-{env}` - Use lowercase, hyphens, consistent prefixes

**Tags**: Always include `Environment`, `Project`, `ManagedBy = "Terraform"`, `CostCenter` - Use locals for common tags

**State management**: Use remote backend (Azure Storage), enable state locking, separate state per environment, never commit state files

**Variables**: Use validation blocks, provide descriptions, set sensible defaults, use `sensitive = true` for secrets

**Modules**: Keep modules focused, use versioning, document inputs/outputs, test independently

**Resource dependencies**: Use implicit dependencies (references), explicit `depends_on` only when needed, avoid circular dependencies

**Data sources**: Use data sources for existing resources, avoid hardcoding resource names/IDs

**Workspaces**: Use for environment separation (dev/staging/prod), or separate state files per environment

**Import existing resources**: `terraform import azurerm_resource_group.example /subscriptions/.../resourceGroups/rg-example`

**Plan before apply**: Always run `terraform plan` before `terraform apply`, review changes, use `-out=plan.tfplan` and `terraform apply plan.tfplan`

**Version constraints**: Pin provider versions (`~> 3.0`), specify Terraform version requirement

**Security**: Store secrets in Key Vault, use Managed Identity, enable private endpoints, restrict network access, use least privilege for role assignments

**Cost optimization**: Use appropriate SKUs, enable auto-shutdown for VMs, use consumption plans where possible, review and remove unused resources

**CI/CD integration**: Run `terraform fmt -check`, `terraform validate`, `terraform plan` in pipelines, use service principals for authentication, store state remotely

---

> **Note**: Terraform is declarative infrastructure as code - describe desired state, Terraform plans and applies changes. Use Azure provider (`hashicorp/azurerm`) for Azure resources. Best practices: remote state backend (Azure Storage), modules for reusability, tags for organization, Managed Identity for authentication, separate state per environment. Always plan before apply, use version constraints, and follow Azure naming conventions. Terraform state tracks resource mapping - never edit manually, use `terraform import` for existing resources.
