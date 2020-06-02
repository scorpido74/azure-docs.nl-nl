---
title: Media Graph-concept-Azure
description: Met media Graph kunt u bepalen waar media moeten worden vastgelegd, hoe deze moeten worden verwerkt en waar de resultaten moeten worden bezorgd. Dit artikel bevat een gedetailleerde beschrijving van het concept van media Graph.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: ee055c34fd37d2d1cc5e7d0bb5147c945dcbff94
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84260965"
---
# <a name="media-graph-concept"></a>Concept van media grafiek

## <a name="suggested-pre-reading"></a>Aanbevolen voor lezen

* [Overzicht van live video-analyses op IoT Edge](overview.md)
* [Live video-analyses op IoT Edge terminologie](terminology.md)

## <a name="overview"></a>Overzicht

Met media Graph kunt u bepalen waar media moeten worden vastgelegd, hoe deze moeten worden verwerkt en waar de resultaten moeten worden bezorgd. Een media grafiek bestaat uit bron-, processor-en Sink-knoop punten. In het onderstaande diagram ziet u een grafische weer gave van een media grafiek.   

![Een grafische weer gave van een media grafiek](./media/media-graph/overview.png)


* **Bron knooppunten** maken het vastleggen van media in de media grafiek mogelijk. Media in deze context kunnen conceptueel gezien een audio stroom, een video stroom, een gegevens stroom of een stroom met audio, video en/of gegevens die samen in één stroom worden gecombineerd.
* **Processor knooppunten** maken het verwerken van media in de media grafiek mogelijk.
* **Sink-knoop punten** maken het mogelijk om de verwerkings resultaten te leveren aan services en apps buiten het Media diagram.

## <a name="media-graph-topologies-and-instances"></a>Media Graph-topologieën en-exemplaren 

Met live video Analytics in IoT Edge kunt u media grafieken beheren via twee concepten: "grafiek topologie" en "Graph-exemplaar". Met een grafiek topologie kunt u een blauw druk van een grafiek definiëren, met para meters als tijdelijke aanduidingen voor waarden. In de topologie wordt gedefinieerd welke knoop punten worden gebruikt in de media grafiek en hoe deze in de media grafiek zijn verbonden. Waarden voor de para meters kunnen worden opgegeven bij het maken van grafiek exemplaren die verwijzen naar de topologie. Hierdoor kunt u meerdere exemplaren maken die verwijzen naar dezelfde topologie, maar met verschillende waarden voor de para meters die zijn opgegeven in de topologie. 

## <a name="media-graph-states"></a>Status van media grafiek  

Media grafiek kan een van de volgende statussen hebben:

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

