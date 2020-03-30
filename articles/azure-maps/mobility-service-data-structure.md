---
title: Gegevensstructuren van Mobility Service in Azure Maps| Microsoft Azure Maps
description: In dit artikel leert u over algemene velden en gegevensstructuren die worden geretourneerd via de Microsoft Azure Maps Mobility Services.
author: philmea
ms.author: philmea
ms.date: 06/05/2019
ms.topic: conceptual
ms.service: azure-maps
services: azure-maps
manager: philmea
ms.openlocfilehash: 30696c5dcb3353ea468aa78dbc107dae4d292edb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80334448"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Gegevensstructuren in Azure Maps Mobility Service

Dit artikel introduceert het concept van Metro Area in [Azure Maps Mobility Service](https://aka.ms/AzureMapsMobilityService). We bespreken enkele van de gemeenschappelijke velden die worden geretourneerd wanneer deze service wordt opgevraagd voor haltes en lijnen voor het openbaar vervoer. We raden je aan dit artikel te lezen voordat je ons ontwikkelt met de Mobiliteitsservice API's.

## <a name="metro-area"></a>Metrogebied

Mobility Service-gegevens worden gegroepeerd op ondersteunde metrogebieden. Metrogebieden volgen de stadsgrenzen niet. Een metrogebied kan meerdere steden, dichtbevolkte steden en omliggende steden bevatten. In feite kan een land / regio een metrogebied zijn. 

De `metroID` ID van een metrogebied kan worden gebruikt om de [Get Metro Area Info API](https://aka.ms/AzureMapsMobilityMetroAreaInfo)aan te roepen. Gebruik de API 'Metro' van Azure Maps om transittypen, transitbureaus, actieve waarschuwingen en aanvullende gegevens voor de gekozen metro aan te vragen. U ook de ondersteunde metrogebieden en metro-ID's aanvragen. Metro gebied ID's zijn onderhevig aan verandering.

**metroID:** 522 **Naam:** Seattle-Tacoma-Bellevue

![Seattle-metro-gebied](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>ID's stoppen

Transitstops kunnen worden aangeduid met twee typen id's, de [GFTS-id (General Transit Feed Specification)](https://gtfs.org/) en de stop-ID van Azure Maps. De GFTS-id wordt de stopKey genoemd en de stop-id van Azure Maps wordt stopID genoemd. Wanneer u vaak verwijst naar transitstops, wordt u aangemoedigd om de stop-ID van Azure Maps te gebruiken. stopID is stabieler en waarschijnlijk hetzelfde blijven zolang de fysieke stop bestaat. De GTFS-stop-ID wordt vaker bijgewerkt. GTFS-stop-ID kan bijvoorbeeld worden bijgewerkt per verzoek van de GTFS-provider of wanneer een nieuwe GTFS-versie wordt uitgebracht. Hoewel de fysieke stop geen verandering had, kan de GTFS-stop-ID veranderen.

Om te beginnen u doorvoerstops in de buurt aanvragen met [de TransitAPI in de buurt](https://aka.ms/AzureMapsMobilityNearbyTransit)in de buurt.

## <a name="line-groups-and-lines"></a>Lijngroepen en -lijnen

Mobility Service maakt gebruik van een parallel gegevensmodel voor lijnen en lijngroepen. Dit model wordt gebruikt om beter om te gaan met wijzigingen die zijn overgenomen van [GTFS-routes](https://gtfs.org/) en de reisgegevens.


### <a name="line-groups"></a>Lijngroepen

Een lijngroep is een entiteit die alle lijnen die logischerwijs deel uitmaken van dezelfde groep groep samenbrengt. Meestal bevat een lijngroep twee regels, één van punt A naar B, en de andere van punt B naar A. Beide lijnen zouden eigendom zijn van hetzelfde ov-bureau en hetzelfde lijnnummer hebben. Er kunnen echter gevallen zijn waarin een lijngroep meer dan twee regels of slechts één regel binnen de lijn heeft.


### <a name="lines"></a>Lijnen

Zoals hierboven besproken, bestaat elke regelgroep uit een reeks regels. Elke regelgroep bestaat uit twee regels en elke regel beschrijft een richting.  Er zijn echter gevallen waarin meer regels een lijngroep samenstellen. Er is bijvoorbeeld een lijn die soms omgaat door een bepaalde buurt en soms niet. In beide gevallen werkt het onder hetzelfde regelnummer. Ook kan een lijngroep uit één regel worden samengesteld. Een cirkelvormige lijn met één richting is een linggroep met één lijn.

Om te beginnen u regelgroepen aanvragen met behulp van de [API Transitline opvragen.](https://aka.ms/AzureMapsMobilityTransitLine)


## <a name="next-steps"></a>Volgende stappen

Meer informatie over het aanvragen van transitgegevens met Mobility Service:

> [!div class="nextstepaction"]
> [Transitgegevens aanvragen](how-to-request-transit-data.md)

Meer informatie over het aanvragen van realtime gegevens met Mobility Service:

> [!div class="nextstepaction"]
> [Realtime gegevens opvragen](how-to-request-real-time-data.md)

Ontdek de API-documentatie van Azure Maps Mobility Service API

> [!div class="nextstepaction"]
> [Documentatie van Mobility Service API](https://aka.ms/AzureMapsMobilityService)
