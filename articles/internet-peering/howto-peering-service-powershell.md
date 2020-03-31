---
title: Peering Service inschakelen op een Direct-peering met PowerShell
titleSuffix: Azure
description: Peering Service inschakelen op een Direct-peering met PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ac843fa5440f7ba085d33cd897bcd4a1722f77ac
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774183"
---
# <a name="enable-peering-service-on-a-direct-peering-using-powershell"></a>Peering Service inschakelen op een Direct-peering met PowerShell

In dit artikel wordt beschreven hoe [u Peering Service](overview-peering-service.md) inschakelt op een Direct-peering met PowerShell-cmdlets en het implementatiemodel Resource Manager.

Als u dat liever hebt, u deze handleiding voltooien via de [portal.](howto-peering-service-portal.md)

## <a name="before-you-begin"></a>Voordat u begint
* Controleer [vereisten](prerequisites.md) voordat u met de configuratie begint.
* Kies een Direct-peering in uw abonnement waarop u Peering Service wilt inschakelen. Als u er geen hebt, converteert u een verouderde Direct-peering of maakt u een nieuwe Direct-peering.
    * Als u een verouderd Direct-peering wilt converteren, volgt u de instructies in [Een verouderde directe peering converteren naar Azure-bron met PowerShell](howto-legacy-direct-powershell.md).
    * Als u een nieuwe Direct-peering wilt maken, volgt u de instructies in [Direct-peering maken of wijzigen met PowerShell](howto-direct-powershell.md).

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Peering Service inschakelen op Direct peering

### <a name="view-direct-peering"></a><a name= get></a>Direct peering bekijken
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>De service Direct peering voor peering inschakelen

Nadat u Direct-peering in de vorige stap hebt ingeschakeld, schakelt u deze in voor Peering Service.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Een verbinding met direct peering wijzigen

Als u verbindingsinstellingen wilt wijzigen, raadpleegt u Een sectie **Direct peering wijzigen** in Een direct [peering maken of wijzigen met PowerShell](howto-direct-powershell.md)

## <a name="next-steps"></a>Volgende stappen

* [Exchange-peering maken of wijzigen met PowerShell](howto-exchange-powershell.md)
* [Een verouderde Exchange-peering converteren naar Azure-bron met PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Aanvullende bronnen
U gedetailleerde beschrijvingen van alle parameters krijgen door de volgende opdracht uit te voeren:

```powershell
Get-Help Get-AzPeering -detailed
```

Zie [Veelgestelde vragen](service-faqs.md)voor veelgestelde vragen.