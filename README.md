# ARMtemplate1

This repository contains an Azure Resource Manager (ARM) template for deploying the **Dependency Agent** on Linux Virtual Machines using an Azure Policy definition.

## Contents

| File | Description |
|------|-------------|
| `Deploy_Linux.txt` | ARM template that creates an Azure Policy definition to automatically deploy the Dependency Agent on Linux VMs |

## What This Template Does

The template (`Deploy_Linux.txt`) creates a custom Azure Policy definition named **DeployDependencyAgentForLinux** that:

- **Targets**: All Azure subscriptions/resource groups where the policy is assigned
- **Condition**: Triggers on `Microsoft.Compute/virtualMachines` resources that have a Linux OS configuration and do **not** already have the `DependencyAgentLinux` extension installed
- **Effect**: `deployIfNotExists` — automatically deploys the Dependency Agent if it is missing
- **Extension deployed**: `DependencyAgentLinux` version `9.4` from publisher `Microsoft.EnterpriseCloud.Monitoring`

## Prerequisites

- An active Azure subscription
- Azure CLI installed and authenticated, **or** access to the Azure Portal
- Contributor or Owner role on the target subscription/resource group

## How to Deploy

### Using Azure CLI

```bash
# Log in to Azure
az login

# Set your subscription
az account set --subscription "<your-subscription-id>"

# Deploy the policy definition at the subscription level
az deployment sub create \
  --location eastus \
  --template-file Deploy_Linux.txt
```

### Using Azure Portal

1. Navigate to the [Azure Portal](https://portal.azure.com)
2. Go to **Deploy a custom template** (search for "Deploy a custom template")
3. Click **Build your own template in the editor**
4. Paste the contents of `Deploy_Linux.txt` and click **Save**
5. Fill in the required fields and click **Review + create**

## After Deployment

Once the policy definition is deployed, you must **assign** it to a scope (subscription or resource group) for it to take effect:

```bash
az policy assignment create \
  --name "deploy-dependency-agent-linux" \
  --display-name "Deploy Dependency Agent on Linux VMs" \
  --policy "DeployDependencyAgentForLinux" \
  --scope "/subscriptions/<your-subscription-id>"
```

## Policy Details

| Property | Value |
|----------|-------|
| Policy Name | `DeployDependencyAgentForLinux` |
| Display Name | Deploy Dependency Agent on Linux VMs |
| Type | Custom |
| Mode | All |
| Category | Compute |
| API Version | 2020-09-01 |
| Agent Publisher | Microsoft.EnterpriseCloud.Monitoring |
| Agent Type | DependencyAgentLinux |
| Agent Version | 9.4 |

## Related Resources

- [Azure Monitor Dependency Agent](https://learn.microsoft.com/en-us/azure/azure-monitor/vm/vminsights-dependency-agent-maintenance)
- [Azure Policy documentation](https://learn.microsoft.com/en-us/azure/governance/policy/overview)
- [ARM template reference for policy definitions](https://learn.microsoft.com/en-us/azure/templates/microsoft.authorization/policydefinitions)
