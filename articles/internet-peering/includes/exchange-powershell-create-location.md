---
title: bestand opnemen
titleSuffix: Azure
description: bestand opnemen
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: 6f5d2dc30ac0f6316587fa0836b87cbd4efc0a8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774391"
---
PowerShell-cmdlet **Get-AzPeeringLocation** retourneert een lijst met `Kind`peeringlocaties met de verplichte parameter , die u in latere stappen zult gebruiken:

```powershell
Get-AzPeeringLocation -Kind "Exchange"
```

Exchange-peeringlocaties bevatten de volgende velden:
* ExchangeName
* PeeringLocatie
* Land
* PeeringDBFacilityId
* PeeringDBFacilityLink
* MicrosoftIPv4Adres
* MicrosoftIPv6Adres

Bevestig dat u aanwezig bent op de gewenste peering faciliteit door te verwijzen naar [PeeringDB](https://wwww.peeringdb.com).

Hieronder vindt u een voorbeeld dat laat zien hoe u Seattle gebruiken als peeringlocatie om een peering te maken:

```powershell
$exchangeLocations = Get-AzPeeringLocation -Kind Exchange
$exchangeLocation = $exchangeLocations | where {$_.PeeringLocation -eq "Seattle"}

#check the location metadata
$exchangeLocation

ExchangeName          : Columbia IX
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 99999
PeeringDBFacilityLink : https://www.peeringdb.com/ix/99999
MicrosoftIPv4Address  : 10.12.97.129
MicrosoftIPv6Address  :

ExchangeName          : Equinix Seattle
PeeringLocation       : Seattle
Country               : US
PeeringDBFacilityId   : 11
PeeringDBFacilityLink : https://www.peeringdb.com/ix/11
MicrosoftIPv4Address  : 198.32.134.152
MicrosoftIPv6Address  : 2001:504:12::15

...

```