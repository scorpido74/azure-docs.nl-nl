---
title: Overzicht van logboekquery's in Azure Monitor | Microsoft Documenten
description: Beantwoordt veelvoorkomende vragen met betrekking tot logboekquery's en laat u aan de slag met het gebruik ervan.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.openlocfilehash: 54a6f875bc33d24d412d2424c634d1019b4af399
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670114"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Overzicht van logboekquery's in Azure Monitor
Met logboekquery's u de waarde van de gegevens die worden verzameld in [Azure Monitor-logboeken](../platform/data-platform-logs.md)volledig benutten. Met een krachtige querytaal u gegevens uit meerdere tabellen samenvoegen, grote sets gegevens samenvoegen en complexe bewerkingen uitvoeren met minimale code. Vrijwel elke vraag kan worden beantwoord en analyse uitgevoerd zolang de ondersteunende gegevens zijn verzameld, en u begrijpt hoe u de juiste query construeren.

Sommige functies in Azure Monitor, zoals [inzichten](../insights/insights-overview.md) en [oplossingen,](../insights/solutions-inventory.md) verwerken logboekgegevens zonder u bloot te stellen aan de onderliggende query's. Als u andere functies van Azure Monitor volledig wilt gebruiken, moet u begrijpen hoe query's worden opgebouwd en hoe u deze gebruiken om gegevens in Azure Monitor-logboeken interactief te analyseren.

Gebruik dit artikel als uitgangspunt voor het leren over logboekquery's in Azure Monitor. Het beantwoordt veelgestelde vragen en biedt links naar andere documentatie die verdere details en lessen biedt.

## <a name="how-can-i-learn-how-to-write-queries"></a>Hoe kan ik leren hoe ik query's moet schrijven?
Als je meteen in dingen wilt springen, kun je beginnen met de volgende tutorials:

