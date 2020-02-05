---
title: Gegevens structuren van de Mobility-service in Azure Maps | Microsoft Azure kaarten
description: In dit artikel vindt u informatie over algemene velden en gegevens structuren die worden geretourneerd via de Microsoft Azure Maps Mobility Services.
author: walsehgal
ms.author: v-musehg
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: ceecdcc508e5b43c8775b6a88f9b4e4f0eb23c77
ms.sourcegitcommit: 4f6a7a2572723b0405a21fea0894d34f9d5b8e12
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/04/2020
ms.locfileid: "76989005"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Gegevens structuren in de Azure Maps Mobility-service

In dit artikel wordt het concept van metro gebied in [Azure Maps Mobility-service](https://aka.ms/AzureMapsMobilityService)geïntroduceerd. We bespreken enkele algemene velden die worden geretourneerd wanneer deze service wordt opgevraagd voor open bare doorvoer onderbrekingen en-lijnen. We raden u aan dit artikel te lezen voordat u met de Mobility service-Api's ontwikkelt.

## <a name="metro-area"></a>Metro gebied

De gegevens van de Mobility-service worden gegroepeerd op ondersteunde metro gebieden. Metro gebieden volgen niet de grenzen van steden. Een metro gebied kan meerdere steden, gevulde stad en rond steden bevatten. Een land/regio kan eigenlijk een metro gebied zijn. 

De `metroID` is de ID van een metro gebied dat kan worden gebruikt voor het aanroepen van de informatie-API voor het ophalen van een [metro gebied](https://aka.ms/AzureMapsMobilityMetroAreaInfo). Gebruik Azure Maps ' "een metro-API verkrijgen" om doorvoer typen, doorvoer instanties, actieve waarschuwingen en aanvullende Details voor de gekozen metro lijn aan te vragen. U kunt ook de ondersteunde metro gebieden en metroIDs aanvragen. De Id's van een metro gebied kunnen worden gewijzigd.

**metroID:** 522- **naam:** Seattle-Tacoma-Bellevue

![Seattle-metro-area](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Stop-Id's

Er kunnen door doorvoer beëindigingen worden verwezen door twee typen Id's, de GFTS-id [(General Transit feed Specification)](https://gtfs.org/) en de Azure Maps stop-id. De GFTS-ID wordt aangeduid als de stopKey en de Azure Maps stop-ID wordt stopID genoemd. Wanneer vaak door Voer wordt geadviseerd, wordt u aangeraden de Azure Maps stop-ID te gebruiken. stopID is stabieler en waarschijnlijk hetzelfde, zolang de fysieke stop bestaat. De GTFS-stop-ID wordt vaker bijgewerkt. Bijvoorbeeld, GTFS stop-ID kan worden bijgewerkt per GTFS provider aanvraag of wanneer een nieuwe GTFS-versie wordt uitgebracht. Hoewel de fysieke stop geen wijziging heeft, kan de GTFS-stop-ID worden gewijzigd.

Als u wilt beginnen, kunt u stoppen met het verzoek om een transit op te halen met behulp van de [API](https://aka.ms/AzureMapsMobilityNearbyTransit)voor het

## <a name="line-groups-and-lines"></a>Lijn groepen en-regels

Mobility service maakt gebruik van een parallel gegevens model voor lijnen en lijn groepen. Dit model wordt gebruikt om beter te kunnen omgaan met wijzigingen die zijn overgenomen van [GTFS](https://gtfs.org/) -routes en de TRIPS-gegevens.


### <a name="line-groups"></a>Regel groepen

Een regel groep is een entiteit waarmee alle regels die logisch deel uitmaken van dezelfde groep worden gegroepeerd. Normaal gesp roken bevat een lijn groep twee regels, één van punt A tot en met B, en de andere retour van punt B naar A. Beide regels behoren tot hetzelfde open bare transport Agentschap en hebben hetzelfde regel nummer. Het kan echter voor komen dat een regel groep meer dan twee regels of één regel bevat.


### <a name="lines"></a>Transferorderregels

Zoals hierboven is beschreven, bestaat elke regel groep uit een set regels. Elke regel groep bestaat uit twee regels en elke regel beschrijft een richting.  Er zijn echter gevallen waarin meer regels een regel groep vormen. Er is bijvoorbeeld een regel die soms een bepaalde groep afrondt en soms niet. In beide gevallen werkt deze onder hetzelfde regel nummer. Een regel groep kan ook bestaan uit één regel. Een ronde lijn met één richting is een groep met één regel.

Als u wilt beginnen, kunt u regel groepen aanvragen met behulp van de [regel-API voor transit ophalen](https://aka.ms/AzureMapsMobilityTransitLine).


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het aanvragen van doorvoer gegevens met de Mobility-service:

> [!div class="nextstepaction"]
> [Transit gegevens aanvragen](how-to-request-transit-data.md)

Meer informatie over het aanvragen van real-time gegevens met behulp van Mobility service:

> [!div class="nextstepaction"]
> [Real-time gegevens aanvragen](how-to-request-real-time-data.md)

De documentatie voor de Azure Maps Mobility Service API verkennen

> [!div class="nextstepaction"]
> [API-documentatie voor Mobility service](https://aka.ms/AzureMapsMobilityService)
