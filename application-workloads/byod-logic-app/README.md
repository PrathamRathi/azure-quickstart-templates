---
description: This template deploys a request-triggered Logic App for Azure AD Entitlement Management. It creates a Logic App with an HTTP Request trigger, conditional validation of catalog and resource identifiers, and AADPOP authentication policies for secure integration with Entra ID and Access Reviews.
page_type: sample
products:
- azure
- azure-resource-manager
- azure-logic-apps
- azure-active-directory
urlFragment: byod-logic-app
languages:
- json
---

# Deploy a Request-Triggered BYOD Logic App for Entitlement Management

![Azure Public Test Date](https://azurequickstartsservice.blob.core.windows.net/badges/application-workloads/byod-logic-app/PublicLastTestDate.svg)
![Azure Public Test Result](https://azurequickstartsservice.blob.core.windows.net/badges/application-workloads/byod-logic-app/PublicDeployment.svg)

![Azure US Gov Last Test Date](https://azurequickstartsservice.blob.core.windows.net/badges/application-workloads/byod-logic-app/FairfaxLastTestDate.svg)
![Azure US Gov Last Test Result](https://azurequickstartsservice.blob.core.windows.net/badges/application-workloads/byod-logic-app/FairfaxDeployment.svg)

![Best Practice Check](https://azurequickstartsservice.blob.core.windows.net/badges/application-workloads/byod-logic-app/BestPracticeResult.svg)
![Cred Scan Check](https://azurequickstartsservice.blob.core.windows.net/badges/application-workloads/byod-logic-app/CredScanResult.svg)

[![Deploy To Azure](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/deploytoazure.svg?sanitize=true)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fbyod-logic-app%2Fazuredeploy.json)

[![Visualize](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/1-CONTRIBUTION-GUIDE/images/visualizebutton.svg?sanitize=true)](http://armviz.io/#/?load=https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fapplication-workloads%2Fbyod-logic-app%2Fazuredeploy.json)

## Overview

This template deploys a **request-triggered Logic App** for Azure AD Entitlement Management BYOD (Bring Your Own Data) scenarios. Unlike the polling-based variant, this Logic App is invoked via HTTP webhook and validates incoming requests against configured catalog and resource identifiers.

The deployed Logic App includes:

- **HTTP Request Trigger**: Accepts POST requests with review details (reviewDefinitionId, reviewInstanceId, catalogId, customDataProvidedResourceId, callbackDataType, triggerType)
- **Conditional Validation**: Verifies that the incoming catalogId and customDataProvidedResourceId match the configured parameters
- **AADPOP Authentication**: Two Entra ID Proof-of-Possession policies securing the trigger endpoint — one for the ESI app and one for Access Reviews

## Parameters

| Parameter | Type | Description | Default Value |
|-----------|------|-------------|---------------|
| `name` | string | Name of the Logic App | *Required* |
| `location` | string | Deployment location | Resource group location |
| `catalogId` | string | Catalog Id from Entitlement Management | *Required* |
| `customDataProvidedResourceId` | string | Custom Data Provided Resource Id from Entitlement Management | *Required* |
| `armResource` | string | ARM resource based on the cloud | `management.azure.com` |
| `issuer` | string | Token issuer | `https://sts.windows.net/` |
| `appId` | string | ESI AppId for Logic App Creation | `2829028a-b35b-42cc-86e3-517e751a3e2a` |
| `arAppId` | string | AR AppId for Logic App Creation | `d8c767ef-3e9a-48c4-aef9-562696539b39` |

## Deployment

### Azure Portal

Click the **Deploy to Azure** button above.

### Azure CLI

```bash
az group create --name myResourceGroup --location eastus

az deployment group create \
  --resource-group myResourceGroup \
  --template-file azuredeploy.json \
  --parameters name=myLogicApp catalogId=<your-catalog-id> customDataProvidedResourceId=<your-resource-id>
```

### PowerShell

```powershell
New-AzResourceGroup -Name myResourceGroup -Location eastus

New-AzResourceGroupDeployment `
  -ResourceGroupName myResourceGroup `
  -TemplateFile azuredeploy.json `
  -name myLogicApp `
  -catalogId '<your-catalog-id>' `
  -customDataProvidedResourceId '<your-resource-id>'
```

## Security

- **SAS authentication is disabled** — the trigger endpoint does not accept SAS tokens
- **AADPOP policies** enforce Proof-of-Possession token validation, ensuring only authorized Entra ID applications (ESI and Access Reviews) can invoke the Logic App
- Claims validated include issuer, audience, appId, HTTP method, host, and resource path

## Resources Deployed

- **Microsoft.Logic/workflows**: Logic App with HTTP Request trigger and AADPOP access control

`Tags: Microsoft.Logic/workflows, Request, Http, AADPOP, Identity Governance, Entitlement Management, Access Reviews, BYOD`
