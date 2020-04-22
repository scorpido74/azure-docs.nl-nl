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
ms.openlocfilehash: dbaa0b5fc87cb5393b323b8a9b7a38b72efe9518
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81680771"
---
De PowerShell-cmdlet **Get-AzPeeringLocation** retourneert een lijst `Kind`met peeringlocaties met de verplichte parameter , die u in latere stappen zult gebruiken.

```powershell
Get-AzPeeringLocation -Kind Direct
```

Directe peeringlocaties bevatten de volgende velden:
* PeeringLocatie 
* Land
* PeeringDBFacilityId
* PeeringDBFacilityLink
* Bandbreedtebiedt

Bevestig dat u aanwezig bent op de gewenste peering faciliteit door te verwijzen naar [PeeringDB](https://wwww.peeringdb.com).

In dit voorbeeld ziet u hoe u Seattle gebruiken als peeringlocatie om een direct peering te maken.

```powershell
$peeringLocations = Get-AzPeeringLocation -Kind Direct
$peeringLocation = $peeringLocations | where {$_.PeeringLocation -contains "Seattle"}
$peeringLocation

PeeringLocation       : Seattle
Address               : 2001 Sixth Avenue
Country               : US
PeeringDBFacilityId   : 71
PeeringDBFacilityLink : https://www.peeringdb.com/fac/71
BandwidthOffers       : {10Gbps, 100Gbps}
```