Met een RTSP-bron kunnen media van een [RTSP](https://tools.ietf.org/html/rfc2326) -server worden vastgelegd. RTSP wordt gebruikt voor het instellen en beheren van de media sessies tussen een server en een client. Het RTSP-bron knooppunt in de media grafiek fungeert als een-client en kan een sessie met de opgegeven RTSP-server tot stand brengen. Veel apparaten, zoals de meeste [IP-camera's](https://en.wikipedia.org/wiki/IP_camera) , hebben een ingebouwde RTSP-server. [ONVIF](https://www.onvif.org/) vereist dat RTSP wordt ondersteund in de definitie van [profielen G, S & T-](https://www.onvif.org/wp-content/uploads/2019/12/ONVIF_Profile_Feature_overview_v2-3.pdf) compatibele apparaten. Voor het RTSP-bron knooppunt in Media Graph moet u een RTSP-URL opgeven, samen met referenties voor het inschakelen van een geverifieerde verbinding.

#### <a name="iot-hub-message-source"></a>Bron van IoT Hub bericht 

Net als andere [IOT Edge modules](../../iot-edge/iot-edge-glossary.md#iot-edge-module)kan live video Analytics op IOT Edge-module berichten ontvangen via de [IOT Edge hub](../../iot-edge/iot-edge-glossary.md#iot-edge-hub). Deze berichten kunnen worden verzonden vanuit andere modules of apps die worden uitgevoerd op het edge-apparaat of vanuit de Cloud. Dergelijke berichten kunnen worden geleverd (gerouteerd) naar een [benoemde invoer](../../iot-edge/module-composition.md#sink) in de module. Met een IoT Hub bericht bron kunnen dergelijke berichten worden doorgestuurd naar een exemplaar van media Graph. Deze berichten of signalen kunnen vervolgens intern worden gebruikt in de media grafiek, meestal om signaal poorten te activeren (Zie de [Signal Gate-processor](#signal-gate-processor) hieronder). 

U kunt bijvoorbeeld een IoT Edge module hebben waarmee een bericht wordt gegenereerd wanneer een deur wordt geopend. Het bericht van die module kan worden doorgestuurd naar IoT Edge hub, vanaf waar het kan worden doorgestuurd naar de IoT hub-bericht bron van een media grafiek. In de media grafiek kan de bericht bron van de IoT hub de gebeurtenis door geven aan een signaal Gate-processor, waardoor de video van een RTSP-bron in een bestand kan worden opgenomen. 

### <a name="processors"></a>Processors  

#### <a name="motion-detection-processor"></a>Bewegings detectie processor 

Met de bewegings detectie processor kunt u bewegingen in live video detecteren. Hiermee wordt de inkomende video onderzocht en wordt bepaald of er bewegingen in de video zijn. Als er bewegingen worden gedetecteerd, wordt de video door gegeven aan het downstream-knoop punt en wordt er een gebeurtenis verzonden. De bewegings detectie processor (in combi natie met andere media grafiek knooppunten) kan worden gebruikt voor het activeren van de opname van de binnenkomende video wanneer er beweging is gedetecteerd.

#### <a name="frame-rate-filter-processor"></a>Processor voor frame frequentie filter  

Met de filter processor voor frame snelheid kunt u frames uit de inkomende video stroom met een opgegeven frame snelheid bemonsteren. Zo kunt u het aantal frames dat wordt verzonden naar de knoop punten met een lagere stroom (zoals de HTTP-extensie processor) beperken voor verdere verwerking.

#### <a name="http-extension-processor"></a>HTTP-extensie processor 

Met de HTTP-extensie processor kunt u uw eigen AI met een media grafiek aansluiten. De HTTP-extensie processor gaat in als invoer gedecodeerde video frames en stuurt deze frames door naar een HTTP-eind punt. De processor kan, indien nodig, met het HTTP-eind punt verifiëren. Daarnaast heeft de processor een ingebouwde afbeeldings indelings functie waarmee video frames kunnen worden geschaald en gecoded voordat ze worden doorgestuurd. Schalen heeft opties voor de hoogte-breedte verhouding van de afbeelding die moet worden behouden, aangevuld of uitgerekt terwijl de code ring opties biedt voor de verschillende code ring van afbeeldingen, zoals JPEG, PNG of bmp.

#### <a name="signal-gate-processor"></a>Signal Gate-processor  

Met de signaal Gate-processor kan de media voorwaardelijk worden doorgestuurd van het ene knoop punt naar het andere. Het fungeert ook als een buffer, zodat media en gebeurtenissen kunnen worden gesynchroniseerd. Een voor beeld is het gebruik van een signaal Gate-processor tussen een RTSP-bron en een Asset-Sink en het gebruik van de uitvoer van een bewegings detector-processor om de poort te activeren. Met een dergelijke media grafiek kunt u alleen de opname van media activeren wanneer er bewegingen worden gedetecteerd in de binnenkomende video. 

### <a name="sinks"></a>Wastafel  

#### <a name="asset-sink"></a>Asset Sink  

Een Asset sink maakt het mogelijk dat een media grafiek media gegevens (video-en/of audio) naar een Azure Media Services Asset schrijft. Zie de sectie [Asset](terminology.md#asset) voor meer informatie over assets en hun rol in het vastleggen en afspelen van media.  

#### <a name="file-sink"></a>Bestands Sink  

Met de bestands Sink kan een media grafiek media gegevens (video en/of audio) schrijven naar een locatie op het lokale bestands systeem van het IoT Edge apparaat. De bestands Sink moet worden downstream van een Signal Gate-processor. Hiermee wordt de duur van de uitvoer bestanden beperkt tot waarden die zijn opgegeven in de eigenschappen van de signaal Gate-processor.

#### <a name="iot-hub-message-sink"></a>Bericht Sink IoT Hub  

Met een IoT Hub bericht sink kunt u gebeurtenissen publiceren naar IoT Edge hub. De Edge hub kan de gegevens vervolgens door sturen naar andere modules of apps op het edge-apparaat of naar IoT Hub in de Cloud (per routes die zijn opgegeven in het implementatie manifest). De IoT Hub-bericht Sink kan gebeurtenissen van upstream-processors, zoals een bewegings detectie processor of vanuit een externe service voor het afwijzen van een uitgevallen, accepteren via HTTP-extensie processor.

## <a name="scenarios"></a>Scenario's

Met een combi natie van de bron-, processor-en Sink-knoop punten, die hierboven zijn gedefinieerd, kunt u media grafieken maken voor verschillende scenario's. Hier volgen enkele voor beelden van scenario's

* [Continue video-opname](continuous-video-recording-concept.md)
* [Video-opname op basis van gebeurtenissen](event-based-video-recording-concept.md)
* [Live video analyses zonder video-opname](analyze-live-video-concept.md)

## <a name="next-steps"></a>Volgende stappen

Volg de [Snelstartgids: Live video Analytics uitvoeren met uw eigen model](use-your-model-quickstart.md) artikel voor meer informatie over het uitvoeren van bewegings detectie in een live video-feed.
