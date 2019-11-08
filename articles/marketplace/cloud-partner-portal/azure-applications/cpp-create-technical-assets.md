---
title: Technische activa van Azure Application maken | Azure Marketplace
description: Maak de technische activa voor een Azure-toepassings aanbieding.
services: Azure, Marketplace, Cloud Partner Portal,
author: v-miclar
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
ms.date: 12/13/2018
ms.author: pabutler
ms.openlocfilehash: c9abaab4597e9fea43a2f0dcabc0e4a527ed5a6a
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827610"
---
# <a name="prepare-your-azure-application-technical-assets"></a>De technische activa van uw Azure-toepassing voorbereiden

In dit artikel worden de resources beschreven voor het voorbereiden van de technische activa voor uw Azure-toepassings aanbieding.

## <a name="before-you-begin"></a>Voordat u begint

Bekijk de volgende video, [bouw oplossings sjablonen en beheerde toepassingen voor Azure Marketplace](https://channel9.msdn.com/Events/Build/2018/BRK3603), een overzicht van het maken van een Azure Resource Manager sjabloon voor het definiëren van een Azure-toepassings oplossing en het vervolgens publiceren van de app-aanbieding naar de Azure Marketplace.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


Raadpleeg de volgende documentatie voor Azure-toepassingen, die Quick starts, zelf studies en voor beelden biedt.

- [Azure Resource Manager sjablonen begrijpen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- Quickstarts:

  - [Sjablonen voor Azure Quick Start](https://azure.microsoft.com/documentation/templates/)
  - [GitHub Azure Quick Start-sjablonen](https://github.com/azure/azure-quickstart-templates)
  - [Toepassings definitie publiceren](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [Service catalogus-app implementeren](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- Zelfstudies:

  - [Definitie bestanden maken](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [Een Marketplace-toepassing publiceren](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

  - Voor beelden

    - [Azure CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    - [Oplossingen voor beheerde toepassingen](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>Fundamentele technische kennis

Het ontwerpen, bouwen en testen van deze assets vergt tijd en vereist technische kennis van het Azure-platform en de technologieën die worden gebruikt om de aanbieding te bouwen.

Uw technische team moet kennis hebben van de volgende micro soft-technologieën:

- Basis informatie over [Azure-Services](https://azure.microsoft.com/services/)
- Azure- [toepassingen ontwerpen en ontwikkelen](https://azure.microsoft.com/solutions/architecture/)
- Praktische kennis van [Azure-virtual machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage)en [Azure-netwerken](https://azure.microsoft.com/services/?filter=networking)
- Werk ervaring van [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Praktische kennis van [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Aanbevolen hulpprogram ma's

Kies een of beide van de volgende script omgevingen om uw Azure-toepassing te beheren:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure CLI](https://docs.microsoft.com/cli/azure)

Het is raadzaam om de volgende hulpprogram ma's toe te voegen aan uw ontwikkel omgeving:

- [Azure-opslagverkenner](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio code](https://code.visualstudio.com/) met de volgende extensies:

  - Extensie: [Azure Resource Manager-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Extensie: [beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Extensie: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

We raden u ook aan om de beschik bare hulpprogram ma's op de [Azure Ontwikkelhulpprogramma's](https://azure.microsoft.com/tools/) -pagina te bekijken en als u Visual Studio, de [Visual Studio Marketplace](https://marketplace.visualstudio.com/), gebruikt.

## <a name="next-steps"></a>Volgende stappen

[Aanbieding voor Azure-toepassing maken](./cpp-create-offer.md)