- [Aan de slag met Log Analytics in Azure Monitor.](get-started-portal.md)
- [Aan de slag met logboekquery's in Azure Monitor](get-started-queries.md).

Zodra je de basis hebt, loop je meerdere lessen met je eigen gegevens of gegevens uit onze demoomgeving, te beginnen met: 

- [Werken met tekenreeksen in azure monitor-logboekquery's](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>Welke taal gebruiken logquery's?
Azure Monitor Logs is gebaseerd op [Azure Data Explorer](/azure/data-explorer)en logboekquery's worden geschreven met dezelfde Kusto-querytaal (KQL). Dit is een rijke taal ontworpen om gemakkelijk te lezen en auteur, en je moet in staat zijn om te beginnen met het gebruik ervan met minimale begeleiding.

Zie [KQL-documentatie](/azure/kusto/query) van Azure Data Explorer voor volledige documentatie over KQL en referentie over verschillende beschikbare functies.<br>
Zie [Aan de slag met logboekquery's in Azure Monitor](get-started-queries.md) voor een snelle doorloop van de taal met behulp van gegevens uit Azure Monitor-logboeken.
Zie [taalverschillen in queryquery's](data-explorer-difference.md) van Azure Monitor in Azure Monitor voor kleine verschillen in de versie van KQL die door Azure Monitor wordt gebruikt.

## <a name="what-data-is-available-to-log-queries"></a>Welke gegevens zijn beschikbaar om query's te registreren?
Alle gegevens die worden verzameld in Azure Monitor Logs zijn beschikbaar voor het ophalen en analyseren van logboekquery's. Verschillende gegevensbronnen schrijven hun gegevens naar verschillende tabellen, maar u meerdere tabellen in één query opnemen om gegevens in meerdere bronnen te analyseren. Wanneer u een query maakt, bepaalt u eerst welke tabellen de gegevens bevatten die u zoekt, zodat u op zijn minst een basiskennis moet hebben van hoe gegevens in Azure Monitor-logboeken zijn gestructureerd.

Zie [Bronnen van Azure Monitor-logboeken](../platform/data-platform-logs.md#sources-of-azure-monitor-logs)voor een lijst met verschillende gegevensbronnen die Azure Monitor-logboeken vullen.<br>
Zie [Structuur van Azure Monitor-logboeken](logs-structure.md) voor een uitleg over hoe de gegevens zijn gestructureerd.

## <a name="what-does-a-log-query-look-like"></a>Hoe ziet een logboekquery eruit?
Een query kan zo eenvoudig zijn als één tabelnaam voor het ophalen van alle records uit die tabel:

```Kusto
Syslog
```

Of het kan filteren op bepaalde records, ze samenvatten en de resultaten in een grafiek visualiseren:

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

Voor complexere analyses u gegevens uit meerdere tabellen ophalen met behulp van een join om de resultaten samen te analyseren.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
Zelfs als u niet bekend bent met KQL, moet u in staat zijn om op zijn minst achterhalen van de fundamentele logica wordt gebruikt door deze query's. Ze beginnen met de naam van een tabel en voegen vervolgens meerdere opdrachten toe om die gegevens te filteren en te verwerken. Een query kan elk aantal opdrachten gebruiken en u complexere query's schrijven als u vertrouwd raakt met de verschillende Beschikbare KQL-opdrachten.

Zie [Aan de slag met logboekquery's in Azure Monitor](get-started-queries.md) voor een zelfstudie over logboekquery's waarin de taal en algemene functies worden geïntroduceerd, .<br>


## <a name="what-is-log-analytics"></a>Wat is Log Analytics?
Log Analytics is het primaire hulpmiddel in de Azure-portal voor het schrijven van logquery's en het interactief analyseren van hun resultaten. Zelfs als een logboekquery elders in Azure Monitor wordt gebruikt, moet u de query doorgaans eerst schrijven en testen met behulp van Log Analytics.

U Log Analytics op verschillende plaatsen in de Azure-portal starten. Het bereik van de gegevens die beschikbaar zijn voor Log Analytics wordt bepaald door hoe u deze start. Zie [Querybereik](scope.md) voor meer informatie.

- Selecteer **Logboeken** in het menu **azure monitor** of het menu **Logboekanalysewerkruimten.**
- Selecteer **Analytics** op de **pagina Overzicht** van een toepassing Application Insights.
- Selecteer **Logboeken** in het menu van een Azure-bron.

![Log Analytics](media/log-query-overview/log-analytics.png)

Zie [Aan de slag met Log Analytics in Azure Monitor](get-started-portal.md) voor een zelfstudie-walkthrough van Log Analytics die een aantal functies introduceert.

## <a name="where-else-are-log-queries-used"></a>Waar worden logboekquery's nog meer gebruikt?
Naast het interactief werken met logboekquery's en hun resultaten in Log Analytics, zijn gebieden in Azure Monitor waar u query's gebruikt het volgende:

- **Waarschuwingsregels.** [Waarschuwingsregels](../platform/alerts-overview.md) identificeren proactief problemen van gegevens in uw werkruimte.  Elke waarschuwingsregel is gebaseerd op een logboekzoekopdracht die automatisch op regelmatige tijdstippen wordt uitgevoerd.  De resultaten worden geïnspecteerd om te bepalen of er een waarschuwing moet worden gemaakt.
- **Dashboards.** U de resultaten van elke query vastmaken aan een [Azure-dashboard](../learn/tutorial-logs-dashboards.md) waarmee u logboek- en metrische gegevens samen visualiseren en optioneel delen met andere Azure-gebruikers.
- **Weergaven.**  U visualisaties van gegevens maken die met [View Designer](../platform/view-designer.md)in gebruikersdashboards moeten worden opgenomen.  Logboekquery's bieden de gegevens die worden gebruikt door [tegels](../platform/view-designer-tiles.md) en [visualisatieonderdelen](../platform/view-designer-parts.md) in elke weergave.  
- **Exporteren.**  Wanneer u logboekgegevens uit Azure Monitor importeert in Excel of [Power BI,](../platform/powerbi.md)maakt u een logboekquery om de gegevens te definiëren die u wilt exporteren.
- **Powershell.** U een PowerShell-script uitvoeren vanaf een opdrachtregel of een Azure Automation-runbook waarmee [u Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) gebruikt om logboekgegevens op te halen uit Azure Monitor.  Deze cmdlet vereist een query om de gegevens te bepalen die moeten worden opgehaald.
- **Azure Monitor Logs API.**  Met de [API azure monitorlogboeken](https://dev.loganalytics.io) kan elke REST API-client logboekgegevens ophalen uit de werkruimte.  De API-aanvraag bevat een query die wordt uitgevoerd tegen Azure Monitor om de gegevens te bepalen die moeten worden opgehaald.


## <a name="next-steps"></a>Volgende stappen
- Loop door een [zelfstudie over het gebruik van Log Analytics in de Azure-portal.](get-started-portal.md)
- Loop door een [tutorial over het schrijven van query's.](get-started-queries.md)
