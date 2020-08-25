---
title: Media Graph-concept-Azure
description: Met een media grafiek kunt u definiëren waar media moeten worden vastgelegd, hoe deze moeten worden verwerkt en waar de resultaten moeten worden bezorgd. Dit artikel bevat een gedetailleerde beschrijving van het concept van media Graph.
ms.topic: conceptual
ms.date: 05/01/2020
ms.openlocfilehash: 8c6775da6804b5079c89cae73d4621dd8067e90a
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798836"
---
# <a name="media-graph"></a>Mediagrafiek

## <a name="suggested-pre-reading"></a>Aanbevolen om te lezen

* [Overzicht van Live Video Analytics in IoT Edge](overview.md)
* [Terminologie van Live Video Analytics in IoT Edge](terminology.md)

## <a name="overview"></a>Overzicht

Met een media grafiek kunt u definiëren waar media moeten worden vastgelegd, hoe deze moeten worden verwerkt en waar de resultaten moeten worden bezorgd. U kunt dit doen door onderdelen of knoop punten op de gewenste manier te koppelen. In het onderstaande diagram ziet u een grafische weer gave van een media grafiek.  

![Een grafische weer gave van een media grafiek](./media/media-graph/overview.png)

Live video Analytics op IoT Edge ondersteunt verschillende soorten bronnen, processors en Sinks.

* **Bron knooppunten** maken het vastleggen van media in de media grafiek mogelijk. Media in deze context kunnen conceptueel gezien een audio stroom, een video stroom, een gegevens stroom of een stroom met audio, video en/of gegevens die samen in één stroom worden gecombineerd.
* **Processor knooppunten** maken het verwerken van media in de media grafiek mogelijk.
* **Sink-knoop punten** maken het mogelijk om de verwerkings resultaten te leveren aan services en apps buiten het Media diagram.

## <a name="media-graph-topologies-and-instances"></a>Media Graph-topologieën en-exemplaren 

Met live video Analytics in IoT Edge kunt u media grafieken beheren via twee concepten: "grafiek topologie" en "Graph-exemplaar". Met een grafiek topologie kunt u de blauw druk van een grafiek definiëren, met para meters als tijdelijke aanduidingen voor waarden. In de topologie wordt gedefinieerd welke knoop punten worden gebruikt in de media grafiek en hoe deze in de media grafiek zijn verbonden. Als u bijvoorbeeld de feed wilt vastleggen vanuit een camera, hebt u een grafiek nodig met een bron knooppunt dat video ontvangt en een Sink-knoop punt dat de video schrijft.

De waarden voor de para meters in de topologie worden opgegeven wanneer u grafiek exemplaren maakt die naar de topologie verwijzen. Hierdoor kunt u meerdere exemplaren maken die verwijzen naar dezelfde topologie, maar met verschillende waarden voor de para meters die zijn opgegeven in de topologie. In het bovenstaande voor beeld kunt u para meters gebruiken om het IP-adres van de camera en de naam van de opgenomen video weer te geven. U kunt een groot aantal exemplaren van grafieken maken met die topologie-één exemplaar voor elke camera in een gebouw, bijvoorbeeld elk met het specifieke IP-adres en de specifieke naam.

## <a name="media-graph-states"></a>Status van media grafiek  

Een media grafiek kan een van de volgende statussen hebben:

* Inactief: geeft de status aan waar een media grafiek is geconfigureerd, maar niet actief is.
* Activeren: de status wanneer een media grafiek wordt geïnstantieerd (dat wil zeggen, de status van de overgang tussen inactieve en actieve).
* Actief: de status wanneer een media grafiek actief is. 

    > [!NOTE]
    >  De media grafiek kan actief zijn zonder dat de gegevens worden getransporteerd (de invoer video bron gaat bijvoorbeeld offline).
* Deactiveren: dit is de status wanneer een media grafiek wordt overgezet van actief naar inactief.

In het onderstaande diagram ziet u de computer status van media Graph.

![Status computer media grafiek](./media/media-graph/media-graph-state-machine.png)

## <a name="sources-processors-and-sinks"></a>Bronnen, processors en sinks  

Live video Analytics op IoT Edge ondersteunt de volgende typen knoop punten in een media grafiek:

### <a name="sources"></a>Bronnen 

#### <a name="rtsp-source"></a>RTSP-bron 

