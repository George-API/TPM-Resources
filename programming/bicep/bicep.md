# Bicep for Azure Infrastructure as Code

**Focus**: Bicep DSL syntax, Azure resource deployment, modules, parameters, outputs, and enterprise IaC patterns.

## Table of Contents

- [Core Syntax & Bicep Language](#core-syntax--bicep-language)
- [Parameters & Outputs](#parameters--outputs)
- [Resources & Modules](#resources--modules)
- [Deployment & State](#deployment--state)
- [Azure Resource Examples](#azure-resource-examples)
- [Azure Networking](#azure-networking)
- [Azure Compute](#azure-compute)
- [Azure Storage & Data](#azure-storage--data)
- [Azure Identity & Security](#azure-identity--security)
- [Azure Integration Services](#azure-integration-services)
- [Best Practices](#best-practices)

---

## Core Syntax & Bicep Language

**Bicep**: Domain-specific language (DSL) for Azure - compiles to ARM JSON templates, declarative infrastructure

**Blocks**: `resource`, `module`, `param`, `var`, `output`, `targetScope`

**Target scopes**: `resourceGroup` (default), `subscription`, `managementGroup`, `tenant`

**Comments**: `// single line`, `/* multi-line */`

**Interpolation**: `'${param.name}'` (string interpolation), `resource.id` (resource references)

**Functions**: `resourceGroup()`, `subscription()`, `managementGroup()`, `deployment()`, `uniqueString()`, `concat()`, `toLower()`, `toUpper()`, `length()`, `split()`, `join()`, `replace()`, `substring()`, `base64()`, `base64ToString()`, `json()`, `jsonParse()`, `dateTimeAdd()`, `utcNow()`

**Conditionals**: `condition ? trueValue : falseValue` (ternary), `if (condition) { }`

**Loops**: `@batchSize(int)`, `for item in items: { }` (resource/module loops)

**Logical operators**: `&&`, `||`, `!`, `==`, `!=`, `>=`, `<=`, `>`, `<`

**Null coalescing**: `value ?? defaultValue`

---

## Parameters & Outputs

**Parameters**:
```bicep
@description('Resource name prefix')
@allowed(['dev', 'staging', 'prod'])
param environment string = 'dev'

@description('Location for resources')
param location string = resourceGroup().location

@description('Tags object')
param tags object = {
  Environment: environment
  ManagedBy: 'Bicep'
}

@description('Storage account name')
@minLength(3)
@maxLength(24)
param storageAccountName string

@description('Enable feature flag')
param enableFeature bool = false

@description('Array of subnet configurations')
param subnets array = [
  { name: 'app', addressPrefix: '10.0.1.0/24' }
  { name: 'data', addressPrefix: '10.0.2.0/24' }
]
```

**Parameter decorators**: `@description()`, `@allowed()`, `@secure()`, `@minLength()`, `@maxLength()`, `@minValue()`, `@maxValue()`, `@metadata()`

**Parameter types**: `string`, `int`, `bool`, `object`, `array`, `secureString`, `secureObject`

**Variables**:
```bicep
var storageAccountName = 'st${uniqueString(resourceGroup().id)}${environment}'
var tags = {
  Environment: environment
  Project: projectName
  ManagedBy: 'Bicep'
}
var subnetConfigs = [
  { name: 'app', addressPrefix: '10.0.1.0/24' }
  { name: 'data', addressPrefix: '10.0.2.0/24' }
]
```

**Outputs**:
```bicep
output storageAccountId string = storageAccount.id
output storageAccountName string = storageAccount.name
output primaryEndpoint object = {
  blob: storageAccount.properties.primaryEndpoints.blob
  queue: storageAccount.properties.primaryEndpoints.queue
}
```

---

## Resources & Modules

**Resource definition**:
```bicep
resource storageAccount 'Microsoft.Storage/storageAccounts@2023-01-01' = {
  name: storageAccountName
  location: location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
  properties: {
    supportsHttpsTrafficOnly: true
    minimumTlsVersion: 'TLS1_2'
  }
  tags: tags
}
```

**Resource references**: `resource.id`, `resource.name`, `resource.properties.attribute`

**Conditional resources**:
```bicep
resource keyVault 'Microsoft.KeyVault/vaults@2023-07-01' = if (enableKeyVault) {
  name: keyVaultName
  location: location
  properties: {
    // ...
  }
}
```

**Resource loops**:
```bicep
resource storageAccounts 'Microsoft.Storage/storageAccounts@2023-01-01' = [for item in range(0, 3): {
  name: 'st${uniqueString(resourceGroup().id)}${item}'
  location: location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
}]
```

**Modules**:
```bicep
// modules/storage-account/main.bicep
@description('Storage account name')
param storageAccountName string

@description('Resource group name')
param resourceGroupName string

@description('Location')
param location string

@description('Tags')
param tags object

resource storageAccount 'Microsoft.Storage/storageAccounts@2023-01-01' = {
  name: storageAccountName
  location: location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
  tags: tags
}

output storageAccountId string = storageAccount.id
```

**Module usage**:
```bicep
module storageModule 'modules/storage-account/main.bicep' = {
  name: 'storageDeployment'
  params: {
    storageAccountName: storageAccountName
    resourceGroupName: resourceGroup().name
    location: location
    tags: tags
  }
}

output storageAccountId string = storageModule.outputs.storageAccountId
```

**Module sources**: Local (`./modules/name`), Registry (`br:registry.azurecr.io/bicep/modules/storage:v1`), Template Spec (`ts:subscription/resourceGroup/templateSpec:version`)

---

## Deployment & State

**Deployment commands**:
```bash
# Validate
az deployment group validate --resource-group rg-example --template-file main.bicep --parameters @parameters.json

# What-if (preview changes)
az deployment group what-if --resource-group rg-example --template-file main.bicep --parameters @parameters.json

# Deploy
az deployment group create --resource-group rg-example --template-file main.bicep --parameters @parameters.json

# Deploy with parameter file
az deployment group create --resource-group rg-example --template-file main.bicep --parameters environment=prod location=eastus
```

**State management**: Bicep uses Azure Resource Manager (ARM) - no separate state file like Terraform. State is managed by Azure Resource Manager.

**Deployment scopes**:
- **Resource group**: `az deployment group create`
- **Subscription**: `az deployment sub create`
- **Management group**: `az deployment mg create`
- **Tenant**: `az deployment tenant create`

**Deployment modes**: `Incremental` (default, add/update resources), `Complete` (replace resource group contents)

**Deployment history**: Tracked in Azure Portal, can view/rollback deployments

---

## Azure Resource Examples

**Resource Group**:
```bicep
targetScope = 'subscription'

resource resourceGroup 'Microsoft.Resources/resourceGroups@2023-07-01' = {
  name: 'rg-${environment}-${projectName}'
  location: location
  tags: tags
}
```

**Storage Account**:
```bicep
resource storageAccount 'Microsoft.Storage/storageAccounts@2023-01-01' = {
  name: storageAccountName
  location: location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
  properties: {
    supportsHttpsTrafficOnly: true
    minimumTlsVersion: 'TLS1_2'
    allowBlobPublicAccess: false
  }
  tags: tags
}

resource blobContainer 'Microsoft.Storage/storageAccounts/blobServices/containers@2023-01-01' = {
  parent: storageAccount::blobServices
  name: 'data'
  properties: {
    publicAccess: 'None'
  }
}
```

**App Service Plan**:
```bicep
resource appServicePlan 'Microsoft.Web/serverfarms@2023-01-01' = {
  name: 'asp-${projectName}-${environment}'
  location: location
  kind: 'linux'
  sku: {
    name: 'B1'
    tier: 'Basic'
  }
  tags: tags
}
```

**App Service**:
```bicep
resource webApp 'Microsoft.Web/sites@2023-01-01' = {
  name: 'app-${projectName}-${environment}'
  location: location
  kind: 'app,linux'
  properties: {
    serverFarmId: appServicePlan.id
    siteConfig: {
      linuxFxVersion: 'DOTNETCORE|8.0'
      alwaysOn: true
      appSettings: [
        {
          name: 'APPINSIGHTS_KEY'
          value: appInsights.properties.InstrumentationKey
        }
      ]
    }
  }
  identity: {
    type: 'SystemAssigned'
  }
  tags: tags
}
```

**Key Vault**:
```bicep
resource keyVault 'Microsoft.KeyVault/vaults@2023-07-01' = {
  name: keyVaultName
  location: location
  properties: {
    tenantId: subscription().tenantId
    sku: {
      family: 'A'
      name: 'standard'
    }
    accessPolicies: [
      {
        tenantId: subscription().tenantId
        objectId: currentUserObjectId
        permissions: {
          secrets: ['get', 'list', 'set', 'delete']
          keys: ['get', 'list']
        }
      }
    ]
    networkAcls: {
      defaultAction: 'Deny'
      bypass: 'AzureServices'
    }
  }
  tags: tags
}

resource keyVaultSecret 'Microsoft.KeyVault/vaults/secrets@2023-07-01' = {
  parent: keyVault
  name: 'connection-string'
  properties: {
    value: storageAccount.properties.primaryEndpoints.blob
  }
}
```

**Application Insights**:
```bicep
resource appInsights 'Microsoft.Insights/components@2020-02-02' = {
  name: 'appi-${projectName}-${environment}'
  location: location
  kind: 'web'
  properties: {
    Application_Type: 'web'
    Request_Source: 'rest'
  }
  tags: tags
}
```

---

## Azure Networking

**Virtual Network**:
```bicep
resource virtualNetwork 'Microsoft.Network/virtualNetworks@2023-05-01' = {
  name: 'vnet-${projectName}-${environment}'
  location: location
  properties: {
    addressSpace: {
      addressPrefixes: ['10.0.0.0/16']
    }
    subnets: [
      {
        name: 'app'
        properties: {
          addressPrefix: '10.0.1.0/24'
          delegations: [
            {
              name: 'Microsoft.Web.serverFarms'
              properties: {
                serviceName: 'Microsoft.Web/serverFarms'
              }
            }
          ]
        }
      }
      {
        name: 'data'
        properties: {
          addressPrefix: '10.0.2.0/24'
        }
      }
    ]
  }
  tags: tags
}
```

**Network Security Group**:
```bicep
resource networkSecurityGroup 'Microsoft.Network/networkSecurityGroups@2023-05-01' = {
  name: 'nsg-${projectName}-app'
  location: location
  properties: {
    securityRules: [
      {
        name: 'AllowHTTPS'
        properties: {
          priority: 100
          direction: 'Inbound'
          access: 'Allow'
          protocol: 'Tcp'
          sourcePortRange: '*'
          destinationPortRange: '443'
          sourceAddressPrefix: '*'
          destinationAddressPrefix: '*'
        }
      }
    ]
  }
  tags: tags
}

resource subnetNsgAssociation 'Microsoft.Network/virtualNetworks/subnets@2023-05-01' = {
  parent: virtualNetwork
  name: 'app'
  properties: {
    addressPrefix: '10.0.1.0/24'
    networkSecurityGroup: {
      id: networkSecurityGroup.id
    }
  }
}
```

**Public IP**:
```bicep
resource publicIP 'Microsoft.Network/publicIPAddresses@2023-05-01' = {
  name: 'pip-${projectName}-${environment}'
  location: location
  sku: {
    name: 'Standard'
    tier: 'Regional'
  }
  properties: {
    publicIPAllocationMethod: 'Static'
    publicIPAddressVersion: 'IPv4'
  }
  tags: tags
}
```

**Load Balancer**:
```bicep
resource loadBalancer 'Microsoft.Network/loadBalancers@2023-05-01' = {
  name: 'lb-${projectName}-${environment}'
  location: location
  sku: {
    name: 'Standard'
    tier: 'Regional'
  }
  properties: {
    frontendIPConfigurations: [
      {
        name: 'PublicIPAddress'
        properties: {
          publicIPAddress: {
            id: publicIP.id
          }
        }
      }
    ]
  }
  tags: tags
}
```

**Private Endpoint**:
```bicep
resource privateEndpoint 'Microsoft.Network/privateEndpoints@2023-05-01' = {
  name: 'pe-${projectName}-storage'
  location: location
  properties: {
    subnet: {
      id: virtualNetwork::subnets('data').id
    }
    privateLinkServiceConnections: [
      {
        name: 'psc-storage'
        properties: {
          privateLinkServiceId: storageAccount.id
          groupIds: ['blob']
        }
      }
    ]
  }
  tags: tags
}
```

---

## Azure Compute

**Virtual Machine**:
```bicep
resource networkInterface 'Microsoft.Network/networkInterfaces@2023-05-01' = {
  name: 'nic-${projectName}-${environment}'
  location: location
  properties: {
    ipConfigurations: [
      {
        name: 'ipconfig1'
        properties: {
          subnet: {
            id: virtualNetwork::subnets('app').id
          }
          privateIPAllocationMethod: 'Dynamic'
        }
      }
    ]
  }
  tags: tags
}

resource virtualMachine 'Microsoft.Compute/virtualMachines@2023-03-01' = {
  name: 'vm-${projectName}-${environment}'
  location: location
  properties: {
    hardwareProfile: {
      vmSize: 'Standard_B2s'
    }
    osProfile: {
      computerName: 'vm-${projectName}'
      adminUsername: adminUsername
      adminPassword: adminPassword
      linuxConfiguration: {
        disablePasswordAuthentication: false
      }
    }
    storageProfile: {
      imageReference: {
        publisher: 'Canonical'
        offer: '0001-com-ubuntu-server-jammy'
        sku: '22_04-lts'
        version: 'latest'
      }
      osDisk: {
        createOption: 'FromImage'
        caching: 'ReadWrite'
        managedDisk: {
          storageAccountType: 'Standard_LRS'
        }
      }
    }
    networkProfile: {
      networkInterfaces: [
        {
          id: networkInterface.id
        }
      ]
    }
  }
  tags: tags
}
```

**Virtual Machine Scale Set**:
```bicep
resource vmss 'Microsoft.Compute/virtualMachineScaleSets@2023-03-01' = {
  name: 'vmss-${projectName}-${environment}'
  location: location
  sku: {
    name: 'Standard_B2s'
    capacity: 2
  }
  properties: {
    upgradePolicy: {
      mode: 'Manual'
    }
    virtualMachineProfile: {
      osProfile: {
        computerNamePrefix: 'vm'
        adminUsername: adminUsername
        adminPassword: adminPassword
        linuxConfiguration: {
          disablePasswordAuthentication: false
        }
      }
      storageProfile: {
        imageReference: {
          publisher: 'Canonical'
          offer: '0001-com-ubuntu-server-jammy'
          sku: '22_04-lts'
          version: 'latest'
        }
        osDisk: {
          createOption: 'FromImage'
          caching: 'ReadWrite'
          managedDisk: {
            storageAccountType: 'Standard_LRS'
          }
        }
      }
      networkProfile: {
        networkInterfaceConfigurations: [
          {
            name: 'nic'
            properties: {
              primary: true
              ipConfigurations: [
                {
                  name: 'internal'
                  properties: {
                    subnet: {
                      id: virtualNetwork::subnets('app').id
                    }
                  }
                }
              ]
            }
          }
        ]
      }
    }
  }
  tags: tags
}
```

**Container Instances**:
```bicep
resource containerGroup 'Microsoft.ContainerInstance/containerGroups@2023-05-01' = {
  name: 'aci-${projectName}-${environment}'
  location: location
  properties: {
    containers: [
      {
        name: 'app'
        properties: {
          image: 'nginx:latest'
          resources: {
            requests: {
              cpu: 0.5
              memoryInGb: 1.5
            }
          }
          ports: [
            {
              port: 80
              protocol: 'TCP'
            }
          ]
        }
      }
    ]
    osType: 'Linux'
    ipAddress: {
      type: 'Public'
      dnsNameLabel: 'aci-${projectName}'
      ports: [
        {
          port: 80
          protocol: 'TCP'
        }
      ]
    }
  }
  tags: tags
}
```

---

## Azure Storage & Data

**SQL Database**:
```bicep
resource sqlServer 'Microsoft.Sql/servers@2023-05-01-preview' = {
  name: 'sql-${projectName}-${environment}'
  location: location
  properties: {
    administratorLogin: sqlAdminLogin
    administratorLoginPassword: sqlAdminPassword
    version: '12.0'
    minimalTlsVersion: '1.2'
  }
  tags: tags
}

resource sqlDatabase 'Microsoft.Sql/servers/databases@2023-05-01-preview' = {
  parent: sqlServer
  name: 'sqldb-${projectName}'
  properties: {
    collation: 'SQL_Latin1_General_CP1_CI_AS'
    maxSizeBytes: 2147483648
    requestedServiceObjectiveName: 'Basic'
  }
  tags: tags
}

resource sqlFirewallRule 'Microsoft.Sql/servers/firewallRules@2023-05-01-preview' = {
  parent: sqlServer
  name: 'AllowAzureServices'
  properties: {
    startIpAddress: '0.0.0.0'
    endIpAddress: '0.0.0.0'
  }
}
```

**Cosmos DB**:
```bicep
resource cosmosAccount 'Microsoft.DocumentDB/databaseAccounts@2023-09-15' = {
  name: 'cosmos-${projectName}-${environment}'
  location: location
  kind: 'GlobalDocumentDB'
  properties: {
    consistencyPolicy: {
      defaultConsistencyLevel: 'Session'
    }
    locations: [
      {
        locationName: location
        failoverPriority: 0
        isZoneRedundant: false
      }
    ]
    databaseAccountOfferType: 'Standard'
  }
  tags: tags
}

resource cosmosDatabase 'Microsoft.DocumentDB/databaseAccounts/sqlDatabases@2023-09-15' = {
  parent: cosmosAccount
  name: 'cosmosdb-${projectName}'
  properties: {
    resource: {
      id: 'cosmosdb-${projectName}'
    }
  }
}

resource cosmosContainer 'Microsoft.DocumentDB/databaseAccounts/sqlDatabases/containers@2023-09-15' = {
  parent: cosmosDatabase
  name: 'container'
  properties: {
    resource: {
      id: 'container'
      partitionKey: {
        paths: ['/id']
        kind: 'Hash'
      }
    }
  }
}
```

**Data Factory**:
```bicep
resource dataFactory 'Microsoft.DataFactory/factories@2018-06-01' = {
  name: 'adf-${projectName}-${environment}'
  location: location
  identity: {
    type: 'SystemAssigned'
  }
  tags: tags
}
```

---

## Azure Identity & Security

**User-Assigned Managed Identity**:
```bicep
resource userAssignedIdentity 'Microsoft.ManagedIdentity/userAssignedIdentities@2023-01-31' = {
  name: 'id-${projectName}-${environment}'
  location: location
  tags: tags
}
```

**Role Assignment**:
```bicep
resource roleAssignment 'Microsoft.Authorization/roleAssignments@2022-04-01' = {
  name: guid(resourceGroup().id, userAssignedIdentity.id, 'Storage Blob Data Contributor')
  scope: storageAccount
  properties: {
    roleDefinitionId: subscriptionResourceId('Microsoft.Authorization/roleDefinitions', 'ba92f5b4-2d11-453d-a403-e96b0029c9fe')
    principalId: userAssignedIdentity.properties.principalId
    principalType: 'ServicePrincipal'
  }
}
```

**System-Assigned Managed Identity** (on resource):
```bicep
resource webApp 'Microsoft.Web/sites@2023-01-01' = {
  // ...
  identity: {
    type: 'SystemAssigned'
  }
}
```

---

## Azure Integration Services

**Service Bus**:
```bicep
resource serviceBusNamespace 'Microsoft.ServiceBus/namespaces@2022-10-01-preview' = {
  name: 'sb-${projectName}-${environment}'
  location: location
  sku: {
    name: 'Standard'
    tier: 'Standard'
  }
  tags: tags
}

resource serviceBusQueue 'Microsoft.ServiceBus/namespaces/queues@2022-10-01-preview' = {
  parent: serviceBusNamespace
  name: 'queue-${projectName}'
  properties: {
    maxSizeInMegabytes: 1024
    defaultMessageTimeToLive: 'PT1H'
  }
}
```

**Event Hub**:
```bicep
resource eventHubNamespace 'Microsoft.EventHub/namespaces@2023-01-01-preview' = {
  name: 'eh-${projectName}-${environment}'
  location: location
  sku: {
    name: 'Standard'
    tier: 'Standard'
    capacity: 1
  }
  tags: tags
}

resource eventHub 'Microsoft.EventHub/namespaces/eventhubs@2023-01-01-preview' = {
  parent: eventHubNamespace
  name: 'hub-${projectName}'
  properties: {
    partitionCount: 2
    messageRetentionInDays: 1
  }
}
```

**API Management**:
```bicep
resource apiManagement 'Microsoft.ApiManagement/service@2023-05-01-preview' = {
  name: 'apim-${projectName}-${environment}'
  location: location
  sku: {
    name: 'Developer'
    capacity: 1
  }
  properties: {
    publisherName: 'Example'
    publisherEmail: 'admin@example.com'
  }
  tags: tags
}
```

**Function App**:
```bicep
resource functionStorageAccount 'Microsoft.Storage/storageAccounts@2023-01-01' = {
  name: 'stfunc${uniqueString(resourceGroup().id)}'
  location: location
  kind: 'StorageV2'
  sku: {
    name: 'Standard_LRS'
  }
}

resource functionAppServicePlan 'Microsoft.Web/serverfarms@2023-01-01' = {
  name: 'asp-func-${projectName}'
  location: location
  kind: 'functionapp'
  sku: {
    name: 'Y1'
    tier: 'Dynamic'
  }
}

resource functionApp 'Microsoft.Web/sites@2023-01-01' = {
  name: 'func-${projectName}-${environment}'
  location: location
  kind: 'functionapp,linux'
  properties: {
    serverFarmId: functionAppServicePlan.id
    siteConfig: {
      appSettings: [
        {
          name: 'AzureWebJobsStorage'
          value: 'DefaultEndpointsProtocol=https;AccountName=${functionStorageAccount.name};AccountKey=${functionStorageAccount.listKeys().keys[0].value}'
        }
        {
          name: 'FUNCTIONS_EXTENSION_VERSION'
          value: '~4'
        }
        {
          name: 'FUNCTIONS_WORKER_RUNTIME'
          value: 'dotnet'
        }
      ]
      linuxFxVersion: 'DOTNETCORE|8.0'
    }
  }
  identity: {
    type: 'SystemAssigned'
  }
  tags: tags
}
```

---

## Best Practices

**File organization**: `main.bicep` (main template), `modules/` (reusable modules), `parameters/` (parameter files), `README.md` (documentation)

**Naming conventions**: `rg-{env}-{project}`, `st{project}{env}`, `kv-{project}-{env}`, `vnet-{project}-{env}` - Use lowercase, hyphens, consistent prefixes

**Tags**: Always include `Environment`, `Project`, `ManagedBy = "Bicep"`, `CostCenter` - Use variables/parameters for common tags

**Parameters**: Use decorators for validation (`@description()`, `@allowed()`, `@minLength()`, `@maxLength()`), provide defaults, use `@secure()` for secrets

**Modules**: Keep modules focused, use versioning (registry), document inputs/outputs, test independently

**Resource dependencies**: Use implicit dependencies (references), explicit `dependsOn` only when needed, avoid circular dependencies

**Deployment**: Always use `what-if` before deployment, validate templates, use parameter files, version control all Bicep files

**Version constraints**: Pin API versions for resources, use latest stable versions, test version upgrades

**Security**: Store secrets in Key Vault, use Managed Identity, enable private endpoints, restrict network access, use least privilege for role assignments

**Cost optimization**: Use appropriate SKUs, enable auto-shutdown for VMs, use consumption plans where possible, review and remove unused resources

**CI/CD integration**: Run `az bicep build`, `az deployment group validate`, `az deployment group what-if` in pipelines, use service principals for authentication

**State management**: Bicep uses ARM - no separate state file. Deployment history tracked in Azure Portal. Use deployment modes appropriately (Incremental vs Complete).

**Error handling**: Use `what-if` to preview changes, validate before deployment, handle deployment failures gracefully, use deployment scripts for complex scenarios

---

> **Note**: Bicep is Microsoft's domain-specific language for Azure - compiles to ARM JSON templates. Use Azure CLI (`az deployment group create`) or Azure PowerShell for deployment. Best practices: use modules for reusability, parameters for flexibility, tags for organization, Managed Identity for authentication, separate parameter files per environment. Always validate and use what-if before deployment. Bicep state is managed by Azure Resource Manager - no separate state file like Terraform.

