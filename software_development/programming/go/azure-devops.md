# Go for Azure Cloud Architecture & DevOps

**Focus**: Azure SDK usage, infrastructure automation, containerization, CI/CD, monitoring, and DevOps patterns in Go.

## Table of Contents

- [Azure SDK Setup](#azure-sdk-setup)
- [Authentication](#authentication)
- [Resource Management](#resource-management)
- [Storage Services](#storage-services)
- [Key Vault](#key-vault)
- [Infrastructure as Code](#infrastructure-as-code)
- [Containerization](#containerization)
- [CI/CD Patterns](#cicd-patterns)
- [Monitoring & Logging](#monitoring--logging)
- [DevOps Patterns](#devops-patterns)

---

## Azure SDK Setup

### Install Azure SDK Packages
```bash
go get github.com/Azure/azure-sdk-for-go/sdk/azcore
go get github.com/Azure/azure-sdk-for-go/sdk/azidentity
go get github.com/Azure/azure-sdk-for-go/sdk/resourcemanager/resources/armresources
go get github.com/Azure/azure-sdk-for-go/sdk/resourcemanager/compute/armcompute
go get github.com/Azure/azure-sdk-for-go/sdk/storage/azblob
go get github.com/Azure/azure-sdk-for-go/sdk/keyvault/azsecrets
```

### Common Imports
```go
import (
    "context"
    "github.com/Azure/azure-sdk-for-go/sdk/azcore/to"
    "github.com/Azure/azure-sdk-for-go/sdk/azidentity"
    "github.com/Azure/azure-sdk-for-go/sdk/resourcemanager/resources/armresources"
)
```

---

## Authentication

### Default Azure Credential
```go
import "github.com/Azure/azure-sdk-for-go/sdk/azidentity"

cred, err := azidentity.NewDefaultAzureCredential(nil)
// Tries: Environment → Managed Identity → Azure CLI → Azure PowerShell
```

### Client Secret Credential
```go
cred, err := azidentity.NewClientSecretCredential(tenantID, clientID, clientSecret, nil)
```

### Managed Identity (VMs, AKS, App Service)
```go
cred, err := azidentity.NewManagedIdentityCredential(nil)
```

### Certificate Credential
```go
cred, err := azidentity.NewClientCertificateCredential(tenantID, clientID, certificates, privateKey, nil)
```

### Workload Identity (Kubernetes)
```go
cred, err := azidentity.NewWorkloadIdentityCredential(&azidentity.WorkloadIdentityCredentialOptions{
    ClientID:      clientID,
    TenantID:      tenantID,
    TokenFilePath: "/var/run/secrets/azure/tokens/azure-identity-token",
})
```

---

## Resource Management

### Resource Groups
```go
import "github.com/Azure/azure-sdk-for-go/sdk/resourcemanager/resources/armresources"

client, err := armresources.NewResourceGroupsClient(subscriptionID, cred, nil)

// Create
resp, err := client.CreateOrUpdate(ctx, "rg-name", armresources.ResourceGroup{
    Location: to.Ptr("eastus"),
    Tags: map[string]*string{"environment": to.Ptr("production")},
}, nil)

// Get
rg, err := client.Get(ctx, "rg-name", nil)

// List
pager := client.NewListPager(nil)
for pager.More() {
    page, _ := pager.NextPage(ctx)
}

// Delete
pollResp, _ := client.BeginDelete(ctx, "rg-name", nil)
_, _ = pollResp.PollUntilDone(ctx, nil)
```

### Virtual Machines
```go
import "github.com/Azure/azure-sdk-for-go/sdk/resourcemanager/compute/armcompute"

vmClient, _ := armcompute.NewVirtualMachinesClient(subscriptionID, cred, nil)

vm := armcompute.VirtualMachine{
    Location: to.Ptr("eastus"),
    Properties: &armcompute.VirtualMachineProperties{
        HardwareProfile: &armcompute.HardwareProfile{
            VMSize: to.Ptr(armcompute.VirtualMachineSizeTypesStandardB1s),
        },
        StorageProfile: &armcompute.StorageProfile{
            ImageReference: &armcompute.ImageReference{
                Publisher: to.Ptr("Canonical"),
                Offer:     to.Ptr("UbuntuServer"),
                SKU:       to.Ptr("18.04-LTS"),
                Version:   to.Ptr("latest"),
            },
        },
        OSProfile: &armcompute.OSProfile{
            ComputerName:  to.Ptr("vm-name"),
            AdminUsername: to.Ptr("adminuser"),
            AdminPassword: to.Ptr("password"),
        },
    },
}

pollResp, _ := vmClient.BeginCreateOrUpdate(ctx, "rg-name", "vm-name", vm, nil)
_, _ = pollResp.PollUntilDone(ctx, nil)
```

### Network Resources
```go
import "github.com/Azure/azure-sdk-for-go/sdk/resourcemanager/network/armnetwork"

vnetClient, _ := armnetwork.NewVirtualNetworksClient(subscriptionID, cred, nil)

vnet := armnetwork.VirtualNetwork{
    Location: to.Ptr("eastus"),
    Properties: &armnetwork.VirtualNetworkPropertiesFormat{
        AddressSpace: &armnetwork.AddressSpace{
            AddressPrefixes: []*string{to.Ptr("10.0.0.0/16")},
        },
        Subnets: []*armnetwork.Subnet{{
            Name: to.Ptr("default"),
            Properties: &armnetwork.SubnetPropertiesFormat{
                AddressPrefix: to.Ptr("10.0.0.0/24"),
            },
        }},
    },
}

pollResp, _ := vnetClient.BeginCreateOrUpdate(ctx, "rg-name", "vnet-name", vnet, nil)
_, _ = pollResp.PollUntilDone(ctx, nil)
```

---

## Storage Services

### Blob Storage
```go
import "github.com/Azure/azure-sdk-for-go/sdk/storage/azblob"

serviceClient, _ := azblob.NewServiceClient(accountURL, cred, nil)
containerClient := serviceClient.NewContainerClient("container-name")
_, _ = containerClient.Create(ctx, nil)

blobClient := containerClient.NewBlockBlobClient("blob.txt")
_, _ = blobClient.UploadBuffer(ctx, []byte("Hello, Azure!"), nil)

downloadResp, _ := blobClient.DownloadStream(ctx, nil)
data, _ := io.ReadAll(downloadResp.Body)

pager := containerClient.NewListBlobsFlatPager(nil)
for pager.More() {
    page, _ := pager.NextPage(ctx)
    for _, blob := range page.Segment.BlobItems {
        // Process blob
    }
}
```

### Queue Storage
```go
import "github.com/Azure/azure-sdk-for-go/sdk/storage/azqueue"

serviceClient, _ := azqueue.NewServiceClient(accountURL, cred, nil)
queueClient := serviceClient.NewQueueClient("queue-name")
_, _ = queueClient.Create(ctx, nil)

_, _ = queueClient.EnqueueMessage(ctx, "message text", nil)

resp, _ := queueClient.DequeueMessages(ctx, nil)
for _, msg := range resp.Messages {
    _, _ = queueClient.DeleteMessage(ctx, *msg.MessageID, *msg.PopReceipt, nil)
}
```

---

## Key Vault

### Secrets
```go
import "github.com/Azure/azure-sdk-for-go/sdk/keyvault/azsecrets"

client, _ := azsecrets.NewClient(vaultURL, cred, nil)

_, _ = client.SetSecret(ctx, "secret-name", "secret-value", nil)

secret, _ := client.GetSecret(ctx, "secret-name", nil)
value := *secret.Value

pager := client.NewListSecretsPager(nil)
for pager.More() {
    page, _ := pager.NextPage(ctx)
}

_, _ = client.BeginDeleteSecret(ctx, "secret-name", nil)
```

### Certificates
```go
import "github.com/Azure/azure-sdk-for-go/sdk/keyvault/azcertificates"

certClient, _ := azcertificates.NewClient(vaultURL, cred, nil)
cert, _ := certClient.GetCertificate(ctx, "cert-name", nil)
```

---

## Infrastructure as Code

### Terraform Provider Development
```go
import "github.com/hashicorp/terraform-plugin-sdk/v2/helper/schema"

func resourceAzureVM() *schema.Resource {
    return &schema.Resource{
        Create: resourceAzureVMCreate,
        Read:   resourceAzureVMRead,
        Update: resourceAzureVMUpdate,
        Delete: resourceAzureVMDelete,
        Schema: map[string]*schema.Schema{
            "name":     {Type: schema.TypeString, Required: true},
            "location": {Type: schema.TypeString, Required: true},
        },
    }
}

func resourceAzureVMCreate(d *schema.ResourceData, m interface{}) error {
    d.SetId("vm-id")
    return nil
}
```

### Pulumi (Go)
```go
import (
    "github.com/pulumi/pulumi-azure/sdk/v5/go/azure/core"
    "github.com/pulumi/pulumi-azure/sdk/v5/go/azure/storage"
    "github.com/pulumi/pulumi/sdk/v3/go/pulumi"
)

func main() {
    pulumi.Run(func(ctx *pulumi.Context) error {
        rg, _ := core.NewResourceGroup(ctx, "rg", &core.ResourceGroupArgs{
            Location: pulumi.String("EastUS"),
        })

        account, _ := storage.NewAccount(ctx, "sa", &storage.AccountArgs{
            ResourceGroupName:      rg.Name,
            Location:               rg.Location,
            AccountTier:            pulumi.String("Standard"),
            AccountReplicationType: pulumi.String("LRS"),
        })

        ctx.Export("storageAccountName", account.Name)
        return nil
    })
}
```

---

## Containerization

### Dockerfile for Go
```dockerfile
FROM golang:1.21-alpine AS builder
WORKDIR /app
COPY go.mod go.sum ./
RUN go mod download
COPY . .
RUN CGO_ENABLED=0 GOOS=linux go build -o app .

FROM alpine:latest
RUN apk --no-cache add ca-certificates
WORKDIR /root/
COPY --from=builder /app/app .
CMD ["./app"]
```

### Docker Compose
```yaml
version: '3.8'
services:
  app:
    build: .
    ports: ["8080:8080"]
    environment:
      - AZURE_CLIENT_ID=${AZURE_CLIENT_ID}
      - AZURE_TENANT_ID=${AZURE_TENANT_ID}
```

### Kubernetes Client
```go
import (
    "k8s.io/client-go/kubernetes"
    "k8s.io/client-go/tools/clientcmd"
    metav1 "k8s.io/apimachinery/pkg/apis/meta/v1"
)

config, _ := clientcmd.BuildConfigFromFlags("", kubeconfig)
clientset, _ := kubernetes.NewForConfig(config)

pods, _ := clientset.CoreV1().Pods("default").List(ctx, metav1.ListOptions{})

deployment := &appsv1.Deployment{
    ObjectMeta: metav1.ObjectMeta{Name: "app-deployment"},
    Spec: appsv1.DeploymentSpec{Replicas: to.Ptr(int32(3))},
}
_, _ = clientset.AppsV1().Deployments("default").Create(ctx, deployment, metav1.CreateOptions{})
```

### Azure Kubernetes Service (AKS)
```go
import "github.com/Azure/azure-sdk-for-go/sdk/resourcemanager/containerservice/armcontainerservice"

aksClient, _ := armcontainerservice.NewManagedClustersClient(subscriptionID, cred, nil)

cluster := armcontainerservice.ManagedCluster{
    Location: to.Ptr("eastus"),
    Properties: &armcontainerservice.ManagedClusterProperties{
        AgentPoolProfiles: []*armcontainerservice.ManagedClusterAgentPoolProfile{{
            Name:   to.Ptr("agentpool"),
            Count:  to.Ptr(int32(3)),
            VMSize: to.Ptr("Standard_DS2_v2"),
        }},
        DNSPrefix: to.Ptr("aks-cluster"),
    },
}

pollResp, _ := aksClient.BeginCreateOrUpdate(ctx, "rg-name", "aks-cluster", cluster, nil)
_, _ = pollResp.PollUntilDone(ctx, nil)
```

---

## CI/CD Patterns

### GitHub Actions
```yaml
name: Go CI/CD
on:
  push: {branches: [main]}
  pull_request: {branches: [main]}

jobs:
  test:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-go@v4
        with: {go-version: '1.21'}
      - run: go test ./...
      - run: go vet ./...
      - run: golangci-lint run

  build:
    runs-on: ubuntu-latest
    needs: test
    steps:
      - uses: actions/checkout@v3
      - uses: actions/setup-go@v4
        with: {go-version: '1.21'}
      - run: go build -o app
      - uses: actions/upload-artifact@v3
        with: {name: app, path: app}

  deploy-azure:
    runs-on: ubuntu-latest
    needs: build
    steps:
      - uses: azure/login@v1
        with: {creds: ${{ secrets.AZURE_CREDENTIALS }}}
      - uses: actions/download-artifact@v3
        with: {name: app}
      - uses: azure/webapps-deploy@v2
        with: {app-name: 'app-name', package: './app'}
```

### Azure DevOps Pipeline
```yaml
trigger:
  branches: {include: [main]}

pool:
  vmImage: 'ubuntu-latest'

variables:
  goVersion: '1.21'

steps:
  - task: Go@0
    displayName: 'Install Go'
    inputs: {version: '$(goVersion)'}
  - task: Go@0
    displayName: 'Run Tests'
    inputs: {command: 'test', arguments: './...'}
  - task: Go@0
    displayName: 'Build'
    inputs: {command: 'build', arguments: '-o app'}
  - task: AzureWebApp@1
    displayName: 'Deploy to Azure'
    inputs:
      azureSubscription: 'subscription'
      appName: 'app-name'
      package: '$(System.DefaultWorkingDirectory)/app'
```

### Azure Container Registry
```go
import "github.com/Azure/azure-sdk-for-go/sdk/resourcemanager/containerregistry/armcontainerregistry"

acrClient, _ := armcontainerregistry.NewRegistriesClient(subscriptionID, cred, nil)

registry := armcontainerregistry.Registry{
    Location: to.Ptr("eastus"),
    SKU: &armcontainerregistry.SKU{Name: to.Ptr(armcontainerregistry.SKUNameBasic)},
    Properties: &armcontainerregistry.RegistryProperties{AdminUserEnabled: to.Ptr(true)},
}

pollResp, _ := acrClient.BeginCreate(ctx, "rg-name", "acr-name", registry, nil)
_, _ = pollResp.PollUntilDone(ctx, nil)
```

---

## Monitoring & Logging

### Application Insights
```go
import "github.com/microsoft/ApplicationInsights-Go/appinsights"

telemetryConfig := appinsights.NewTelemetryConfiguration("instrumentation-key")
telemetryClient := appinsights.NewTelemetryClientFromConfig(telemetryConfig)

telemetryClient.TrackEvent("CustomEvent", map[string]string{"resource": "vm-001", "action": "start"})
telemetryClient.TrackMetric("CustomMetric", 42.0, map[string]string{"unit": "count"})
telemetryClient.TrackTrace("Operation started", appinsights.Information)
telemetryClient.TrackException(err)
telemetryClient.Channel().Flush()
```

### Structured Logging (Logrus)
```go
import "github.com/sirupsen/logrus"

log := logrus.New()
log.SetFormatter(&logrus.JSONFormatter{})
log.SetLevel(logrus.InfoLevel)

log.WithFields(logrus.Fields{"resource": "vm-001", "action": "start", "region": "eastus"}).Info("VM started")
log.WithError(err).Error("Failed to start VM")
```

### Structured Logging (Zap)
```go
import "go.uber.org/zap"

logger, _ := zap.NewProduction()
defer logger.Sync()

logger.Info("VM started", zap.String("resource", "vm-001"), zap.String("action", "start"))
logger.Error("Failed to start VM", zap.Error(err))
```

### Azure Monitor Metrics
```go
import "github.com/Azure/azure-sdk-for-go/sdk/monitor/azmetrics"

metricsClient, _ := azmetrics.NewMetricsClient(cred, nil)

resp, _ := metricsClient.QueryResource(ctx, resourceURI, &azmetrics.QueryResourceOptions{
    Timespan:    to.Ptr("PT1H"),
    Interval:    to.Ptr("PT1M"),
    MetricNames: []string{"Percentage CPU"},
})
```

---

## DevOps Patterns

### Configuration Management
```go
import (
    "os"
    "github.com/caarlos0/env/v9"
)

port := os.Getenv("PORT")
if port == "" {
    port = "8080"
}

type Config struct {
    DatabaseURL   string `env:"DATABASE_URL" envDefault:"localhost"`
    Port          int    `env:"PORT" envDefault:"8080"`
    LogLevel      string `env:"LOG_LEVEL" envDefault:"info"`
    AzureClientID string `env:"AZURE_CLIENT_ID,required"`
    AzureTenantID string `env:"AZURE_TENANT_ID,required"`
}

var cfg Config
if err := env.Parse(&cfg); err != nil {
    log.Fatal(err)
}
```

### Health Checks
```go
import "net/http"

func healthHandler(w http.ResponseWriter, r *http.Request) {
    w.Header().Set("Content-Type", "application/json")
    w.WriteHeader(http.StatusOK)
    json.NewEncoder(w).Encode(map[string]string{
        "status":    "healthy",
        "timestamp": time.Now().Format(time.RFC3339),
    })
}

func readinessHandler(w http.ResponseWriter, r *http.Request) {
    if !checkDependencies() {
        w.WriteHeader(http.StatusServiceUnavailable)
        return
    }
    w.WriteHeader(http.StatusOK)
}

http.HandleFunc("/health", healthHandler)
http.HandleFunc("/ready", readinessHandler)
```

### Graceful Shutdown
```go
import (
    "context"
    "net/http"
    "os"
    "os/signal"
    "syscall"
    "time"
)

srv := &http.Server{Addr: ":8080", Handler: mux}

go func() {
    if err := srv.ListenAndServe(); err != nil && err != http.ErrServerClosed {
        log.Fatal(err)
    }
}()

quit := make(chan os.Signal, 1)
signal.Notify(quit, os.Interrupt, syscall.SIGTERM)
<-quit

ctx, cancel := context.WithTimeout(context.Background(), 10*time.Second)
defer cancel()
srv.Shutdown(ctx)
```

### Retry Pattern with Exponential Backoff
```go
import (
    "time"
    "github.com/cenkalti/backoff/v4"
)

func retryWithBackoff(fn func() error) error {
    exponentialBackoff := backoff.NewExponentialBackOff()
    exponentialBackoff.InitialInterval = 1 * time.Second
    exponentialBackoff.MaxInterval = 30 * time.Second
    exponentialBackoff.MaxElapsedTime = 5 * time.Minute
    return backoff.Retry(fn, exponentialBackoff)
}

err := retryWithBackoff(func() error {
    return azureClient.CreateResource(ctx, resource)
})
```

### Circuit Breaker Pattern
```go
import "github.com/sony/gobreaker"

cb := gobreaker.NewCircuitBreaker(gobreaker.Settings{
    Name:        "azure-api",
    MaxRequests: 3,
    Interval:    60 * time.Second,
    Timeout:     30 * time.Second,
    ReadyToTrip: func(counts gobreaker.Counts) bool {
        return counts.ConsecutiveFailures > 5
    },
})

_, err := cb.Execute(func() (interface{}, error) {
    return azureClient.CreateResource(ctx, resource)
})
```

### Rate Limiting
```go
import "golang.org/x/time/rate"

limiter := rate.NewLimiter(rate.Every(time.Second), 10)

for {
    if err := limiter.Wait(ctx); err != nil {
        return err
    }
    _, err := azureClient.CreateResource(ctx, resource)
}
```

### Resource Tagging
```go
tags := map[string]*string{
    "Environment": to.Ptr("production"),
    "Project":     to.Ptr("infrastructure"),
    "ManagedBy":   to.Ptr("terraform"),
    "CostCenter":  to.Ptr("engineering"),
    "Owner":       to.Ptr("devops-team"),
}

resource := armresources.ResourceGroup{
    Location: to.Ptr("eastus"),
    Tags:     tags,
}
```

### ARM Template Deployment
```go
import "github.com/Azure/azure-sdk-for-go/sdk/resourcemanager/resources/armdeploymentscripts"

deploymentClient, _ := armresources.NewDeploymentsClient(subscriptionID, cred, nil)

template := map[string]interface{}{
    "$schema":    "https://schema.management.azure.com/schemas/2019-04-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "resources":  []interface{}{},
}

deployment := armresources.Deployment{
    Properties: &armresources.DeploymentProperties{
        Template: template,
        Mode:     to.Ptr(armresources.DeploymentModeIncremental),
    },
}

pollResp, _ := deploymentClient.BeginCreateOrUpdate(ctx, "rg-name", "deployment-name", deployment, nil)
_, _ = pollResp.PollUntilDone(ctx, nil)
```

---

## Best Practices

- **Authentication**: Use Managed Identity in Azure environments (VMs, AKS, App Service)
- **Error handling**: Always check errors from Azure SDK calls
- **Context**: Pass context.Context for cancellation and timeouts
- **Resource cleanup**: Use defer or context cancellation for cleanup
- **Retry logic**: Implement retry with exponential backoff for transient failures
- **Rate limiting**: Respect Azure API rate limits
- **Tagging**: Tag all resources for cost tracking and organization
- **Secrets**: Store secrets in Key Vault, never in code
- **Logging**: Use structured logging for better observability
- **Health checks**: Implement /health and /ready endpoints
- **Graceful shutdown**: Handle SIGTERM for clean shutdowns
- **Configuration**: Use environment variables or Key Vault for config
- **Testing**: Mock Azure SDK clients in unit tests
- **Monitoring**: Integrate Application Insights for production workloads
