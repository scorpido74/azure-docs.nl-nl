---
title: Statistieken en diagnostische logboeken van Media Services met Azure Monitor
titleSuffix: Azure Media Services
description: Meer informatie over het bewaken van Azure Media Services-statistieken en diagnostische logboeken via Azure Monitor.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: 75363212684bb09e84a4bc4881af26e6d2a8e7e3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585272"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs-via-azure-monitor"></a>Statistieken en diagnostische logboeken van Media Services bewaken via Azure Monitor

[Met Azure Monitor](../../azure-monitor/overview.md) u statistieken en diagnostische logboeken controleren waarmee u inzicht krijgen in hoe uw apps presteren. Alle gegevens die door Azure Monitor worden verzameld, passen in een van de twee fundamentele typen: statistieken en logboeken. U diagnostische logboeken van Media Services controleren en waarschuwingen en meldingen maken voor de verzamelde statistieken en logboeken. U de metrische gegevens visualiseren en analyseren met behulp van [Metrics explorer](../../azure-monitor/platform/metrics-getting-started.md). U logboeken naar [Azure Storage](https://azure.microsoft.com/services/storage/)verzenden, streamen naar [Azure Event Hubs,](https://azure.microsoft.com/services/event-hubs/)exporteren naar [Log Analytics](https://azure.microsoft.com/services/log-analytics/)of services van derden gebruiken.

Zie Azure Monitor [Metrics](../../azure-monitor/platform/data-platform.md) en [Azure Monitor Diagnostic logs](../../azure-monitor/platform/platform-logs-overview.md)voor een gedetailleerd overzicht.

In dit onderwerp worden ondersteunde [Media Services Metrics](#media-services-metrics) en Media Services Diagnostic logs [besproken.](#media-services-diagnostic-logs)

## <a name="media-services-metrics"></a>Statistieken van Media Services

Statistieken worden op regelmatige tijdstippen verzameld, ongeacht of de waarde verandert. Ze zijn handig voor waarschuwingen omdat ze vaak kunnen worden bemonsterd en een waarschuwing snel kan worden geactiveerd met relatief eenvoudige logica. Zie [Metrische waarschuwingen maken, weergeven en beheren met Azure Monitor](../../azure-monitor/platform/alerts-metric.md)voor informatie over het maken van metrische waarschuwingen.

Media Services ondersteunt monitoringstatistieken voor de volgende bronnen:

* Account
* Streaming-eindpunt

### <a name="account"></a>Account

U de volgende accountstatistieken controleren.

|Naam van meetwaarde|Weergavenaam|Beschrijving|
|---|---|---|
|AssetCount (AssetCount)|Aantal activa|Activa in uw account.|
|AssetQuota|Activaquotum|Assetquota in uw account.|
|AssetQuotaUsedPercentage|Gebruikt actiefquotum|Het percentage van het assetquotum dat al is gebruikt.|
|ContentKeyPolicyCount|Aantal inhoudssleutelbeleidsfactoren|Inhoudssleutelbeleid in uw account.|
|ContentKeyPolicyQuota|Inhoudssleutelbeleidsquotum|Quotum voor inhoudsbeleid in uw account.|
|ContentKeyPolicyQuotaUsedPercentage|Gebruikt gebruikt quotum voor inhoudssleutelbeleid|Het percentage van het al gebruikte contentkeybeleidquotum.|
|Aantal streamingbeleid|Aantal streamingbeleid|Streamingbeleid in je account.|
|StreamingPolicyQuota|Quotum streamingbeleid|Quotum voor streamingbeleid in uw account.|
|StreamingPolicyQuotaUsedPercentage|Gebruikt percentage streamingbeleid|Het percentage van het quotum streamingbeleid dat al is gebruikt.|

U moet ook [accountquota en -limieten controleren.](limits-quotas-constraints.md)

### <a name="streaming-endpoint"></a>Streaming-eindpunt

De volgende Statistieken [voor streaming eindpunten](https://docs.microsoft.com/rest/api/media/streamingendpoints) van Media Services worden ondersteund:

|Naam van meetwaarde|Weergavenaam|Beschrijving|
|---|---|---|
|Aanvragen|Aanvragen|Geeft het totale aantal HTTP-aanvragen op dat wordt weergegeven door het streamingeindpunt.|
|Uitgaand verkeer|Uitgaand verkeer|Het totale aantal uitgaande bytes. Bijvoorbeeld bytes gestreamd door het streaming eindpunt.|
|SuccesE2ELatentie|Succes end-to-end Latency|De duur van de tijd vanaf het moment waarop het streamingeindpunt het verzoek ontving tot wanneer de laatste byte van het antwoord werd verzonden.|

### <a name="why-would-i-want-to-use-metrics"></a>Waarom zou ik statistieken willen gebruiken?

Hier volgen voorbeelden van hoe het monitoren van Media Services-statistieken u kan helpen begrijpen hoe uw apps presteren. Enkele vragen die kunnen worden beantwoord met statistieken van Media Services zijn:

* Hoe houd ik mijn standaard streamingeindpunt in de gaten om te weten wanneer ik de limieten heb overschreden?
* Hoe weet ik of ik genoeg Premium Streaming Endpoint-schaaleenheden heb?
* Hoe kan ik een waarschuwing instellen om te weten wanneer ik mijn streaming eindpunten moet opschalen?
* Hoe stel ik een waarschuwing in om te weten wanneer de maximale uitgang die is geconfigureerd voor het account is bereikt?
* Hoe kan ik zien dat de uitsplitsing van aanvragen mislukt en wat de oorzaak is van de fout?
* Hoe kan ik zien hoeveel HLS- of DASH-aanvragen uit de verpakker worden gehaald?
* Hoe stel ik een waarschuwing in om te weten wanneer de drempelwaarde van aantal mislukte aanvragen is bereikt?

### <a name="example"></a>Voorbeeld

Zie [Statistieken van Media Services controleren](media-services-metrics-howto.md).

## <a name="media-services-diagnostic-logs"></a>Diagnostische logboeken van Media Services

Diagnostische logboeken bevatten uitgebreide en frequente gegevens over de werking van een Azure-bron. Zie [Logboekgegevens verzamelen en gebruiken van uw Azure-bronnen](../../azure-monitor/platform/platform-logs-overview.md)voor meer informatie.

Media Services ondersteunt de volgende diagnostische logboeken:

* Belangrijkste levering

### <a name="key-delivery"></a>Belangrijkste levering

|Name|Beschrijving|
|---|---|
|Aanvraag voor belangrijke bezorgservice|Logboeken met de aanvraaggegevens van de belangrijke leveringsservice. Zie [schema's voor](media-services-diagnostic-logs-schema.md)meer informatie.|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Waarom zou ik diagnostische logboeken willen gebruiken?

Enkele dingen die u onderzoeken met de belangrijkste diagnostische logboeken voor de levering zijn:

* Bekijk het aantal licenties dat wordt geleverd door DRM-type.
* Bekijk het aantal licenties dat door het beleid wordt geleverd.
* Zie fouten op DRM of beleidstype.
* Bekijk het aantal ongeautoriseerde licentieaanvragen van clients.

### <a name="example"></a>Voorbeeld

Zie [Diagnostische logboeken van Media Service controleren](media-services-diagnostic-logs-howto.md).

## <a name="next-steps"></a>Volgende stappen

* [Logboekgegevens verzamelen en gebruiken uit uw Azure-bronnen](../../azure-monitor/platform/platform-logs-overview.md)
* [Metrische waarschuwing maken, bekijken en beheren met Azure Monitor](../../azure-monitor/platform/alerts-metric.md)
* [Statistieken van Media Services controleren](media-services-metrics-howto.md)
* [Diagnostische logboeken van Media Service controleren](media-services-diagnostic-logs-howto.md)
