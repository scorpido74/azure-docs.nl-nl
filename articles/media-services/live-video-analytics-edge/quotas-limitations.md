---
title: Live video-analyses op IoT Edge quota's en beperkingen-Azure
description: In dit artikel wordt de analyse van live video op IoT Edge quota's en beperkingen beschreven.
ms.topic: conceptual
ms.date: 05/22/2020
ms.openlocfilehash: df1978de4ee1bbbe15d0df3b02a70fb51491e9d2
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90529227"
---
# <a name="quotas-and-limitations"></a>Quota en beperkingen

In dit artikel worden de quota's en beperkingen van de live video Analytics op IoT Edge module opgesomd.

## <a name="maximum-period-of-disconnected-use"></a>De maximale periode van het niet-verbonden gebruik

De Edge-module kan tijdelijk geen netwerk verbinding meer verliezen. Als de module meer dan 36 uur niet meer is verbonden, worden alle grafiek exemplaren die werden uitgevoerd, gedeactiveerd en worden verdere directe methode aanroepen geblokkeerd.

Als u de module Edge wilt hervatten naar een operationele status, moet u de netwerk verbinding herstellen, waarna de module kan communiceren met het Azure media service-account.

## <a name="maximum-number-of-graph-instances"></a>Maximum aantal grafiek exemplaren

U kunt Maxi maal 1000 Graph-instanties per module hebben (gemaakt via GraphInstanceSet).

## <a name="maximum-number-of-graph-topologies"></a>Maximum aantal grafiek topologieën

U kunt Maxi maal 50 grafiek topologieën per module hebben (gemaakt via GraphTopologySet).

## <a name="limitations-on-graph-topologies-at-preview"></a>Beperkingen voor grafiek topologieën tijdens de preview-versie

Met de preview-versie zijn er beperkingen op verschillende knoop punten die kunnen worden verbonden met een media grafiek topologie.

* RTSP-bron
   * Er is slechts één RTSP-bron per grafiek topologie toegestaan.
* Processor voor frame frequentie filter
   * Moet direct worden downstream van de RTSP-bron-of bewegings detectie processor.
   * Kan niet worden gebruikt als downstream van een HTTP-of gRPC-extensie processor.
   * Kan niet upstream van een bewegings detectie processor zijn.
* HTTP-extensieprocessor
   * Er kan Maxi maal één processor per grafiek topologie zijn.
* gRPC-extensieprocessor
   * Er kan Maxi maal één processor per grafiek topologie zijn.
* Bewegings detectie processor
   * Moet direct worden downstream van de RTSP-bron.
   * Er kan Maxi maal één processor per grafiek topologie zijn.
   * Kan niet worden gebruikt als downstream van een HTTP-of gRPC-extensie processor.
* Signal Gate-processor
   * Moet direct worden downstream van de RTSP-bron.
* Asset Sink 
   * Moet direct worden downstream van de RTSP-bron-of signaal Gate-processor.
* Bestands Sink
   * Moet direct worden downstream van de Signal Gate-processor.
   * Kan niet direct worden downstream van een HTTP-of gRPC-extensie processor of een bewegings detectie processor
* IoT Hub Sink
   * Kan niet direct worden downstream van een IoT Hub bron.

Als zowel de bewegings detectie-als de filter frequentie processor knooppunten worden gebruikt, moeten ze zich in dezelfde keten van knoop punten bevinden die leiden tot het RTSP-bron knooppunt.

## <a name="limitations-on-media-service-operations-at-preview"></a>Beperkingen van media service-bewerkingen op de preview-versie

Op het moment van de preview-versie biedt de live video Analytics op IoT Edge geen ondersteuning voor het volgende:

* De mogelijkheid om het media service account te migreren van het ene naar het andere abonnement zonder onderbreking.
* De mogelijkheid om meer dan één opslag account met het media service account te gebruiken.
* De mogelijkheid om de gegevens van de Service-Principal in de gewenste eigenschappen van de module dynamisch te wijzigen zonder opnieuw op te starten.

U kunt alleen IP-camera's gebruiken die het RTSP-protocol ondersteunen. U vindt IP-camera's die RTSP ondersteunen op de pagina met [ONVIF-compatibele](https://www.onvif.org/conformant-products) producten. Zoek naar apparaten die voldoen aan de profielen G, S of T.

Verder moet u deze camera's configureren voor het gebruik van H. 264 video en AAC-audio. Andere codecs worden momenteel niet ondersteund. 

## <a name="next-steps"></a>Volgende stappen

[Overzicht](overview.md)
