---
title: Een verouderde Exchange-peering converteren naar een Azure-bron met PowerShell
titleSuffix: Azure
description: Een verouderde Exchange-peering converteren naar een Azure-bron met PowerShell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: eedf87548d62e05d4940911ed3dcd821077acb27
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686781"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-powershell"></a>Een verouderde Exchange-peering converteren naar een Azure-bron met PowerShell

In dit artikel wordt beschreven hoe u een bestaande verouderde Exchange-peering converteert naar een Azure-bron met PowerShell-cmdlets.

Als u dat liever hebt, u deze handleiding voltooien met behulp van de [Azure-portal.](howto-legacy-exchange-portal.md)

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk de [vereisten](prerequisites.md) en de [Exchange-peering-walkthrough](walkthrough-exchange-all.md) voordat u met de configuratie begint.

### <a name="work-with-azure-powershell"></a>Werken met Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Een verouderde Exchange-peering converteren naar een Azure-bron

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Meld u aan bij uw Azure-account en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>Ontvang legacy Exchange-peering voor conversie
In dit voorbeeld ziet u hoe u legacy Exchange-peering krijgen op de locatie voor peering in Seattle:

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
Deze opdracht kan worden gebruikt om verouderde Exchange-peering om te zetten naar een Azure-bron:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Wanneer u legacy-peering converteert naar een Azure-bron, worden wijzigingen niet ondersteund.
&nbsp;

In dit voorbeeldwordt weergegeven wanneer de end-to-end provisioning is voltooid:

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
Zie [veelgestelde vragen over internetpeering voor](faqs.md)meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Een Exchange-peering maken of wijzigen met PowerShell](howto-exchange-powershell.md)
