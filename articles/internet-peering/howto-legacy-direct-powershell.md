---
title: Een verouderde directe peering naar een Azure-resource converteren met behulp van Power shell
titleSuffix: Azure
description: Een verouderde directe peering naar een Azure-resource converteren met behulp van Power shell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: how-to
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 803d2a09fb4114dae36dcb6147139c7b657501f8
ms.sourcegitcommit: 537c539344ee44b07862f317d453267f2b7b2ca6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/11/2020
ms.locfileid: "84700124"
---
# <a name="convert-a-legacy-direct-peering-to-an-azure-resource-by-using-powershell"></a>Een verouderde directe peering naar een Azure-resource converteren met behulp van Power shell

In dit artikel wordt beschreven hoe u een bestaande verouderde directe peering converteert naar een Azure-resource met behulp van Power shell-cmdlets.

Als u wilt, kunt u deze hand leiding volt ooien met behulp van Azure [Portal](howto-legacy-direct-portal.md).

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk de [vereisten](prerequisites.md) en de [Stapsgewijze instructies voor direct peering](walkthrough-direct-all.md) voordat u begint met de configuratie.

### <a name="work-with-azure-powershell"></a>Werken met Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-direct-peering-to-an-azure-resource"></a>Een verouderde directe peering converteren naar een Azure-resource

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Meld u aan bij uw Azure-account en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-a-legacy-direct-peering-for-conversion"></a><a name= get></a>Een verouderde directe peering voor conversie ophalen
In dit voor beeld ziet u hoe u een verouderde directe peering op de vestiging Seattle peering krijgt.

```powershell
$legacyPeering = Get-AzLegacyPeering `
    -Kind Direct -PeeringLocation "Seattle"
$legacyPeering
```

Hieronder volgt een voorbeeld van een antwoord:
```powershell
Name                       :
Sku                        : Basic_Direct_Free
Kind                       : Direct
PeeringLocation            : Seattle
UseForPeeringService       : False
PeerAsn.Id                 :
Connection                 : ------------------------
PeeringDBFacilityId        : 71
SessionPrefixIPv4          : 4.71.156.72/30
PeerSessionIPv4Address     : 4.71.156.73
MicrosoftIPv4Address       : 4.71.156.74
SessionStateV4             : Established
MaxPrefixesAdvertisedV4    : 20000
SessionPrefixIPv6          : 2001:1900:2100::1e10/126
MaxPrefixesAdvertisedV6    : 2000
ConnectionState            : Active
BandwidthInMbps            : 0
ProvisionedBandwidthInMbps : 20000
Connection                 : ------------------------
PeeringDBFacilityId        : 71
SessionPrefixIPv4          : 4.68.70.140/30
PeerSessionIPv4Address     : 4.68.70.141
MicrosoftIPv4Address       : 4.68.70.142
SessionStateV4             : Established
MaxPrefixesAdvertisedV4    : 20000
SessionPrefixIPv6          : 2001:1900:4:3::cc/126
PeerSessionIPv6Address     : 2001:1900:4:3::cd
MicrosoftIPv6Address       : 2001:1900:4:3::ce
SessionStateV6             : Established
MaxPrefixesAdvertisedV6    : 2000
ConnectionState            : Active
BandwidthInMbps            : 0
ProvisionedBandwidthInMbps : 20000
ProvisioningState          : Succeeded
```

### <a name="convert-a-legacy-direct-peering"></a>Een verouderde directe peering converteren

&nbsp;
> [!IMPORTANT]
> Wanneer u een verouderde peering converteert naar een Azure-resource, worden wijzigingen niet ondersteund. &nbsp;

Gebruik deze opdracht om een verouderde directe peering te converteren naar een Azure-resource:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleDirectPeering" `
    -ResourceGroupName "PeeringResourceGroup" `

```

Hieronder volgt een voorbeeld van een antwoord:

```powershell
Name                 : SeattleDirectPeering
Sku.Name             : Basic_Direct_Free
Kind                 : Direct
Connections          : {11, 11}
PeerAsn.Id           : /subscriptions/{subscriptionId}/providers/Microsoft.Peering/peerAsns/{asnNumber}
UseForPeeringService : False
PeeringLocation      : Seattle
ProvisioningState    : Succeeded
Location             : centralus
Id                   : /subscriptions/{subscriptionId}/resourceGroups/PeeringResourceGroup/providers/Microsoft.Peering/peerings/SeattleDirectPeering
Type                 : Microsoft.Peering/peerings
Tags                 : {}
```

## <a name="additional-resources"></a>Aanvullende bronnen
U kunt gedetailleerde beschrijvingen van alle para meters verkrijgen door de volgende opdracht uit te voeren:

```powershell
Get-Help Get-AzPeering -detailed
```

Zie [Veelgestelde vragen over Internet peering](faqs.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Een directe peering maken of wijzigen met behulp van Power shell](howto-direct-powershell.md)
