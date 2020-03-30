---
title: Peering Service inschakelen op Direct peering met de portal
titleSuffix: Azure
description: Peering Service inschakelen op Direct peering met de portal
services: internet-peering
author: derekolo
ms.service: internet-peering
ms.topic: article
ms.date: 3/18/2020
ms.author: derekol
ms.openlocfilehash: 87a65826a338f4098ae24c33939ea7f9d4475e36
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80129864"
---
# <a name="enable-peering-service-on-a-direct-peering-using-the-portal"></a>Peering Service inschakelen op Direct peering met de portal

In dit artikel wordt beschreven hoe [u Peering Service](overview-peering-service.md) op een Direct-peering inschakelen met behulp van de portal.

Als u dat liever hebt, u deze handleiding voltooien met behulp van de [PowerShell.](howto-peering-service-powershell.md)

## <a name="before-you-begin"></a>Voordat u begint
* Controleer [vereisten](prerequisites.md) voordat u met de configuratie begint.
* Kies een Direct-peering in uw abonnement waarop u Peering Service wilt inschakelen. Als u er geen hebt, converteert u een verouderde Direct-peering of maakt u een nieuwe Direct-peering.
    * Als u een verouderd Direct-peering wilt converteren, volgt u de instructies in [Een verouderde directe peering converteren naar Azure-bron met behulp van de portal.](howto-legacy-direct-portal.md)
    * Als u een nieuwe Direct-peering wilt maken, volgt u de instructies in [Direct-peering maken of wijzigen met behulp van de portal](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Peering Service inschakelen op Direct peering

### <a name="view-direct-peering"></a><a name= get></a>Direct peering bekijken
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>De service Direct peering voor peering inschakelen

Nadat u Direct-peering in de vorige stap hebt geopend, schakelt u deze in voor Peering Service.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Een verbinding met direct peering wijzigen

Als u verbindingsinstellingen wilt wijzigen, raadpleegt u Een sectie **Direct peering wijzigen** in Een direct [peering maken of wijzigen met behulp van de portal](howto-direct-portal.md)

## <a name="next-steps"></a>Volgende stappen

* [Exchange-peering maken of wijzigen met behulp van de portal](howto-exchange-portal.md)
* [Een verouderde Exchange-peering converteren in een Azure-resource met de portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Aanvullende bronnen

Zie [Veelgestelde vragen](service-faqs.md)voor veelgestelde vragen.