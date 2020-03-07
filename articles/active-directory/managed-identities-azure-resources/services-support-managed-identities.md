---
title: Azure-Services die beheerde identiteiten ondersteunen-Azure AD
description: Lijst met services die beheerde identiteiten voor Azure-resources en Azure AD-verificatie ondersteunen
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 02/13/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: a0576a70b1b345d31ffc11c55f7fa5cbd288acd5
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78356050"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Services die beheerde identiteiten voor Azure-resources ondersteunen

Beheerde identiteiten voor Azure-resources bieden Azure-Services met een automatisch beheerde identiteit in Azure Active Directory. Met een beheerde identiteit kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie zonder referenties in uw code. We zijn bezig met het integreren van beheerde identiteiten voor Azure-resources en Azure AD-verificatie in Azure. Controleer regel matig of er updates zijn.

> [!NOTE]
> Beheerde identiteiten voor Azure-resources is de nieuwe naam voor de service die eerder de naam Managed Service Identity (MSI) had.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure-services die beheerde identiteiten voor Azure-resources ondersteunen

De volgende Azure-Services ondersteunen beheerde identiteiten voor Azure-resources:

### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Systeem toegewezen | Beschikbaar | Preview | Preview | Preview | 
| Gebruiker toegewezen | Beschikbaar | Preview | Preview | Preview |

