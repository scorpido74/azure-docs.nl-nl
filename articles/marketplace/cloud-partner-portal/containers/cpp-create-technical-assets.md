---
title: Installatie kopieën van Azure containers maken technische activa | Azure Marketplace
description: Maak de technische activa voor een Azure-container.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 11/01/2018
ms.author: pabutler
ms.openlocfilehash: 6aae1957a3dcc58339eb4f3c9e250513843ceb18
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73819050"
---
# <a name="prepare-your-container-technical-assets"></a>De technische activa van de container voorbereiden

In dit artikel worden de stappen en vereisten beschreven voor het configureren van een container waarin de Azure Marketplace wordt aangeboden.

## <a name="before-you-begin"></a>Voordat u begint

Raadpleeg de [Azure container instances](https://docs.microsoft.com/azure/container-instances) -documentatie, waarmee u Quick starts, zelf studies en voor beelden kunt bekijken.

## <a name="fundamental-technical-knowledge"></a>Fundamentele technische kennis

Het ontwerpen, bouwen en testen van deze assets vergt tijd en vereist technische kennis van het Azure-platform en de technologieën die worden gebruikt om de aanbieding te bouwen.
 
Naast uw oplossings domein moet uw technische team kennis hebben over de volgende micro soft-technologieën:

-   Basis informatie over [Azure-Services](https://azure.microsoft.com/services/) 
-   Azure- [toepassingen ontwerpen en ontwikkelen](https://azure.microsoft.com/solutions/architecture/)
-   Praktische kennis van [Azure virtual machines](https://azure.microsoft.com/services/virtual-machines/), [Azure Storage](https://azure.microsoft.com/services/?filter=storage) en [Azure-netwerken](https://azure.microsoft.com/services/?filter=networking)
-   Werk ervaring van [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
-   Praktische kennis van [JSON](https://www.json.org/)

## <a name="suggested-tools"></a>Aanbevolen hulpprogram ma's

Kies een of beide van de volgende script omgevingen om uw container installatie kopie te beheren:

-   [Azure PowerShell](https://docs.microsoft.com/powershell/azure/overview)
-   [Azure CLI](https://docs.microsoft.com/cli/azure)

Daarnaast raden wij aan de volgende hulpprogram ma's toe te voegen aan uw ontwikkel omgeving:

-   [Azure-opslagverkenner](https://docs.microsoft.com/azure/vs-azure-tools-storage-manage-with-storage-explorer)
-   [Visual Studio Code](https://code.visualstudio.com/)
    *   Extensie: [Azure Resource Manager-Hulpprogram ma's](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
    *   Extensie: [beautify](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
    *   Extensie: [PRETTIFY JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json)

We raden u ook aan om de beschik bare hulpprogram ma's op de [Azure Ontwikkelhulpprogramma's](https://azure.microsoft.com/tools/) -pagina te bekijken en als u Visual Studio, de [Visual Studio Marketplace](https://marketplace.visualstudio.com/), gebruikt.

## <a name="create-the-container-image"></a>De container installatie kopie maken

Zie het volgende voor meer informatie:

* [Zelf studie: een container installatie kopie maken voor implementatie naar Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
* [Zelf studie: container installatie kopieën bouwen en implementeren in de Cloud met Azure Container Registry taken](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task)

## <a name="next-steps"></a>Volgende stappen

[Uw container aanbieding maken](./cpp-create-offer.md)
