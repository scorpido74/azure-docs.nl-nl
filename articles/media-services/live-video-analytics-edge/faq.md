---
title: Veelgestelde vragen over live video Analytics op IoT Edge-Azure
description: In dit onderwerp vindt u antwoorden op live video Analytics op IoT Edge Veelgestelde vragen.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 705c74b1ba6d3a28911185b82a558099e87dc18d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87011512"
---
# <a name="frequently-asked-questions-faqs"></a>Veelgestelde vragen (FAQ)

In dit onderwerp vindt u antwoorden op live video Analytics op IoT Edge Veelgestelde vragen.

## <a name="general"></a>Algemeen

Wat zijn de systeem variabelen die kunnen worden gebruikt in de definitie van de grafiek topologie?

|Variabele   |Beschrijving|
|---|---|
|[System. DateTime](/dotnet/framework/data/adonet/sql/linq/system-datetime-methods)|Vertegenwoordigt een onmiddellijke tijd, meestal uitgedrukt als een datum en tijd van de dag.|
|System. GraphTopologyName   |Vertegenwoordigt een media grafiek topologie, bevat de blauw druk van een grafiek.|
|System. GraphInstanceName|  Vertegenwoordigt een exemplaar van een media grafiek, bevat parameter waarden en verwijst naar de topologie.|

## <a name="configuration-and-deployment"></a>Configuratie en implementatie

Kan ik de module media Edge implementeren op een Windows 10-apparaat?
    * Ja. Zie het artikel over [Linux-containers in Windows 10](/virtualization/windowscontainers/deploy-containers/linux-containers).

## <a name="capture-from-ip-camera-and-rtsp-settings"></a>Vastleggen van IP-camera en RTSP-instellingen

* Moet ik een speciale SDK op mijn apparaat gebruiken om een videostream te verzenden?
    * Nee. Live video Analytics op IoT Edge ondersteunt het vastleggen van media met het RTSP video streaming protocol (dat wordt ondersteund op de meeste IP-camera's).
* Kan ik media pushen naar live video Analytics op IoT Edge met behulp van RTMP of Smooth (zoals een Media Services live-evenement)?
    * Nee. LVA biedt alleen ondersteuning voor RTSP voor het vastleggen van video van IP-camera's.
    * Elke camera die RTSP-streaming via TCP/HTTP ondersteunt, zou moeten werken. 
* Kan ik de RTSP-bron-URL in een grafiekexemplaar opnieuw instellen of bijwerken?
    * Ja, wanneer het exemplaar van de grafiek de status inactief heeft.  
* Is er een RTSP-Simulator beschikbaar voor gebruik tijdens het testen en ontwikkelen?
    * Ja. Er is een [RTSP Simulator](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) Edge-module beschikbaar voor gebruik in de Quick Start en zelf studies ter ondersteuning van het leer proces. Deze module wordt geleverd als best-effort en is mogelijk niet altijd beschikbaar. Het wordt ten zeerste aangeraden deze niet langer dan een paar uur te gebruiken. Voordat u plannen maakt voor een productie-implementatie, moet u investeren in tests met uw werkelijke RTSP-bron.
* Bieden jullie ondersteuning voor ONVIF-detectie van IP-camera's aan de rand?
    * Nee, er is geen ondersteuning voor ONVIF-detectie van apparaten aan de rand.

## <a name="streaming-and-playback"></a>Streaming en afspelen

* Kunnen assets die zijn geregistreerd voor AMS van de Edge, worden afgespeeld met Media Services streaming-technologieën zoals HLS of DASH Board?
    * Ja. De vastgelegde activa kunnen worden gestreamd zoals andere assets in Azure Media Services. Als u de inhoud wilt streamen, moet u een streaming-eind punt hebben gemaakt en de status wordt uitgevoerd. Als u het standaard proces voor het maken van streaming-locators gebruikt, krijgt u toegang tot een HLS-of een DASH-manifest voor streaming naar elk mogelijk Player-Framework. Zie [dynamische verpakking](../latest/dynamic-packaging-overview.md)voor meer informatie over het maken van publicatie-HLS of-streepjes manifesten.
* Kan ik de standaard functies voor beveiliging van inhoud en DRM van Media Services op een gearchiveerde Asset gebruiken?
    * Ja. Alle standaard functies voor dynamische versleuteling van inhoud en DRM zijn beschikbaar voor gebruik in de assets die zijn opgenomen in een media grafiek.
* Welke spelers kan ik gebruiken om inhoud van de geregistreerde assets weer te geven?
   * Alle standaard spelers die ondersteuning bieden voor compatibele Apple HTTP Live Streaming (HLS) versie 3 of versie 4 worden ondersteund. Daarnaast wordt elke speler die compatibel is met MPEG-DASH Play, ook ondersteund.
    De aanbevolen spelers voor testen zijn onder andere:

    * [Azure Media Player](../latest/use-azure-media-player.md)
    * [HLS.js](https://hls-js.netlify.app/demo/)
    * [Video.js](https://videojs.com/)
    * [Dash.js](https://github.com/Dash-Industry-Forum/dash.js/wiki)
    * [Schuda-speler](https://github.com/google/shaka-player)
    * [ExoPlayer](https://github.com/google/ExoPlayer)
    * [Apple native HTTP Live Streaming](https://developer.apple.com/streaming/)
    * Edge, Chrome of Safari gebouwd in HTML5-Video speler
    * Commerciële spelers die ondersteuning bieden voor HLS of onderbroken afspelen
* Wat zijn de limieten voor het streamen van een media Graph-Asset?
    * Het streamen van een live-of opgenomen activum vanuit een media grafiek maakt gebruik van hetzelfde hoogwaardige infra structuur en streaming-eind punt dat Media Services ondersteunt voor on-demand en live streamen voor media & entertainment, OTT en het verzenden van klanten. Dit betekent dat u snel en eenvoudig de Azure CDN, Verizon of Akamai kunt inschakelen voor het leveren van uw inhoud aan een publiek als een klein aantal kijkers, of tot miljoenen, afhankelijk van uw scenario.

    Inhoud kan worden geleverd met Apple HTTP Live Streaming (HLS) of MPEG-DASH.

## <a name="monitoring-and-metrics"></a>Bewaking en metrische gegevens

* Kan ik de media grafiek op de rand bewaken met behulp van Event Grid?
    * Nee. Momenteel wordt Event Grid niet ondersteund.
* Kan ik Azure Monitor gebruiken om de status, metrische gegevens en prestaties van mijn media grafieken in de Cloud of aan de rand weer te geven?
    * Nee.
* Zijn er hulpprogram ma's waarmee u de Media Services IoT Edge module gemakkelijker kunt bewaken?
    * Visual Studio code ondersteunt de extensie ' Azure IoT tools ' waarmee u de LVAEdge-module-eind punten eenvoudig kunt bewaken. U kunt dit hulp programma gebruiken om snel te beginnen met het bewaken van het IoT Hub ingebouwde eind punt voor "events" en de Afleidings berichten te zien die vanuit het edge-apparaat naar de cloud worden gerouteerd. 

    Daarnaast kunt u deze extensie gebruiken om de module te bewerken voor de module LVAEdge om de instellingen voor de media grafiek te wijzigen.

Zie het artikel over [bewaking en logboek registratie](monitoring-logging.md) voor meer informatie.

## <a name="billing-and-availability"></a>Facturering en beschik baarheid

* Hoe wordt LiveVideo Analytics on IoT Edge gefactureerd?
    * Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/media-services/) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

[Quickstart: Over Live Video Analytics in IoT Edge](get-started-detect-motion-emit-events-quickstart.md)