Raadpleeg de volgende lijst om de beheerde identiteit voor Azure Virtual Machines te configureren (in regio's waar beschikbaar):

- [Azure-portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sjablonen](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Azure Virtual Machine Scale Sets

|Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Systeem toegewezen | Beschikbaar | Preview | Preview | Preview |
| Gebruiker toegewezen | Beschikbaar | Preview | Preview | Preview |

Raadpleeg de volgende lijst om de beheerde identiteit voor Azure Virtual Machine Scale Sets te configureren (in regio's waar beschikbaar):

- [Azure-portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sjablonen](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Systeem toegewezen | Beschikbaar | Beschikbaar | Beschikbaar | Beschikbaar |
| Gebruiker toegewezen | Beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Raadpleeg de volgende lijst om de beheerde identiteit voor Azure App Service te configureren (in regio's waar beschikbaar):

- [Azure-portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-sjabloon](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprints

|Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Systeem toegewezen | Beschikbaar | Beschikbaar | Niet beschikbaar | Niet beschikbaar |
| Gebruiker toegewezen | Beschikbaar | Beschikbaar | Niet beschikbaar | Niet beschikbaar |

Raadpleeg de volgende lijst voor het gebruik van een beheerde identiteit met [Azure-blauw drukken](../../governance/blueprints/overview.md):

- [Azure Portal-blauw druk toewijzen](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API-blauw druk toewijzen](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Azure Functions

Type beheerde identiteit |Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Systeem toegewezen | Beschikbaar | Beschikbaar | Beschikbaar | Beschikbaar |
| Gebruiker toegewezen | Beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Raadpleeg de volgende lijst om de beheerde identiteit voor Azure Functions te configureren (in regio's waar beschikbaar):

- [Azure-portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-sjabloon](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Systeem toegewezen | Preview | Preview | Niet beschikbaar | Preview |
| Gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Raadpleeg de volgende lijst om de beheerde identiteit voor Azure Logic Apps te configureren (in regio's waar beschikbaar):

- [Azure-portal](/azure/logic-apps/create-managed-service-identity#enable-system-assigned-identity-in-azure-portal)
- [Azure Resource Manager-sjabloon](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Systeem toegewezen | Beschikbaar | Beschikbaar | Niet beschikbaar | Beschikbaar |
| Gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Raadpleeg de volgende lijst voor het configureren van een beheerde identiteit voor Azure Data Factory v2 (in regio's waar beschikbaar):

- [Azure-portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Systeem toegewezen | Beschikbaar | Beschikbaar | Niet beschikbaar | Niet beschikbaar |
| Gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Raadpleeg de volgende lijst om de beheerde identiteit voor Azure API Management te configureren (in regio's waar beschikbaar):

- [Azure Resource Manager-sjabloon](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Systeem toegewezen | Linux: preview<br>Windows: niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| Gebruiker toegewezen | Linux: preview<br>Windows: niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Raadpleeg de volgende lijst om de beheerde identiteit voor Azure Container Instances te configureren (in regio's waar beschikbaar):

- [Azure CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager-sjabloon](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Azure Container Registry Tasks

Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Systeem toegewezen | Beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| Gebruiker toegewezen | Preview | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Raadpleeg de volgende lijst voor het configureren van een beheerde identiteit voor Azure Container Registry taken (in regio's waar beschikbaar):

- [Azure CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-service-fabric"></a>Azure Service Fabric
De [beheerde identiteit voor service Fabric toepassingen](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) is in de preview-versie en beschikbaar in alle regio's.

Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Systeem toegewezen | Beschikbaar | Niet beschikbaar | Niet beschikbaar | niet beschikbaar |
| Gebruiker toegewezen | Beschikbaar | Niet beschikbaar | Niet beschikbaar |Niet beschikbaar |

Raadpleeg de volgende lijst voor het configureren van beheerde identiteit voor Azure Service Fabric-toepassingen in alle regio's:
- [Azure Resource Manager-sjabloon](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-Services die ondersteuning bieden voor Azure AD-verificatie

De volgende services ondersteunen Azure AD-verificatie en zijn getest met client services die beheerde identiteiten voor Azure-resources gebruiken.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Raadpleeg de volgende lijst om de toegang tot Azure Resource Manager te configureren:

- [Toegang toewijzen via Azure Portal](howto-assign-access-portal.md)
- [Toegang toewijzen via Power shell](howto-assign-access-powershell.md)
- [Toegang toewijzen via Azure CLI](howto-assign-access-CLI.md)
- [Toegang toewijzen via Azure Resource Manager sjabloon](../../role-based-access-control/role-assignments-template.md)

| Cloud | Resource-id | Status |
|--------|------------|--------|
| Azure Global | `https://management.azure.com/`| Beschikbaar |
| Azure Government | `https://management.usgovcloudapi.net/` | Beschikbaar |
| Azure Duitsland | `https://management.microsoftazure.de/` | Beschikbaar |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | Beschikbaar |

### <a name="azure-key-vault"></a>Azure Key Vault

| Cloud | Resource-id | Status |
|--------|------------|--------|
| Azure Global | `https://vault.azure.net`| Beschikbaar |
| Azure Government | `https://vault.usgovcloudapi.net` | Beschikbaar |
| Azure Duitsland |  `https://vault.microsoftazure.de` | Beschikbaar |
| Azure China 21Vianet | `https://vault.azure.cn` | Beschikbaar |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Cloud | Resource-id | Status |
|--------|------------|--------|
| Azure Global | `https://datalake.azure.net/` | Beschikbaar |
| Azure Government |  | Niet beschikbaar |
| Azure Duitsland |   | Niet beschikbaar |
| Azure China 21Vianet |  | Niet beschikbaar |

### <a name="azure-sql"></a>Azure SQL 

| Cloud | Resource-id | Status |
|--------|------------|--------|
| Azure Global | `https://database.windows.net/` | Beschikbaar |
| Azure Government | `https://database.usgovcloudapi.net/` | Beschikbaar |
| Azure Duitsland | `https://database.cloudapi.de/` | Beschikbaar |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | Beschikbaar |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Cloud | Resource-id | Status |
|--------|------------|--------|
| Azure Global | `https://eventhubs.azure.net` | Beschikbaar |
| Azure Government |  | Niet beschikbaar |
| Azure Duitsland |   | Niet beschikbaar |
| Azure China 21Vianet |  | Niet beschikbaar |

### <a name="azure-service-bus"></a>Azure Service Bus

| Cloud | Resource-id | Status |
|--------|------------|--------|
| Azure Global | `https://servicebus.azure.net`  | Beschikbaar |
| Azure Government |  | Beschikbaar |
| Azure Duitsland |   | Niet beschikbaar |
| Azure China 21Vianet |  | Niet beschikbaar |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage blobs en wacht rijen

| Cloud | Resource-id | Status |
|--------|------------|--------|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | Beschikbaar |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | Beschikbaar |
| Azure Duitsland | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | Beschikbaar |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | Beschikbaar |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| Cloud | Resource-id | Status |
|--------|------------|--------|
| Azure Global | `https://*.asazure.windows.net` | Beschikbaar |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | Beschikbaar |
| Azure Duitsland | `https://*.asazure.cloudapi.de` | Beschikbaar |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | Beschikbaar |
