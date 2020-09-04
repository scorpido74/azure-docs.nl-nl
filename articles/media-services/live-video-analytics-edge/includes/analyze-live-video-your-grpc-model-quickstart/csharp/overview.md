---
ms.openlocfilehash: 97c21ca300ee070b2cebaa01a585c1618899b1eb
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88687169"
---

![Overzicht](../../../media/quickstarts/overview-grpc.png)

Dit diagram laat zien hoe de signalen in deze quickstart stromen. Een [Edge-module](https://github.com/Azure/live-video-analytics/tree/master/utilities/rtspsim-live555) simuleert een IP-camera die als host fungeert voor een RTSP-server (Real-Time Streaming Protocol). Een [RTSP-bron](../../../media-graph-concept.md#rtsp-source)knooppunt haalt de video-feed van deze server, en verstuurt videoframes naar het knooppunt van de [bewegingsdetectieprocessor](../../../media-graph-concept.md#motion-detection-processor). Met deze processor wordt beweging gedetecteerd. Zodra beweging wordt gedetecteerd, worden videoframes naar het knooppunt [gRPC-extensieprocessor](../../../media-graph-concept.md#grpc-extension-processor) gepusht.

Het knooppunt gRPC-extensie speelt de rol van een proxy. Het converteert videoframes naar het opgegeven afbeeldingstype. Vervolgens wordt de afbeelding via gRPC doorgestuurd naar een andere Edge-module die een AI-model achter een gRPC-eindpunt uitvoert via een [gedeeld geheugen](https://en.wikipedia.org/wiki/Shared_memory). In dit voorbeeld wordt die Edge-module gebouwd met behulp van het [YOLOv3](https://github.com/Azure/live-video-analytics/tree/master/utilities/video-analysis/yolov3-onnx)-model, dat vele soorten objecten kan detecteren. Het gRPC-extensieprocessor-knooppunt verzamelt de detectieresultaten en publiceert de gebeurtenissen naar het [IoT Hub Sink](https://docs.microsoft.com/azure/media-services/live-video-analytics-edge/media-graph-concept#iot-hub-message-sink)-knooppunt. Het knooppunt verzendt die gebeurtenissen vervolgens naar [IoT Edge-hub](https://docs.microsoft.com/azure/iot-edge/iot-edge-glossary#iot-edge-hub).

In deze quickstart gaat u het volgende doen:

1. De mediagrafiek maken en implementeren.
1. De resultaten interpreteren.
1. Resources opschonen.
