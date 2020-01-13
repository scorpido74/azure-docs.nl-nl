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
ms.openlocfilehash: 213910ee2439fa958b9f1d4926883eb8e066ba41
ms.sourcegitcommit: f9601bbccddfccddb6f577d6febf7b2b12988911
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/12/2020
ms.locfileid: "75910723"
---
# <a name="data-structures-in-azure-maps-mobility-service"></a>Gegevens structuren in de Azure Maps Mobility-service

In dit artikel wordt het concept van het metro gebied van [Azure Maps Mobility-service](https://aka.ms/AzureMapsMobilityService) en enkele van de gemeen schappelijke velden die door de services worden geretourneerd, geïntroduceerd wanneer deze worden opgevraagd voor open bare doorvoer onderbrekingen en-lijnen. We raden u aan dit artikel te door lopen voordat u begint met de Api's van de Mobility-service. Hieronder vindt u een beschrijving van deze algemene velden.

## <a name="metro-area"></a>Metro gebied

De gegevens van de Mobility-service worden opgesplitst in ondersteunde metro gebieden. Metro gebieden volgen niet de grenzen van steden, een metro gebied kan meerdere steden bevatten, bijvoorbeeld een met hoge bewoonde stad en de bijbehorende steden. een land/regio kan een metro gebied zijn. 

De `metroID` is de ID van een metro gebied dat kan worden gebruikt voor het aanroepen van de informatie-API voor het weer geven van een [metro gebied](https://aka.ms/AzureMapsMobilityMetroAreaInfo) om ondersteunde doorvoer typen en aanvullende informatie te vragen voor het metro gebied, zoals doorvoer instanties en actieve waarschuwingen. U kunt met behulp van de Azure Maps een metro-API ophalen om de ondersteunde metro gebieden en metroIDs aan te vragen. De Id's van een metro gebied kunnen worden gewijzigd.

**metroID:** 522- **naam:** Seattle-Tacoma-Bellevue

![Seattle-metro-area](./media/mobility-service-data-structure/seattle-metro.png)

## <a name="stop-ids"></a>Stop-Id's

Door doorvoer onderbrekingen kunnen worden verwezen door twee typen Id's, de GFTS-id ( [General Transit feed Specification)](https://gtfs.org/) (aangeduid als stopKey) en de Azure Maps stop-id (aangeduid als stopId). Als er wordt gebruikgemaakt van een onderbreking in de loop van de tijd, wordt het aanbevolen om de Azure Maps stop-ID te gebruiken, omdat deze ID stabieler is en waarschijnlijk niet wordt gewijzigd zolang de fysieke stop bestaat. De GTFS stop-ID wordt vaker bijgewerkt, bijvoorbeeld als de GTFS-provider deze moet wijzigen of als de nieuwe GTFS-versie wordt vrijgegeven, hoewel de fysieke stop geen wijziging heeft.

Als u wilt starten, kunt u stoppen in de buurt van de transit door gebruik te maken van de [API voor binnenkomend verkeer ophalen](https://aka.ms/AzureMapsMobilityNearbyTransit)

## <a name="line-groups-and-lines"></a>Lijn groepen en-regels

Mobility service maakt gebruik van een parallel gegevens model voor lijnen en lijn groepen voor een betere afronding van de wijzigingen die zijn overgenomen van [GTFS](https://gtfs.org/) routes en trips data model.


### <a name="line-groups"></a>Regel groepen

Een regel groep is een entiteit waarmee alle regels die logisch deel uitmaken van dezelfde groep worden gegroepeerd. Normaal gesp roken bevat een lijn groep twee regels, één van punt A tot en met B, en de andere retour van punt B naar A, beide tot hetzelfde open bare transport Agentschap en met hetzelfde regel nummer. Het kan echter voor komen dat een regel groep meer dan twee regels of één regel bevat.


### <a name="lines"></a>Lijnen

Zoals hierboven is beschreven, bestaat elke regel groep uit een set regels. Vaak wordt in elke regel een richting beschreven en elke regel groep bestaat uit twee regels. Er zijn echter gevallen waarin meer regels bestaan uit een regel groep, bijvoorbeeld een regel die soms door een bepaalde groep wordt omgeleid en soms niet, en die in beide gevallen onder hetzelfde regel nummer wordt gebruikt en er andere gevallen zijn waarin een regel g groeperen bestaat uit één regel, bijvoorbeeld een ronde lijn met één richting.

Als u wilt beginnen, kunt u regel groepen aanvragen met behulp van de [regel-API voor transit ophalen](https://aka.ms/AzureMapsMobilityTransitLine) en later inzoomen op regels.


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
