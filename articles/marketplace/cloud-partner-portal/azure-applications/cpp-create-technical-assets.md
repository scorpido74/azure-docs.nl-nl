---
title: Technische elementen van Azure-toepassingen maken | Azure Marketplace
description: Maak de technische elementen voor een Azure-toepassingsaanbieding.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 12/13/2018
ms.author: dsindona
ms.openlocfilehash: 041b2133ed63a906d3fea3ab67890a0057151b9b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80285262"
---
# <a name="prepare-your-azure-application-technical-assets"></a>Uw technische elementen van azure-toepassingen voorbereiden

In dit artikel worden de bronnen beschreven voor het voorbereiden van de technische elementen voor uw Azure-toepassingsaanbieding.

## <a name="before-you-begin"></a>Voordat u begint

Bekijk de volgende video, [Oplossingen voor het bouwen en Beheerde toepassingen voor de Azure Marketplace,](https://channel9.msdn.com/Events/Build/2018/BRK3603)een overzicht over het maken van een Azure Resource Manager-sjabloon om een Azure-toepassingsoplossing te definiëren en vervolgens hoe u de app-aanbieding vervolgens publiceert naar de Azure Marketplace.

>[!VIDEO https://channel9.msdn.com/Events/Build/2018/BRK3603/player]


Bekijk de volgende Azure-toepassingsdocumentatie, die Quickstarts, Tutorials en Voorbeelden bevat.

- [Azure Resource Manager-sjablonen begrijpen](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates)
- Quickstarts:

  - [Azure Quickstart-sjablonen](https://azure.microsoft.com/documentation/templates/)
  - [GitHub Azure Quickstart-sjablonen](https://github.com/azure/azure-quickstart-templates)
  - [Toepassingsdefinitie publiceren](https://docs.microsoft.com/azure/managed-applications/publish-managed-app-definition-quickstart)
  - [Servicecatalogus-app implementeren](https://docs.microsoft.com/azure/managed-applications/deploy-service-catalog-quickstart)

  
- Zelfstudies:

  - [Definitiebestanden maken](https://docs.microsoft.com/azure/managed-applications/publish-service-catalog-app)
  - [Een Marketplace-toepassing publiceren](https://docs.microsoft.com/azure/managed-applications/publish-marketplace-app)

  - Voorbeelden:

    - [Azure-CLI](https://docs.microsoft.com/azure/managed-applications/cli-samples)
    - [Azure PowerShell](https://docs.microsoft.com/azure/managed-applications/powershell-samples)
    - [Beheerde toepassingsoplossingen](https://docs.microsoft.com/azure/managed-applications/sample-projects)

## <a name="fundamental-technical-knowledge"></a>Fundamentele technische kennis

Het ontwerpen, bouwen en testen van deze assets kost tijd en vereist technische kennis van zowel het Azure-platform als de technologieën die worden gebruikt om het aanbod te bouwen.

Uw engineeringteam moet kennis hebben over de volgende Microsoft-technologieën:

- Basiskennis van [Azure Services](https://azure.microsoft.com/services/)
- [Azure-toepassingen ontwerpen en ontwerpen](https://azure.microsoft.com/solutions/architecture/)
- Werkkennis van [Azure Virtual Machines,](https://azure.microsoft.com/services/virtual-machines/) [Azure Storage](https://azure.microsoft.com/services/?filter=storage)en Azure [Networking](https://azure.microsoft.com/services/?filter=networking)
- Werkkennis van [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Werkkennis van [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Voorgestelde hulpprogramma's

Kies een of beide van de volgende scriptomgevingen om uw Azure-toepassing te beheren:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
- [Azure-CLI](https://docs.microsoft.com/cli/azure)

We raden u aan de volgende tools toe te voegen aan uw ontwikkelomgeving:

- [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
- [Visual Studio Code](https://code.visualstudio.com/) met de volgende extensies:

  - Extensie: [Azure Resource Manager-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Uitbreiding: [Verfraaien](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Uitbreiding: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

We raden ook aan om de beschikbare hulpprogramma's op de pagina [Azure Developer Tools](https://azure.microsoft.com/tools/) te bekijken en, als u Visual Studio gebruikt, de Visual Studio [Marketplace](https://marketplace.visualstudio.com/).

## <a name="next-steps"></a>Volgende stappen

[Aanbieding voor Azure-toepassing maken](./cpp-create-offer.md)

