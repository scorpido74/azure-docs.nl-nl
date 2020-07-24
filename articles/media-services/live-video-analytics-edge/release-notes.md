---
title: Live video Analytics op IoT Edge opmerkingen bij de release-Azure
description: Dit onderwerp bevat opmerkingen bij de release van live video Analytics over IoT Edge releases, verbeteringen, fout oplossingen en bekende problemen.
ms.topic: conceptual
ms.date: 04/27/2020
ms.openlocfilehash: 28260728532d9db52b8d36488c2e456bd11803ea
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87091776"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Opmerkingen bij de release van live video op IoT Edge

>Ontvang een melding over wanneer u deze pagina voor updates opnieuw moet bezoeken door deze URL te kopiëren en te plakken: `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` in uw RSS-feed-lezer.

In dit artikel vindt u informatie over:

* De nieuwste releases
* Bekende problemen
* Opgeloste fouten
* Afgeschafte functionaliteit

## <a name="july-13-2020"></a>13 juli 2020

Deze release code voor de vernieuwings frequentie van juli 2020 van de module is:

```
     mcr.microsoft.com/media/live-video-analytics:1.0.2
```

> [!NOTE]
> In de Quick starts en zelf studies maakt de implementatie manifesten gebruik van een tag van 1 (live-video-Analytics: 1). Als u dergelijke manifesten opnieuw implementeert, moet de module worden bijgewerkt op uw rand > apparaten.

### <a name="new-features"></a>Nieuwe functies
* U kunt nu grafiek-topologieën maken die een Asset Sink-knoop punt hebben en een knoop punt voor bestands sinks, downstream van een signaal Gate-processor knooppunt. Zie [Dit](https://github.com/Azure/live-video-analytics/tree/master/MediaGraph/topologies/evr-motion-assets-files) voor een voor beeld.

### <a name="bug-fixes"></a>Opgeloste fouten
* Verbeteringen in de validatie van de gewenste eigenschappen

## <a name="june-1-2020"></a>1 juni 2020

Deze versie is de eerste open bare preview-versie van live video Analytics op IoT Edge. De release code is

```
     mcr.microsoft.com/media/live-video-analytics:1.0.0
```

### <a name="supported-functionalities"></a>Ondersteunde functies
* Analyseer live video streams met behulp van AI-modules van uw keuze en geef optioneel video op op het apparaat met de rand of in de Cloud
* Gebruik op Linux AMD64-besturings systemen die worden [ondersteund](../../iot-edge/support.md) door IOT Edge
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
