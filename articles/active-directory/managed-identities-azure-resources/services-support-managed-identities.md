---
title: Azure Services die beheerde identiteiten ondersteunen - Azure AD
description: Lijst met services die beheerde identiteiten voor Azure-resources en Azure AD-verificatie ondersteunen
services: active-directory
author: MarkusVi
ms.author: markvi
ms.date: 04/03/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4c7a65df100cd58561ce12ac2ae01281eebd419a
ms.sourcegitcommit: 62c5557ff3b2247dafc8bb482256fef58ab41c17
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80656054"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Services die beheerde identiteiten voor Azure-resources ondersteunen

Beheerde identiteiten voor Azure-resources bieden Azure-services een automatisch beheerde identiteit in Azure Active Directory. Met behulp van een beheerde identiteit u zich verifiëren voor elke service die Azure AD-verificatie ondersteunt zonder referenties in uw code te hebben. We zijn bezig met het integreren van beheerde identiteiten voor Azure-resources en Azure AD-verificatie in Azure. Kom regelmatig terug voor updates.

> [!NOTE]
> Beheerde identiteiten voor Azure-resources is de nieuwe naam voor de service die eerder de naam Managed Service Identity (MSI) had.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure-services die beheerde identiteiten voor Azure-resources ondersteunen

De volgende Azure-services ondersteunen beheerde identiteiten voor Azure-resources:

### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Beheerd identiteitstype | Alle algemeen beschikbare<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Toegewezen systeem | ![Beschikbaar][check] | ![Beschikbaar][check] | Preview | Preview | 
| Gebruiker toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check] | Preview | Preview |

Raadpleeg de volgende lijst om beheerde identiteit voor Azure Virtual Machines te configureren (in regio's waar beschikbaar):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure-CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sjablonen](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-virtual-machine-scale-sets"></a>Microsoft Azure Virtual Machine Scale Sets

|Beheerd identiteitstype | Alle algemeen beschikbare<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Toegewezen systeem | ![Beschikbaar][check] | Preview | Preview | Preview |
| Gebruiker toegewezen | ![Beschikbaar][check] | Preview | Preview | Preview |

Raadpleeg de volgende lijst om beheerde identiteit te configureren voor Azure Virtual Machine Scale Sets (in regio's waar beschikbaar):

- [Azure Portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure-CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sjablonen](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)

### <a name="azure-app-service"></a>Azure App Service

| Beheerd identiteitstype | Alle algemeen beschikbare<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Toegewezen systeem | ![Beschikbaar][check] | ![Beschikbaar][check] | ![Beschikbaar][check] | ![Beschikbaar][check] |
| Gebruiker toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check]  | ![Beschikbaar][check]  | ![Beschikbaar][check] |

Raadpleeg de volgende lijst om beheerde identiteit voor Azure App Service te configureren (in regio's waar beschikbaar):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure-CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-sjabloon](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-blueprints"></a>Azure Blueprints

|Beheerd identiteitstype | Alle algemeen beschikbare<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Toegewezen systeem | ![Beschikbaar][check] | ![Beschikbaar][check] | Niet beschikbaar | Niet beschikbaar |
| Gebruiker toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check] | Niet beschikbaar | Niet beschikbaar |

Raadpleeg de volgende lijst om een beheerde identiteit te gebruiken met [Azure Blueprints:](../../governance/blueprints/overview.md)

- [Azure-portal - blauwdruktoewijzing](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API - blauwdruktoewijzing](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)

### <a name="azure-functions"></a>Azure Functions

Beheerd identiteitstype |Alle algemeen beschikbare<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Toegewezen systeem | ![Beschikbaar][check] | ![Beschikbaar][check] | ![Beschikbaar][check] | ![Beschikbaar][check] |
| Gebruiker toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check]  | ![Beschikbaar][check]  | ![Beschikbaar][check]  |

