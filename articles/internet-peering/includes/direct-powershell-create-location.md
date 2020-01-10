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
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75774287"
---
Power shell **-cmdlet Get-AzPeeringLocation** retourneert een lijst met peering locaties met de verplichte para meter `Kind`, die u in latere stappen gaat gebruiken:

```powershell
Get-AzPeeringLocation -Kind Direct
```

Directe peering-locaties bevatten de volgende velden:
* PeeringLocation 
* Land/regio
* PeeringDBFacilityId
* PeeringDBFacilityLink
* BandwidthOffers

Controleer of u aanwezig bent bij de gewenste peering-faciliteit door te verwijzen naar [PeeringDB](https://w www.peeringdb.com).

Hieronder ziet u een voor beeld waarin wordt uitgelegd hoe u Seattle kunt gebruiken als de peering-locatie voor het maken van een directe peering:

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