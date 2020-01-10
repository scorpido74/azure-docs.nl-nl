---
title: Een verouderde Exchange-peering converteren naar Azure-resource met behulp van Power shell
titleSuffix: Azure
description: Een verouderde Exchange-peering converteren naar Azure-resource met behulp van Power shell
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: article
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: aa11f329cf0a0cb27d58b940b42731a2ec41c272
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75775392"
---
# <a name="convert-a-legacy-exchange-peering-to-azure-resource-using-powershell"></a>Een verouderde Exchange-peering converteren naar Azure-resource met behulp van Power shell

In dit artikel wordt beschreven hoe u een bestaande verouderde Exchange-peering converteert naar Azure-resource met behulp van Power shell-cmdlets.

Als u wilt, kunt u deze hand leiding volt ooien met behulp van de [Portal](howto-legacy-exchange-portal.md).

## <a name="before-you-begin"></a>Voordat u begint
* Bekijk de [vereisten](prerequisites.md) en [Exchange-peering-instructies](walkthrough-exchange-all.md) voordat u begint met de configuratie.

### <a name="working-with-azure-powershell"></a>Werken met Azure PowerShell
[!INCLUDE [CloudShell](./includes/cloudshell-powershell-about.md)]

## <a name="convert-a-legacy-exchange-peering-to-azure-resource"></a>Een verouderde Exchange-peering converteren naar Azure-resource

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Meld u aan bij uw Azure-account en selecteer uw abonnement
[!INCLUDE [Account](./includes/account-powershell.md)]

### <a name= get></a>Oudere uitwisselings peering voor conversie ophalen
Hieronder ziet u het voor beeld voor het verkrijgen van verouderde uitwisseling van Exchange op vestiging Seattle-peering:

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

### <a name="convert-legacy-peering"></a>Verouderde peering converteren
De onderstaande opdracht kan worden gebruikt om verouderde uitwisseling van Exchange naar Azure-resource te converteren:

```powershell
$legacyPeering[0] | New-AzPeering `
    -Name "SeattleExchangePeering" `
    -ResourceGroupName "PeeringResourceGroup"

```

&nbsp;
> [!IMPORTANT] 
> Houd er rekening mee dat bij het converteren van verouderde peering naar Azure-resource geen wijzigingen worden ondersteund &nbsp;

Hieronder ziet u een voor beeld van een reactie wanneer de end-to-end-inrichting is voltooid:

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
U kunt gedetailleerde beschrijvingen van alle parameters krijgen door de volgende opdracht uit:

```powershell
Get-Help Get-AzPeering -detailed
```
Ga voor meer informatie naar [Veelgestelde vragen over Internet peering](faqs.md)

## <a name="next-steps"></a>Volgende stappen

* [Een Exchange-peering maken of wijzigen met Power shell](howto-exchange-powershell.md)
