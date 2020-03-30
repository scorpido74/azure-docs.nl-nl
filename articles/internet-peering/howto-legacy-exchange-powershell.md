---
title: Een verouderde Exchange-peering converteren naar Azure-bron met PowerShell
titleSuffix: Azure
description: Een verouderde Exchange-peering converteren naar Azure-bron met PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: aa11f329cf0a0cb27d58b940b42731a2ec41c272
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75775392"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-powershell"></a>Een verouderde Exchange-peering converteren naar Azure-bron met PowerShell

In dit artikel wordt beschreven hoe u een bestaande verouderde Exchange-peering converteert naar Azure-bronnen met PowerShell-cmdlets.

Als u dat liever hebt, u deze handleiding voltooien via de [portal.](howto-legacy-exchange-portal.md)

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk [vereisten](prerequisites.md) en [exchange-peering-walkthrough](walkthrough-exchange-all.md) voordat u met de configuratie begint.

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Een verouderde Exchange-peering converteren in een Azure-resource

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Meld u aan bij uw Azure-account en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>Ontvang legacy Exchange-peering voor conversie
Hieronder vindt u het voorbeeld om legacy Exchange-peering op seattle-peering-locatie te krijgen:

```powershell
$legacyPeering = Get-AzLegacyPeering -Kind Exchange -PeeringLocation "Seattle"
$legacyPeering
```

Het antwoord lijkt op het volgende voorbeeld:
```powershell
    Kind                     : Exchange
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```

### <a name="convert-legacy-peering"></a>Legacy peering converteren
Onder de opdracht kan worden gebruikt om verouderde Exchange-peering om te zetten in Azure-bron:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Houd er rekening mee dat wijzigingen bij het converteren van legacy-peering naar azure-bron niet worden ondersteund&nbsp;

Hieronder vindt u een voorbeeldreactie wanneer de end-to-end provisioning met succes is voltooid:

```powershell
    Name                     : SeattleExchangePeering
    Kind                     : Exchange
    Sku                      : Basic_Exchange_Free
    PeeringLocation          : Seattle
    PeerAsn                  : 65000
    Connection               : ------------------------
    PeerSessionIPv4Address   : 10.21.31.100
    MicrosoftIPv4Address     : 10.21.31.50
    SessionStateV4           : Established
    MaxPrefixesAdvertisedV4  : 20000
    PeerSessionIPv6Address   : fe01::3e:100
    MicrosoftIPv6Address     : fe01::3e:50
    SessionStateV6           : Established
    MaxPrefixesAdvertisedV6  : 2000
    ConnectionState          : Active
```
## <a name="additional-resources"></a>Aanvullende bronnen
U gedetailleerde beschrijvingen van alle parameters krijgen door de volgende opdracht uit te voeren:

```powershell
Get-Help Get-AzPeering -detailed
```
Ga voor meer informatie naar [veelgestelde vragen over internetpeering](faqs.md)

## <a name="next-steps"></a>Volgende stappen

* [Een Exchange-peering maken of wijzigen met PowerShell](howto-exchange-powershell.md)
