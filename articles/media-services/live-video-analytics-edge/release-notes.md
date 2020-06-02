---
title: Live video Analytics op IoT Edge opmerkingen bij de release-Azure
description: Dit onderwerp bevat opmerkingen bij de release van live video Analytics over IoT Edge releases, verbeteringen, fout oplossingen en bekende problemen.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: fd607ee027b46cf386180338440e6530da911302
ms.sourcegitcommit: 223cea58a527270fe60f5e2235f4146aea27af32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84261035"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Opmerkingen bij de release van live video op IoT Edge

>Ontvang een melding over wanneer u deze pagina voor updates opnieuw moet bezoeken door deze URL te kopiëren en te plakken: `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` in uw RSS-feed-lezer.

In dit artikel vindt u informatie over:

* De nieuwste releases
* Bekende problemen
* Opgeloste fouten
* Afgeschafte functionaliteit

## <a name="june-1-2020"></a>1 juni 2020

Deze versie is de eerste open bare preview-versie van live video Analytics op IoT Edge. De release code is

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Ondersteunde functies
* Analyseer live video streams met behulp van AI-modules van uw keuze en geef optioneel video op op het apparaat met de rand of in de Cloud
* Gebruik op Linux AMD64-besturings systemen die worden [ondersteund](https://docs.microsoft.com/azure/iot-edge/support) door IOT Edge
* De module implementeren en configureren via de IoT Hub met behulp van Azure Portal of Visual Studio code
* [Graph-topologieën en Graph-exemplaren](media-graph-concept.md#media-graph-topologies-and-instances) extern of lokaal beheren via de volgende directe-methode aanroepen

    *   GraphTopologyGet
    *   GraphTopologySet
    *   GraphTopologyDelete
    *   GraphTopologyList
    *   GraphInstanceGet
    *   GraphInstanceSet
    *   GraphInstanceDelete
    *   GraphInstanceList


## <a name="next-steps"></a>Volgende stappen

[Overzicht](overview.md)
