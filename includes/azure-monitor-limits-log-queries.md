---
title: bestand opnemen
description: bestand opnemen
services: azure-monitor
author: rboucher
tags: azure-service-management
ms.topic: include
ms.date: 07/22/2019
ms.author: bwren
ms.custom: include file
ms.openlocfilehash: 627b020ce618a2a1f2646a95e143947876bd6a15
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "82072634"
---
### <a name="general-query-limits"></a>Algemene querylimieten

| Limiet | Beschrijving |
|:---|:---|
| Querytaal | Azure Monitor gebruikt dezelfde [Kusto-querytaal](/azure/kusto/query/) als Azure Data Explorer. Zie [taalverschillen in azure monitor-logboekquery's](../articles/azure-monitor/log-query/data-explorer-difference.md) voor KQL-taalelementen die niet worden ondersteund in Azure Monitor. |
| Azure-regio's | Logquery's kunnen overmatige overhead ervaren wanneer gegevens log analytics-werkruimten in meerdere Azure-regio's omvatten. Zie [Querylimieten](../articles/azure-monitor/log-query/scope.md#query-limits) voor meer informatie. |
| Bronquery's voor kruisbronnen | Maximaal aantal Resources voor Application Insights en Log Analytics-werkruimten in één query die beperkt is tot 100.<br>Query met kruisresources wordt niet ondersteund in View Designer.<br>Logboekwaarschuwingen voor cross-resourcequery's worden ondersteund in de nieuwe api voor geplandeQueryregels.<br>Zie Querylimieten voor [kruisresources](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) voor meer informatie. |

### <a name="user-query-throttling"></a>Beperking van gebruikersquery's
Azure Monitor heeft verschillende beperkingslimieten om te beschermen tegen gebruikers die een buitensporig aantal query's verzenden. Dergelijk gedrag kan mogelijk overbelasten het systeem backend middelen en in gevaar brengen service responsiviteit. De volgende limieten zijn ontworpen om klanten te beschermen tegen onderbrekingen en om een consistent serviceniveau te garanderen. De beperking en limieten van de gebruiker zijn ontworpen om alleen een extreem gebruiksscenario te beïnvloeden en mogen niet relevant zijn voor normaal gebruik.


| Measure | Limiet per gebruiker | Beschrijving |
|:---|:---|:---|
| Gelijktijdige query's | 5 | Als er al 5 query's voor de gebruiker worden uitgevoerd, worden nieuwe query's in een gelijktijdigheidswachtrij per gebruiker geplaatst. Wanneer een van de lopende query's afloopt, wordt de volgende query uit de wachtrij gehaald en gestart. Dit geldt niet voor query's uit waarschuwingsregels.
| Tijd in gelijktijdigheidswachtrij | 2,5 minuten | Als een query langer dan 2,5 minuten in de wachtrij staat zonder te worden gestart, wordt deze beëindigd met een HTTP-foutantwoord met code 429. |
| Totaal query's in gelijktijdige wachtrij | 40 | Zodra het aantal query's in de wachtrij 40 bereikt, worden eventuele aanvullende query's geweigerd met een HTTP-foutcode 429. Dit aantal is een aanvulling op de 5 query's die tegelijkertijd kunnen worden uitgevoerd. |
| Querysnelheid | 200 query's per 30 seconden | Dit is de algemene snelheid waarmee query's door één gebruiker kunnen worden ingediend bij alle werkruimten.  Deze limiet is van toepassing op programmatische query's of query's die zijn gestart met visualisatieonderdelen zoals Azure-dashboards en de overzichtspagina van de log Analytics-werkruimte. |

- Optimaliseer uw query's zoals beschreven in [Logboekquery's optimaliseren in Azure Monitor](../articles/azure-monitor/log-query/query-optimization.md).
- Dashboards en werkmappen kunnen meerdere query's in één weergave bevatten die een reeks query's genereren elke keer dat ze worden geladen of vernieuwd. Overweeg ze op te splitsen in meerdere weergaven die op aanvraag worden geladen. 
- Overweeg in Power BI alleen geaggregeerde resultaten te extraheren in plaats van ruwe logboeken.