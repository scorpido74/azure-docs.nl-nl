---
title: Azure Peering Service inschakelen op een Direct-peering met PowerShell
titleSuffix: Azure
description: Azure Peering Service inschakelen op een Direct-peering met PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: c029b822d00ce545e3623a6212421a55b2d1971f
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686976"
---
# <a name="enable-azure-peering-service-on-a-direct-peering-by-using-powershell"></a>Azure Peering Service inschakelen op een Direct-peering met PowerShell

In dit artikel wordt beschreven hoe u Azure [Peering Service](overview-peering-service.md) inschakelt op een Direct-peering met PowerShell-cmdlets en het Azure Resource Manager-implementatiemodel.

Als u dat liever hebt, u deze handleiding voltooien met behulp van de [Azure-portal.](howto-peering-service-portal.md)

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk de [vereisten](prerequisites.md) voordat u met de configuratie begint.
* Kies een Direct-peering in uw abonnement waarvoor u Peering Service wilt inschakelen. Als u er geen hebt, converteert u een verouderde Direct-peering of maakt u een nieuwe Direct-peering:
    * Als u een verouderd Direct-peering wilt converteren, volgt u de instructies in [Een verouderd Direct-peering converteren naar een Azure-bron met PowerShell](howto-legacy-direct-powershell.md).
    * Als u een nieuwe Direct-peering wilt maken, volgt u de instructies in [Direct-peering maken of wijzigen met PowerShell](howto-direct-powershell.md).

### <a name="work-with-azure-powershell"></a>Werken met Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="enable-peering-service-on-a-direct-peering"></a>Peering Service inschakelen op Direct peering

### <a name="view-direct-peering"></a><a name= get></a>Direct peering bekijken
[!INCLUDE [peering-direct-get](./includes/direct-powershell-get.md)]

### <a name="enable-the-direct-peering-for-peering-service"></a><a name= get></a>De service Direct peering voor peering inschakelen

Nadat u Direct-peering in de vorige stap hebt gekregen, schakelt u deze in voor Peering Service.
[!INCLUDE [peering-direct-modify](./includes/peering-service-direct-powershell.md)]

## <a name="modify-a-direct-peering-connection"></a>Een verbinding met direct peering wijzigen

Als u verbindingsinstellingen wilt wijzigen, raadpleegt u de sectie Direct peering wijzigen in [Een direct peering maken of wijzigen met PowerShell](howto-direct-powershell.md).

## <a name="next-steps"></a>Volgende stappen

* [Exchange-peering maken of wijzigen met PowerShell](howto-exchange-powershell.md)
* [Een verouderde Exchange-peering converteren naar een Azure-bron met PowerShell](howto-legacy-exchange-powershell.md)

## <a name="additional-resources"></a>Aanvullende bronnen
U gedetailleerde beschrijvingen van alle parameters krijgen door de volgende opdracht uit te voeren:

```powershell
Get-Help Get-AzPeering -detailed
```

Zie de [veelgestelde vragen over peeringservice](service-faqs.md)voor veelgestelde vragen.