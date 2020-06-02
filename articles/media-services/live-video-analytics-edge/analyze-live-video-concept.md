---
title: Live video analyseren zonder opname-Azure
description: Een media grafiek kan worden gebruikt om analyse alleen uit te pakken vanuit een live video stroom, zonder dat u deze hoeft op te slaan op de rand of in de Cloud. In dit artikel wordt dit concept besproken.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 29e00b9c04a652771ca150e2a45e980d20f8bc1f
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84260937"
---
# <a name="analyzing-live-video-without-any-recording"></a>Live video analyseren zonder opname

## <a name="suggested-pre-reading"></a>Aanbevolen voor lezen 

* [Concept van media grafiek](media-graph-concept.md)
* [Video-opname op basis van gebeurtenissen](event-based-video-recording-concept.md)

## <a name="overview"></a>Overzicht  

U kunt een media grafiek gebruiken om live video te analyseren zonder delen van de video naar een bestand of een Asset te registreren. De media grafieken die hieronder worden weer gegeven, zijn vergelijkbaar met die in het artikel op [gebeurtenis-gebaseerde video-opnamen](event-based-video-recording-concept.md), maar zonder een knoop punt voor Asset sink of het knoop punt bestands sink.

### <a name="motion-detection"></a>Bewegings detectie

De media grafiek die hieronder wordt weer gegeven, bestaat uit een [RTSP-bron](media-graph-concept.md#rtsp-source) knooppunt, een [bewegings detectie processor](media-graph-concept.md#motion-detection-processor) knooppunt en een [IOT hub Message Sink](media-graph-concept.md#iot-hub-message-sink) -knoop punt. De JSON-weer gave van de grafiek topologie van een dergelijke media grafiek vindt u [hier](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/motion-detection/topology.json). Met deze grafiek kunt u bewegingen in de inkomende live video stream detecteren en de bewegings gebeurtenissen door sturen naar andere apps en services via het knoop punt IoT Hub Message sink. De externe apps of services kunnen een waarschuwing activeren of een melding naar het juiste personeel sturen.

![Live video analyse op basis van bewegings detectie](./media/analyze-live-video/motion-detection.png)

### <a name="analyzing-video-using-a-custom-vision-model"></a>Video analyseren met behulp van een aangepast gezichts model 

In de media grafiek die hieronder wordt weer gegeven, kunt u een live video stroom analyseren met behulp van een aangepast Vision model dat is verpakt in een afzonderlijke module. De JSON-weer gave van de grafiek topologie van een dergelijke media grafiek vindt u [hier](https://github.com/Azure/live-video-analytics/blob/master/MediaGraph/topologies/httpExtension/topology.json). [Hier](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis) vindt u enkele voor beelden van het inpakken van modellen in IOT Edge modules die worden uitgevoerd als een Afleidings service.

![Live video analyses op basis van een externe Afleidings module](./media/analyze-live-video/external-inferencing-module.png)

In deze media grafiek verlaagt het processor knooppunt voor de frame frequentie de frame frequentie van de binnenkomende live video stroom voordat deze wordt verzonden naar een [http extension-processor](media-graph-concept.md#http-extension-processor) knooppunt, waarmee afbeeldings frames (in JPEG-, BMP-of PNG-indeling) naar een externe service voor het afwijzen van de rest worden verzonden. De resultaten van de externe service voor afwijzen worden opgehaald door het knoop punt HTTP-extensie en door gegeven aan de IoT Edge hub via IoT Hub Message Sink-knoop punt. Dit type media grafiek kan worden gebruikt voor het ontwikkelen van oplossingen voor verschillende scenario's, zoals het leren van de implementatie van de time series van Voer tuigen op een snij punt, het gebruik van het patroon van het consumenten verkeer in een Retail Store, enzovoort.

Een uitbrei ding van dit voor beeld is het gebruik van een bewegings detector processor vóór het knoop punt filter voor frame frequentie. Hierdoor wordt de belasting van de service voor afnemen kleiner, omdat deze alleen wordt gebruikt wanneer er sprake is van Motion-activiteit in de video.

![Live video analyse op basis van door Motion gedetecteerde frames via een externe module voor degradatie](./media/analyze-live-video/motion-detected-frames.png)

## <a name="next-steps"></a>Volgende stappen

[Continue video-opname](continuous-video-recording-concept.md)
