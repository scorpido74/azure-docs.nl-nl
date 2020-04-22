---
title: Azure Peering Service inschakelen op een Direct-peering met behulp van de Azure-portal
titleSuffix: Azure
description: Azure Peering Service inschakelen op een Direct-peering met behulp van de Azure-portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 625a17e5acff00f78c5a19725653eec629936f87
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687055"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-the-azure-portal"></a>Azure Peering Service inschakelen op een Direct-peering met behulp van de Azure-portal

In dit artikel wordt beschreven hoe u Azure [Peering Service](overview-peering-service.md) inschakelt op een Direct-peering met behulp van de Azure-portal.

Als u wilt, u deze handleiding voltooien met [PowerShell.](howto-peering-service-powershell.md)

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk de [vereisten](prerequisites.md) voordat u met de configuratie begint.
* Kies een Direct-peering in uw abonnement waarvoor u Peering Service wilt inschakelen. Als u er geen hebt, converteert u een verouderde Direct-peering of maakt u een nieuwe Direct-peering:
    * Als u een verouderd Direct-peering wilt converteren, volgt u de instructies in [Een verouderde directe peering converteren naar een Azure-bron met behulp van de portal](howto-legacy-direct-portal.md).
    * Als u een nieuwe Direct-peering wilt maken, volgt u de instructies in [Direct-peering maken of wijzigen met behulp van de portal](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Peering Service inschakelen op Direct peering

### <a name="view-direct-peering"></a><a name= get></a>Direct peering bekijken
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>De service Direct peering voor peering inschakelen

Nadat u een Direct-peering in de vorige stap hebt geopend, schakelt u deze in voor Peering Service.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Een verbinding met direct peering wijzigen

Zie de sectie Direct peeren wijzigen in [Direct peering wijzigen of wijzigen door de portal te gebruiken](howto-direct-portal.md)als u verbindingsinstellingen wilt wijzigen.

## <a name="next-steps"></a>Volgende stappen

* [Exchange-peering maken of wijzigen met behulp van de portal](howto-exchange-portal.md)
* [Een verouderde Exchange-peering converteren naar een Azure-bron met behulp van de portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Aanvullende bronnen

Zie de [veelgestelde vragen over peeringservice](service-faqs.md)voor veelgestelde vragen.