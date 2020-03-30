---
title: Technische elementen van Azure Containers-afbeelding maken | Azure Marketplace
description: Maak de technische elementen voor een Azure-container.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: dsindona
ms.openlocfilehash: 71b3ec4bf505c333e5eca170e1f5e808ab51f41a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80280062"
---
# <a name="prepare-your-container-technical-assets"></a>De technische activa van de container voorbereiden

In dit artikel worden stappen en vereisten beschreven voor het configureren van een containeraanbieding op Azure Marketplace.

## <a name="before-you-begin"></a>Voordat u begint

Controleer de documentatie [Azure Container Instances,](https://docs.microsoft.com/azure/container-instances) die Quickstarts, Tutorials en Samples bevat.

## <a name="fundamental-technical-knowledge"></a>Fundamentele technische kennis

Het ontwerpen, bouwen en testen van deze assets kost tijd en vereist technische kennis van zowel het Azure-platform als de technologieën die worden gebruikt om het aanbod te bouwen.
 
Naast uw oplossingsdomein moet uw engineeringteam kennis hebben over de volgende Microsoft-technologieën:

-   Basiskennis van [Azure Services](https://azure.microsoft.com/services/) 
-   [Azure-toepassingen ontwerpen en ontwerpen](https://azure.microsoft.com/solutions/architecture/)
-   Werkkennis van [Azure Virtual Machines,](https://azure.microsoft.com/services/virtual-machines/) [Azure Storage](https://azure.microsoft.com/services/?filter=storage) en Azure [Networking](https://azure.microsoft.com/services/?filter=networking)
-   Werkkennis van [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Werkkennis van [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Voorgestelde hulpprogramma's

Kies een of beide van de volgende scriptomgevingen om uw containerafbeelding te beheren:

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure-CLI](https://docs.microsoft.com/cli/azure)

Daarnaast raden we aan om de volgende tools toe te voegen aan je ontwikkelomgeving:

-   [Azure Storage Explorer](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio-code](https://code.visualstudio.com/)
    *   Extensie: [Azure Resource Manager-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Uitbreiding: [Verfraaien](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Uitbreiding: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

We raden ook aan om de beschikbare hulpprogramma's op de pagina [Azure Developer Tools](https://azure.microsoft.com/tools/) te bekijken en, als u Visual Studio gebruikt, de Visual Studio [Marketplace](https://marketplace.visualstudio.com/).

## <a name="create-the-container-image"></a>De containerafbeelding maken

Zie het volgende voor meer informatie:

* [Zelfstudie: Een containerafbeelding maken voor implementatie naar Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Zelfstudie: Containerafbeeldingen in de cloud maken en implementeren met Azure Container Registry Tasks](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>Volgende stappen

[Uw containeraanbieding maken](./cpp-create-offer.md)
