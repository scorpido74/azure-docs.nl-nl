---
title: Peering-service inschakelen op directe peering met behulp van de portal
titleSuffix: Azure
description: Peering-service inschakelen op directe peering met behulp van de portal
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: d6b67c42ef8a5ba5ae98894775d1f56cee39ba8b
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774976"
---
# <a name="enable-peering-service-on-a-direct-peering-using-the-portal"></a>Peering-service inschakelen op directe peering met behulp van de portal

In dit artikel wordt beschreven hoe u de [peering-service](overview-peering-service.md) inschakelt op een directe peering met behulp van de portal.

Als u wilt, kunt u deze hand leiding volt ooien met behulp van [Power shell](howto-peering-service-powershell.md).

## <a name="before-you-begin"></a>Voordat u begint
* Controleer de [vereisten](prerequisites.md) voordat u begint met de configuratie.
* Kies een directe peering in uw abonnement waarvoor u de peering-service wilt inschakelen. Als u er nog geen hebt, kunt u een verouderde directe peering converteren of een nieuwe directe peering maken.
    * Als u een verouderde directe peering wilt converteren, volgt u de instructies in [een verouderde directe peering converteren naar Azure-resource met behulp van de portal](howto-legacy-direct-portal.md).
    * Als u een nieuwe directe peering wilt maken, volgt u de instructies in [een directe peering maken of wijzigen met behulp van de portal](howto-direct-portal.md).

## <a name="enable-peering-service-on-a-direct-peering"></a>Peering-service inschakelen op directe peering

### <a name= get></a>Directe peering weer geven
[!INCLUDE [peering-direct-get-portal](./includes/direct-portal-get.md)]

### <a name= get></a>De directe peering inschakelen voor de peering-service

Als u direct peering in de vorige stap hebt geopend, schakelt u deze in voor de peering-service.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-portal.md)]

## <a name="modify-a-direct-peering-connection"></a>Een directe peering-verbinding wijzigen

Als u de verbindings instellingen wilt wijzigen, raadpleegt u de sectie **direct peering wijzigen** in [een directe peering maken of wijzigen met behulp van de portal](howto-direct-portal.md)

## <a name="next-steps"></a>Volgende stappen

* [Exchange-peering maken of wijzigen met behulp van de portal](howto-exchange-portal.md)
* [Een verouderde uitwisseling van Exchange naar Azure-resource converteren met behulp van de portal](howto-legacy-exchange-portal.md)

## <a name="additional-resources"></a>Aanvullende bronnen

Zie [Veelgestelde vragen over peering-service](service-faqs.md)voor meer informatie.