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
ms.openlocfilehash: 86d768db7a31c634bdaca6c93f633c7bbaf10a65
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75774287"
---
PowerShell-cmdlet **Get-AzPeeringLocation** retourneert een lijst met `Kind`peeringlocaties met de verplichte parameter , die u in latere stappen zult gebruiken:

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

Hieronder vindt u een voorbeeld dat laat zien hoe u Seattle gebruiken als peeringlocatie om een direct peering te maken:

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