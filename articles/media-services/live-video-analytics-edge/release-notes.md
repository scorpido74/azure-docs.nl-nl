---
title: Live video Analytics op IoT Edge opmerkingen bij de release-Azure
description: Dit onderwerp bevat opmerkingen bij de release van live video Analytics over IoT Edge releases, verbeteringen, fout oplossingen en bekende problemen.
ms.topic: conceptual
ms.date: 08/19/2020
ms.openlocfilehash: feab7755dea69a932fe40df59e0dd35f3f826553
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/10/2020
ms.locfileid: "89645814"
---
# <a name="live-video-analytics-on-iot-edge-release-notes"></a>Opmerkingen bij de release van live video op IoT Edge

>Ontvang een melding over wanneer u deze pagina voor updates opnieuw moet bezoeken door deze URL te kopiëren en te plakken: `https://docs.microsoft.com/api/search/rss?search=%22Live+Video+Analytics+on+IoT+Edge+release+notes%22&locale=en-us` in uw RSS-feed-lezer.

In dit artikel vindt u informatie over:

* De nieuwste releases
* Bekende problemen
* Opgeloste fouten
* Afgeschafte functionaliteit

## <a name="august-19-2020"></a>19 augustus 2020

Deze release code voor de vernieuwing van de module augustus 2020 is:

```
mcr.microsoft.com/media/live-video-analytics:1.0.3
```

> [!NOTE]
> In de Quick starts en zelf studies maakt de implementatie manifesten gebruik van een tag van 1 (live-video-Analytics: 1). Als u dergelijke manifesten opnieuw implementeert, moet de module worden bijgewerkt op uw rand > apparaten.

### <a name="new-features"></a>Nieuwe functies 

* U kunt nu prestaties van inhouds overdrachten voor hoge gegevens krijgen tussen live video Analytics op IoT Edge en uw aangepaste extensie met behulp van gRPC Framework. Bekijk [Dit](analyze-live-video-use-your-grpc-model-quickstart.md) om aan de slag te gaan.
* Grotere regionale implementatie van live video analyses en alleen de Cloud service is bijgewerkt.  
* Live video Analytics is nu beschikbaar in 25 extra regio's over de hele wereld. Hier volgt een [lijst](https://azure.microsoft.com/global-infrastructure/services/?products=media-services) met alle beschik bare regio's.  
* De [instelling](https://aka.ms/lva-edge/setup-resources-for-samples) voor snel starten is bijgewerkt en er wordt ondersteuning geboden voor nieuwe regio's.
    * Er is geen actie aanroepen voor iedereen die al een systeem heeft ingesteld

### <a name="bug-fixes"></a>Opgeloste fouten 

* Het gebruik van een afgeschafte Azure-extensie verwijderen in het script instellen

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