Raadpleeg de volgende lijst om beheerde identiteit voor Azure-functies te configureren (in regio's waar beschikbaar):

- [Azure Portal](/azure/app-service/overview-managed-identity#using-the-azure-portal)
- [Azure-CLI](/azure/app-service/overview-managed-identity#using-the-azure-cli)
- [Azure PowerShell](/azure/app-service/overview-managed-identity#using-azure-powershell)
- [Azure Resource Manager-sjabloon](/azure/app-service/overview-managed-identity#using-an-azure-resource-manager-template)

### <a name="azure-logic-apps"></a>Azure Logic Apps

Beheerd identiteitstype | Alle algemeen beschikbare<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Toegewezen systeem | ![Beschikbaar][check] | ![Beschikbaar][check] | Niet beschikbaar | ![Beschikbaar][check] |
| Gebruiker toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check] | Niet beschikbaar | ![Beschikbaar][check] |


Raadpleeg de volgende lijst om beheerde identiteit voor Azure Logic Apps te configureren (in regio's waar beschikbaar):

- [Azure Portal](/azure/logic-apps/create-managed-service-identity#enable-system-assigned-identity-in-azure-portal)
- [Azure Resource Manager-sjabloon](/azure/app-service/overview-managed-identity)

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Beheerd identiteitstype | Alle algemeen beschikbare<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Toegewezen systeem | ![Beschikbaar][check] | ![Beschikbaar][check] | Niet beschikbaar | ![Beschikbaar][check] |
| Gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Raadpleeg de volgende lijst om beheerde identiteit te configureren voor Azure Data Factory V2 (in regio's waar beschikbaar):

- [Azure Portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-api-management"></a>Azure API Management

Beheerd identiteitstype | Alle algemeen beschikbare<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Toegewezen systeem | ![Beschikbaar][check] | ![Beschikbaar][check] | Niet beschikbaar | ![Beschikbaar][check] |
| Gebruiker toegewezen | Preview | Preview | Niet beschikbaar | Preview |

Raadpleeg de volgende lijst om beheerde identiteit voor Azure API-beheer te configureren (in regio's waar beschikbaar):

- [Azure Resource Manager-sjabloon](/azure/api-management/api-management-howto-use-managed-service-identity)

### <a name="azure-container-instances"></a>Azure Container Instances

Beheerd identiteitstype | Alle algemeen beschikbare<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Toegewezen systeem | Linux: Preview<br>Windows: Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| Gebruiker toegewezen | Linux: Preview<br>Windows: Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Raadpleeg de volgende lijst om beheerde identiteit voor Azure Container Instances te configureren (in regio's waar beschikbaar):

- [Azure-CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager-sjabloon](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)

### <a name="azure-container-registry-tasks"></a>Azure Container Registry Tasks

Beheerd identiteitstype | Alle algemeen beschikbare<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Toegewezen systeem | ![Beschikbaar][check] | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| Gebruiker toegewezen | Preview | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Raadpleeg de volgende lijst om beheerde identiteit te configureren voor Azure Container Registry Tasks (in regio's waar beschikbaar):

- [Azure-CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-service-fabric"></a>Azure Service Fabric
[Managed Identity for Service Fabric Applications](https://docs.microsoft.com/azure/service-fabric/concepts-managed-identity) is in Preview en beschikbaar in alle regio's.

Beheerd identiteitstype | Alle algemeen beschikbare<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Toegewezen systeem | ![Beschikbaar][check] | Niet beschikbaar | Niet beschikbaar | niet beschikbaar |
| Gebruiker toegewezen | ![Beschikbaar][check] | Niet beschikbaar | Niet beschikbaar |Niet beschikbaar |

Raadpleeg de volgende lijst om beheerde identiteit voor Azure Service Fabric-toepassingen in alle regio's te configureren:
- [Azure Resource Manager-sjabloon](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-services die Azure AD-verificatie ondersteunen

De volgende services ondersteunen Azure AD-verificatie en zijn getest met clientservices die beheerde identiteiten gebruiken voor Azure-bronnen.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Raadpleeg de volgende lijst om toegang tot Azure Resource Manager te configureren:

- [Toegang toewijzen via Azure-portal](howto-assign-access-portal.md)
- [Toegang toewijzen via PowerShell](howto-assign-access-powershell.md)
- [Toegang toewijzen via Azure CLI](howto-assign-access-CLI.md)
- [Toegang toewijzen via azure resource manager-sjabloon](../../role-based-access-control/role-assignments-template.md)

| Cloud | Resource-id | Status |
|--------|------------|:-:|
| Azure Global | `https://management.azure.com/`| ![Beschikbaar][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![Beschikbaar][check] |
| Azure Duitsland | `https://management.microsoftazure.de/` | ![Beschikbaar][check] |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | ![Beschikbaar][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| Cloud | Resource-id | Status |
|--------|------------|:-:|
| Azure Global | `https://vault.azure.net`| ![Beschikbaar][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![Beschikbaar][check] |
| Azure Duitsland |  `https://vault.microsoftazure.de` | ![Beschikbaar][check] |
| Azure China 21Vianet | `https://vault.azure.cn` | ![Beschikbaar][check] |

### <a name="azure-data-lake"></a>Azure Data Lake 

| Cloud | Resource-id | Status |
|--------|------------|:-:|
| Azure Global | `https://datalake.azure.net/` | ![Beschikbaar][check] |
| Azure Government |  | Niet beschikbaar |
| Azure Duitsland |   | Niet beschikbaar |
| Azure China 21Vianet |  | Niet beschikbaar |

### <a name="azure-sql"></a>Azure SQL 

| Cloud | Resource-id | Status |
|--------|------------|:-:|
| Azure Global | `https://database.windows.net/` | ![Beschikbaar][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![Beschikbaar][check] |
| Azure Duitsland | `https://database.cloudapi.de/` | ![Beschikbaar][check] |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | ![Beschikbaar][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Cloud | Resource-id | Status |
|--------|------------|:-:|
| Azure Global | `https://eventhubs.azure.net` | ![Beschikbaar][check] |
| Azure Government |  | Niet beschikbaar |
| Azure Duitsland |   | Niet beschikbaar |
| Azure China 21Vianet |  | Niet beschikbaar |

### <a name="azure-service-bus"></a>Azure Service Bus

| Cloud | Resource-id | Status |
|--------|------------|:-:|
| Azure Global | `https://servicebus.azure.net`  | ![Beschikbaar][check] |
| Azure Government |  | ![Beschikbaar][check] |
| Azure Duitsland |   | Niet beschikbaar |
| Azure China 21Vianet |  | Niet beschikbaar |









### <a name="azure-storage-blobs-and-queues"></a>Blobs en wachtrijen voor Azure Storage

| Cloud | Resource-id | Status |
|--------|------------|:-:|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Beschikbaar][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Beschikbaar][check] |
| Azure Duitsland | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Beschikbaar][check] |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Beschikbaar][check] |










### <a name="azure-analysis-services"></a>Azure Analysis Services

| Cloud | Resource-id | Status |
|--------|------------|:-:|
| Azure Global | `https://*.asazure.windows.net` | ![Beschikbaar][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![Beschikbaar][check] |
| Azure Duitsland | `https://*.asazure.cloudapi.de` | ![Beschikbaar][check] |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![Beschikbaar][check] |


[check]: media/services-support-managed-identities/check.png "Beschikbaar"
