---
title: Een subnet delegering toevoegen aan of verwijderen uit een virtueel Azure-netwerk
titlesuffix: Azure Virtual Network
description: Meer informatie over het toevoegen of verwijderen van een gedelegeerd subnet voor een service in Azure in Azure.
services: virtual-network
documentationcenter: na
author: KumudD
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2019
ms.author: kumud
ms.openlocfilehash: 9bf44aa5ab18f94ec91650548dc13360ce1b1f3d
ms.sourcegitcommit: f2d9d5133ec616857fb5adfb223df01ff0c96d0a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/03/2019
ms.locfileid: "71938497"
---
# <a name="add-or-remove-a-subnet-delegation"></a>Een subnet delegering toevoegen of verwijderen

Subnet delegering geeft expliciete machtigingen voor de service om servicespecifieke bronnen in het subnet te maken met behulp van een unieke id bij het implementeren van de service. In dit artikel wordt beschreven hoe u een gedelegeerd subnet toevoegt of verwijdert voor een Azure-service.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij Azure Portal op https://portal.azure.com.

## <a name="create-the-virtual-network"></a>Het virtuele netwerk maken

In deze sectie maakt u een virtueel netwerk en het subnet dat u later wilt delegeren aan een Azure-service.

1. Selecteer linksboven in het scherm **Een resource maken** > **Netwerken** > **Virtueel netwerk**.
1. Typ of selecteer in **Virtueel netwerk maken** de volgende gegevens:

    | Instelling | Waarde |
    | ------- | ----- |
    | Name | Voer *MyVirtualNetwork*in. |
    | Adresruimte | Voer *10.0.0.0/16* in. |
    | Subscription | Selecteer uw abonnement.|
    | Resource group | Selecteer **Nieuwe maken**, voer *myResourceGroup* in en selecteer vervolgens **OK**. |
    | Location | Selecteer **oostus**.|
    | Subnet - Naam | Voer *mySubnet*in. |
    | Subnet - adresbereik | Voer *10.0.0.0/24* in. |
    |||
1. Laat de rest als standaard staan en selecteer vervolgens **maken**.

## <a name="delegate-a-subnet-to-an-azure-service"></a>Een subnet overdragen aan een Azure-service

In deze sectie delegeert u het subnet dat u in de voor gaande sectie hebt gemaakt, naar een Azure-service.

1. Voer in de zoekbalk van de portal *myVirtualNetwork* in. Wanneer **myVirtualNetwork** wordt weergegeven in de zoekresultaten, selecteert u dit.
2. Selecteer *myVirtualNetwork*in de zoek resultaten.
3. Selecteer **subnetten**onder **instellingen**en selecteer vervolgens **mySubnet**.
4. Selecteer op de pagina *mySubnet* voor de lijst **subnet delegering** een van de services die worden vermeld onder **subnet delegeren aan een service** (bijvoorbeeld **micro soft. DBforPostgreSQL/serversv2**).  

## <a name="remove-subnet-delegation-from-an-azure-service"></a>Subnet delegering verwijderen van een Azure-service

1. Voer in de zoekbalk van de portal *myVirtualNetwork* in. Wanneer **myVirtualNetwork** wordt weergegeven in de zoekresultaten, selecteert u dit.
2. Selecteer *myVirtualNetwork*in de zoek resultaten.
3. Selecteer **subnetten**onder **instellingen**en selecteer vervolgens **mySubnet**.
4. Selecteer op de pagina *mySubnet* voor de lijst **subnet delegering** de optie **geen** van de services die worden vermeld onder **subnet overdragen aan een service**. 

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [beheren van subnetten in azure](virtual-network-manage-subnet.md).
