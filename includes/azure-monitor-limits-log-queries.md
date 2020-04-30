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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82072634"
---
### <a name="general-query-limits"></a>Algemene query limieten

| Limiet | Beschrijving |
|:---|:---|
| Querytaal | Azure Monitor gebruikt dezelfde [Kusto-query taal](/azure/kusto/query/) als Azure Data Explorer. Zie [Azure monitor taal verschillen in de logboek query](../articles/azure-monitor/log-query/data-explorer-difference.md) voor KQL-taal elementen die niet worden ondersteund in azure monitor. |
| Azure-regio's | Logboek query's kunnen overmatige overhead ondervinden wanneer gegevens Log Analytics werk ruimten in meerdere Azure-regio's. Zie [query limieten](../articles/azure-monitor/log-query/scope.md#query-limits) voor meer informatie. |
| Query's voor meerdere resources | Het maximum aantal Application Insights resources en Log Analytics werk ruimten in één query beperkt tot 100.<br>Query op meerdere resources wordt niet ondersteund in de ontwerp functie voor weer gaven.<br>Er wordt een query voor meerdere resources in logboek waarschuwingen ondersteund in de nieuwe scheduledQueryRules-API.<br>Zie [query's beperken tot meerdere resources](../articles/azure-monitor/log-query/cross-workspace-query.md#cross-resource-query-limits) voor meer informatie. |

### <a name="user-query-throttling"></a>Beperking van gebruikers query's
Azure Monitor heeft verschillende beperkings limieten om gebruikers te beschermen tegen het verzenden van een buitensporig aantal query's. Dit gedrag kan leiden tot overbelasting van de systeem back-bronnen en reactie tijd van de service. De volgende limieten zijn bedoeld om klanten te beschermen tegen onderbrekingen en een consistent service niveau te garanderen. De gebruikers beperking en limieten zijn zodanig ontworpen dat ze alleen van invloed zijn op het extreme gebruiks scenario en niet relevant zijn voor gebruikelijk gebruik.


| Measure | Limiet per gebruiker | Beschrijving |
|:---|:---|:---|
| Gelijktijdige query's | 5 | Als er al vijf query's worden uitgevoerd voor de gebruiker, worden nieuwe query's in een wachtrij voor gelijktijdig gebruik per gebruiker geplaatst. Wanneer een van de actieve query's eindigt, wordt de volgende query opgehaald uit de wachtrij en gestart. Dit omvat geen query's van waarschuwings regels.
| Tijd in gelijktijdigheids wachtrij | 2,5 minuten | Als een query langer dan 2,5 minuten in de wachtrij zit zonder te zijn gestart, wordt deze met een HTTP-fout bericht met code 429 beëindigd. |
| Totaal aantal query's in gelijktijdigheids wachtrij | 40 | Zodra het aantal query's in de wachtrij 40 heeft bereikt, worden eventuele extra query's geweigerd met een HTTP-fout code 429. Dit aantal is een aanvulling op de vijf query's die tegelijkertijd kunnen worden uitgevoerd. |
| Query frequentie | 200 query's per 30 seconden | Dit is het totale aantal query's dat door één gebruiker aan alle werk ruimten kan worden verzonden.  Deze limiet is van toepassing op programmatische query's of query's die worden geïnitieerd door visualisatie onderdelen als Azure-Dash boards en de overzichts pagina Log Analytics werk ruimte. |

- Optimaliseer uw query's zoals beschreven in [logboek Query's optimaliseren in azure monitor](../articles/azure-monitor/log-query/query-optimization.md).
- Dash boards en werkmappen kunnen meerdere query's bevatten in één weer gave die een burst van query's genereert elke keer dat ze worden geladen of vernieuwd. Houd er rekening mee dat u deze kunt opsplitsen in meerdere weer gaven die op aanvraag worden geladen. 
- In Power BI kunt u overwegen om alleen samengevoegde resultaten in plaats van onbewerkte logboeken te extra heren.