---
title: Include-bestand
titleSuffix: Azure
description: Include-bestand
services: internet-peering
author: prmitiki
ms.service: internet-peering
ms.topic: include
ms.date: 11/27/2019
ms.author: prmitiki
ms.openlocfilehash: fa8d8ccef7d6ad6e1b5d9f19de61e45ee8c439fa
ms.sourcegitcommit: b9d4b8ace55818fcb8e3aa58d193c03c7f6aa4f1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82587881"
---
De Power shell **-cmdlet Get-AzPeeringLocation** retourneert een lijst met peering locaties met de `Kind`verplichte para meter, die u in latere stappen zult gebruiken.

```powershell
Get-AzPeeringLocation -Kind "Exchange"
```

Exchange-peering locaties bevatten de volgende velden:
* Exchangenaam
* PeeringLocation
* Land
* PeeringDBFacilityId
* PeeringDBFacilityLink
* MicrosoftIPv4Address
* MicrosoftIPv6Address

Controleer of u aanwezig bent bij de gewenste peering-faciliteit door te verwijzen naar [PeeringDB](https://www.peeringdb.com).

In dit voor beeld ziet u hoe u Seattle gebruikt als de peering-locatie voor het maken van een peering.

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
