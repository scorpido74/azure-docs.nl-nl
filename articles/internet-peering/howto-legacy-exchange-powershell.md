---
title: Een verouderde Exchange-peering converteren naar een Azure-resource met behulp van Power shell
titleSuffix: Azure
description: Een verouderde Exchange-peering converteren naar een Azure-resource met behulp van Power shell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: eedf87548d62e05d4940911ed3dcd821077acb27
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81686781"
---
# <a name="convert-a-legacy-exchange-peering-to-an-azure-resource-by-using-powershell"></a>Een verouderde Exchange-peering converteren naar een Azure-resource met behulp van Power shell

In dit artikel wordt beschreven hoe u een bestaande verouderde Exchange-peering converteert naar een Azure-resource met behulp van Power shell-cmdlets.

Als u wilt, kunt u deze hand leiding volt ooien met behulp van Azure [Portal](howto-legacy-exchange-portal.md).

## <a name="before-you-begin"></a>Voordat u begint
* Controleer de [vereisten](prerequisites.md) en de [Exchange peering-walkthrough](walkthrough-exchange-all.md) voordat u begint met de configuratie.

### <a name="work-with-azure-powershell"></a>Werken met Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-an-azure-resource"></a>Een verouderde Exchange-peering converteren naar een Azure-resource

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Meld u aan bij uw Azure-account en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name="get-legacy-exchange-peering-for-conversion"></a><a name= get></a>Oudere uitwisselings peering voor conversie ophalen
In dit voor beeld ziet u hoe u verouderde uitwisseling van Exchange kunt verkrijgen op de locatie in Seattle peering:

```powershell
$legacyPeering = Get-AzLegacyPeering -Kind Exchange -PeeringLocation "Seattle"
$legacyPeering
```

Het antwoord ziet er ongeveer uit als in het volgende voor beeld:
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

### <a name="convert-legacy-peering"></a>Verouderde peering converteren
Deze opdracht kan worden gebruikt om verouderde uitwisseling van Exchange naar een Azure-resource te converteren:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Wanneer u verouderde peering converteert naar een Azure-resource, worden wijzigingen niet ondersteund.
&nbsp;

In dit voor beeld wordt weer gegeven wanneer de end-to-end-inrichting is voltooid:

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
U kunt gedetailleerde beschrijvingen van alle para meters verkrijgen door de volgende opdracht uit te voeren:

```powershell
Get-Help Get-AzPeering -detailed
```
Zie [Veelgestelde vragen over Internet peering](faqs.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

* [Een Exchange-peering maken of wijzigen met behulp van Power shell](howto-exchange-powershell.md)
