---
title: Een verouderde directe peering converteren naar een Azure-resource met behulp van Power shell
titleSuffix: Azure
description: Een verouderde directe peering converteren naar een Azure-resource met behulp van Power shell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: ba41f4ad8014ba3e85174b7c32e11394f0068643
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775015"
---
# <a name="convert-a-legacy-direct-peering-to-azure-resource-using-powershell"></a>Een verouderde directe peering converteren naar een Azure-resource met behulp van Power shell

In dit artikel wordt beschreven hoe u een bestaande verouderde directe peering converteert naar een Azure-resource met behulp van Power shell-cmdlets.

Als u wilt, kunt u deze hand leiding volt ooien met behulp van de [Portal](howto-legacy-direct-portal.md).

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk [vereisten](prerequisites.md) en [direct peering-scenario](walkthrough-direct-all.md) voordat u begint met de configuratie.

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-legacy-direct-peering-to-azure-resource"></a>Verouderde directe peering converteren naar Azure-resource

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Meld u aan bij uw Azure-account en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name= get></a>Verouderde directe peering voor conversie ophalen
Hieronder ziet u een voor beeld van het ophalen van verouderde directe peering op Seattle peering-locatie

```powershell
$legacyPeering = Get-AzLegacyPeering `
    -Kind Direct -PeeringLocation "Seattle"
$legacyPeering
```

Hieronder ziet u een voor beeld van een antwoord:
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

### <a name="convert-legacy-direct-peering"></a>Verouderde directe peering converteren

&nbsp;
> [!IMPORTANT]
> Houd er rekening mee dat bij het converteren van verouderde peering naar Azure-resource geen wijzigingen worden ondersteund. &nbsp;

Gebruik de onderstaande opdracht om verouderde directe peering te converteren naar Azure-resource:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleDirectPeering" `
    -ResourceGroupName "PeeringResourceGroup" `

```

Hieronder ziet u een voor beeld van een antwoord:

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
U kunt gedetailleerde beschrijvingen van alle parameters krijgen door de volgende opdracht uit:

```powershell
Get-Help Get-AzPeering -detailed
```

Ga voor meer informatie naar [Veelgestelde vragen over Internet peering](faqs.md)

## <a name="next-steps"></a>Volgende stappen

* [Een directe peering maken of wijzigen met behulp van Power shell](howto-direct-powershell.md).
