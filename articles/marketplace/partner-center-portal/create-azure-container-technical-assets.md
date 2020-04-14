---
title: Uw technische assets van Azure Container voorbereiden
description: In dit artikel worden de stappen en vereisten beschreven voor het configureren van een containeraanbieding op Azure Marketplace.
author: anbene
ms.author: mingshen
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/09/2020
ms.openlocfilehash: 62c77681accb19183b5e8aa3ae8d80c225b2d284
ms.sourcegitcommit: 8dc84e8b04390f39a3c11e9b0eaf3264861fcafc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2020
ms.locfileid: "81266017"
---
# <a name="prepare-your-azure-container-technical-assets"></a>Uw technische assets van Azure Container voorbereiden

> [!IMPORTANT]
> We verplaatsen het beheer van uw Azure Container-aanbiedingen van Cloud Partner Portal naar Partner Center. Totdat uw aanbiedingen zijn gemigreerd, volgt u de instructies in [Uw containertechnische assets voorbereiden](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal/containers/cpp-create-technical-assets) voor Cloud Partner Portal om uw aanbiedingen te beheren.

In dit artikel worden de stappen en vereisten beschreven voor het configureren van een containeraanbieding op Azure Marketplace.

## <a name="before-you-begin"></a>Voordat u begint

Zie [Azure Container Instances](https://docs.microsoft.com/azure/container-instances)voor Quickstarts, Tutorials en Voorbeelden .

## <a name="fundamental-technical-knowledge"></a>Fundamentele technische kennis

Het ontwerpen, bouwen en testen van deze assets kost tijd en vereist technische kennis van zowel het Azure-platform als de technologieën die worden gebruikt om het aanbod te bouwen.

Naast uw oplossingsdomein moet uw engineeringteam kennis hebben over de volgende Microsoft-technologieën:

- Basiskennis van [Azure Services](https://azure.microsoft.com/services/)
- [Azure-toepassingen ontwerpen en ontwerpen](https://azure.microsoft.com/solutions/architecture/)
- Werkkennis van [Azure Virtual Machines,](https://azure.microsoft.com/services/virtual-machines/) [Azure Storage](https://azure.microsoft.com/services/?filter=storage)en Azure [Networking](https://azure.microsoft.com/services/?filter=networking)
- Werkkennis van [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/)
- Werkkennis van [JSON](https://www.json.org/).

## <a name="suggested-tools"></a>Voorgestelde hulpprogramma's

Kies een of beide van de volgende scriptomgevingen om uw containerafbeelding te beheren:

- [Azure PowerShell](https://docs.microsoft.com/powershell/azure/?view=azps-3.7.0&viewFallbackFrom=azps-3.6.1)
- [Azure CLI](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest).

We raden u aan deze tools toe te voegen aan uw ontwikkelomgeving:

- [Azure Opslagverkenner](https://aka.ms/GetStartedWithStorageExplorer)
- [Visual Studio Code](https://code.visualstudio.com/)
  - Extensie: [Azure Resource Manager-hulpprogramma's](https://marketplace.visualstudio.com/items?itemName=msazurermtools.azurerm-vscode-tools)
  - Uitbreiding: [Verfraaien](https://marketplace.visualstudio.com/items?itemName=HookyQR.beautify)
  - Uitbreiding: [Prettify JSON](https://marketplace.visualstudio.com/items?itemName=mohsen1.prettify-json).

Bekijk de beschikbare hulpprogramma's op de pagina [Azure Developer Tools.](https://azure.microsoft.com/) Als u Visual Studio gebruikt, bekijkt u de hulpprogramma's die beschikbaar zijn in de [Visual Studio Marketplace.](https://marketplace.visualstudio.com/)

## <a name="create-the-container-image"></a>De containerafbeelding maken

Zie de volgende zelfstudies voor meer informatie:

- [Zelfstudie: Een containerafbeelding maken voor implementatie naar Azure Container Instances](https://docs.microsoft.com/azure/container-instances/container-instances-tutorial-prepare-app)
- [Zelfstudie: Containerafbeeldingen in de cloud maken en implementeren met Azure Container Registry Tasks](https://docs.microsoft.com/azure/container-registry/container-registry-tutorial-quick-task).

## <a name="next-step"></a>Volgende stap

- [Maak uw containeraanbieding](https://docs.microsoft.com/azure/marketplace/partner-center-portal/create-azure-container-offer).