Met een RTSP-bron knooppunt kunt u media opnemen vanaf een [RTSP] ( https://tools.ietf.org/html/rfc2326 server. Bewakings-en op IP gebaseerde camera's verzenden hun gegevens in een protocol met de naam RTSP (Realtime-Streaming-Protocol) dat verschilt van andere typen apparaten, zoals telefoons en video camera's. Dit protocol wordt gebruikt voor het instellen en beheren van de media sessies tussen een server (de camera) en een client. Het RTSP-bron knooppunt in een media grafiek fungeert als een-client en kan een sessie met een RTSP-server tot stand brengen. Veel apparaten, zoals de meeste [IP-camera's](https://en.wikipedia.org/wiki/IP_camera) , hebben een ingebouwde RTSP-server. [ONVIF](https://www.onvif.org/) vereist dat RTSP wordt ondersteund in de definitie van [profielen G, S & T-](https://www.onvif.org/wp-content/uploads/2019/12/ONVIF_Profile_Feature_overview_v2-3.pdf) compatibele apparaten. Het RTSP-bron knooppunt vereist dat u een RTSP-URL opgeeft, samen met referenties voor het inschakelen van een geverifieerde verbinding.

#### <a name="iot-hub-message-source"></a>Bron van IoT Hub bericht 

Net als andere [IOT Edge modules](../../iot-edge/iot-edge-glossary.md#iot-edge-module)kan live video Analytics op IOT Edge-module berichten ontvangen via de [IOT Edge hub](../../iot-edge/iot-edge-glossary.md#iot-edge-hub). Deze berichten kunnen worden verzonden vanuit andere modules of apps die worden uitgevoerd op het edge-apparaat of vanuit de Cloud. Dergelijke berichten worden verzonden naar een [benoemde invoer](../../iot-edge/module-composition.md#sink) in de module. Met een IoT Hub bron knooppunt van berichten kunnen dergelijke berichten een media grafiek bereiken. Deze berichten of signalen kunnen vervolgens intern worden gebruikt in de media grafiek, normaal gesp roken om signaal poorten te activeren (zie onderstaande [signaal poorten](#signal-gate-processor) ). 

U kunt bijvoorbeeld een IoT Edge module hebben waarmee een bericht wordt gegenereerd wanneer een deur wordt geopend. Het bericht van die module kan worden doorgestuurd naar IoT Edge hub, vanaf waar het kan worden doorgestuurd naar de IoT hub-bericht bron van een media grafiek. In de media grafiek kan de bericht bron van de IoT hub de gebeurtenis door geven aan een signaal Gate-processor, waardoor de video van een RTSP-bron in een bestand kan worden opgenomen. 

### <a name="processors"></a>Processors  

#### <a name="motion-detection-processor"></a>Bewegings detectie processor 

Met het knoop punt bewegings detectie processor kunt u bewegingen in live video detecteren. Er worden inkomende video frames onderzocht en er wordt bepaald of er bewegingen in de video zijn. Als er bewegingen worden gedetecteerd, worden de video frames door gegeven aan het downstream-onderdeel en wordt er een gebeurtenis verzonden. Het knoop punt voor de bewegings detectie processor (in combi natie met andere knoop punten) kan worden gebruikt voor het activeren van de opname van de binnenkomende video wanneer er beweging is gedetecteerd.

#### <a name="frame-rate-filter-processor"></a>Processor voor frame frequentie filter  

Met het knoop punt filter voor frame snelheid kunt u frames uit de inkomende video stroom met een opgegeven snelheid bemonsteren. Op deze manier kunt u het aantal frames dat wordt verzonden naar onderdelen met een lagere stroom (zoals een HTTP extension-processor knooppunt) beperken voor verdere verwerking.

#### <a name="http-extension-processor"></a>HTTP-extensie processor

Met het knoop punt voor de HTTP-extensie processor kunt u uw eigen IoT Edge-module verbinden met een media grafiek. Met dit knoop punt worden video frames gedecodeerd als invoer en worden deze frames doorgestuurd naar een HTTP REST-eind punt dat wordt weer gegeven door uw module. Dit knoop punt kan, indien nodig, worden geverifieerd met het REST-eind punt. Daarnaast heeft het knoop punt een ingebouwde afbeeldings indeling voor het schalen en coderen van video frames voordat ze worden doorgestuurd naar het REST-eind punt. De schaalset bevat opties voor de hoogte-breedte verhouding van de afbeelding die moet worden behouden, aangevuld of uitgerekt. De afbeeldings encoder ondersteunt de indelingen JPEG, PNG of BMP.

#### <a name="grpc-extension-processor"></a>gRPC-extensie processor

Het gRPC extension-processor knooppunt gebruikt gedecodeerde video frames als invoer en stuurt deze frames door naar een [gRPC](terminology.md#grpc) -eind punt dat door uw module wordt weer gegeven. Daarnaast heeft het knoop punt een ingebouwde afbeeldings indeling voor het schalen en coderen van video frames voordat ze worden doorgestuurd naar het gRPC-eind punt. De schaalset bevat opties voor de hoogte-breedte verhouding van de afbeelding die moet worden behouden, aangevuld of uitgerekt. De afbeeldings encoder ondersteunt de indelingen JPEG, PNG of bmp.

#### <a name="signal-gate-processor"></a>Signal Gate-processor  

Met het knoop punt Signal Gate processor kunt u op een voorwaardelijke manier media van het ene naar het andere knoop punt door sturen. Het fungeert ook als een buffer, zodat media en gebeurtenissen kunnen worden gesynchroniseerd. Een typische use-case is het invoegen van een signaal Gate-processor knooppunt tussen het RTSP-bron knooppunt en het knoop punt Asset sink, en het gebruik van de uitvoer van een knoop punt van een bewegings detector om de poort te activeren. Met een dergelijke media grafiek zou u video alleen opnemen wanneer er beweging wordt gedetecteerd.

### <a name="sinks"></a>Wastafel  

#### <a name="asset-sink"></a>Asset Sink  

Met een Asset Sink-knoop punt kunt u media gegevens (video en/of audio) schrijven naar een Azure Media Services-Asset. Er kan slechts één Asset Sink-knoop punt in een media grafiek zijn. Zie de sectie [Asset](terminology.md#asset) voor meer informatie over assets en hun rol in het vastleggen en afspelen van media. U kunt ook het artikel [doorlopende video-opname](continuous-video-recording-concept.md) bekijken voor meer informatie over de manier waarop de eigenschappen van dit knoop punt worden gebruikt.

#### <a name="file-sink"></a>Bestands Sink  

Met het knoop punt bestands sink kunt u media gegevens (video en/of audio) schrijven naar een locatie op het lokale bestands systeem van het IoT Edge apparaat. Er kan slechts één bestand Sink-knoop punt in een media grafiek zijn en het moet downstream van een signaal poort-processor knooppunt zijn. Hiermee wordt de duur van de uitvoer bestanden beperkt tot waarden die zijn opgegeven in de knooppunt eigenschappen van de signaal Gate-processor.

#### <a name="iot-hub-message-sink"></a>Bericht Sink IoT Hub  

Met een IoT Hub Message Sink-knoop punt kunt u gebeurtenissen publiceren naar IoT Edge hub. De IoT Edge hub kan de gegevens vervolgens door sturen naar andere modules of apps op het edge-apparaat of naar IoT Hub in de Cloud (per routes die zijn opgegeven in het implementatie manifest). Het knoop punt voor de IoT Hub Message Sink kan gebeurtenissen van upstream-processors, zoals een knoop punt voor bewegings detectie of van een externe service voor het afwijzen van een processor, accepteren via een knoop punt van de HTTP-extensie.

## <a name="rules-on-the-use-of-nodes"></a>Regels voor het gebruik van knoop punten

Zie de [beperkingen voor grafiek topologieën](quotas-limitations.md#limitations-on-graph-topologies-at-preview) voor aanvullende regels over hoe verschillende knoop punten kunnen worden gebruikt in een media grafiek.

## <a name="scenarios"></a>Scenario's

Met een combi natie van de hierboven gedefinieerde bronnen, processors en sinks kunt u media grafieken maken voor verschillende scenario's met betrekking tot de analyse van live video. Voorbeeld scenario's zijn:

* [Continue video-opname](continuous-video-recording-concept.md)
* [Video-opname op basis van gebeurtenissen](event-based-video-recording-concept.md)
* [Live Video Analytics zonder video-opname](analyze-live-video-concept.md)

## <a name="next-steps"></a>Volgende stappen

Zie [Quick Start: Live video Analytics uitvoeren met uw eigen model](use-your-model-quickstart.md)om te zien hoe u bewegings detectie kunt uitvoeren in een live video-feed.
