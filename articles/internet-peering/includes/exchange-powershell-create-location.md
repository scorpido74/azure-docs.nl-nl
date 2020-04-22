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
ms.openlocfilehash: 2ebf5d574b4d185953ab0f7984648c440d6f107e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81678626"
---
De PowerShell-cmdlet **Get-AzPeeringLocation** retourneert een lijst `Kind`met peeringlocaties met de verplichte parameter , die u in latere stappen zult gebruiken.

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

In dit voorbeeld ziet u hoe u Seattle gebruiken als peeringlocatie om een peering te maken.

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