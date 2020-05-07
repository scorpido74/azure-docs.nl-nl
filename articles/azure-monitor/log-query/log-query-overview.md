---
title: Overzicht van logboek query's in Azure Monitor | Microsoft Docs
description: Beantwoordt veelgestelde vragen met betrekking tot logboek query's en haalt u aan de slag.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 06/19/2019
ms.openlocfilehash: 6cd2a28ddbe970385ff44deec2158c257937982e
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82591649"
---
# <a name="overview-of-log-queries-in-azure-monitor"></a>Overzicht van logboek query's in Azure Monitor
Met logboek query's kunt u de waarde van de gegevens die in [Azure monitor logboeken](../platform/data-platform-logs.md)worden verzameld, volledig benutten. Met een krachtige query taal kunt u gegevens uit meerdere tabellen samen voegen, grote gegevens sets verzamelen en complexe bewerkingen met minimale code uitvoeren. Vrijwel elke vraag kan worden beantwoord en de analyse wordt uitgevoerd zolang de ondersteunende gegevens zijn verzameld en u begrijpt hoe u de juiste query kunt bouwen.

Sommige functies in Azure Monitor, zoals [inzichten](../insights/insights-overview.md) en [oplossingen](../insights/solutions-inventory.md) , verwerken logboek gegevens zonder dat u de onderliggende query's hoeft aan te bieden. Als u andere functies van Azure Monitor volledig wilt benutten, moet u weten hoe query's worden samengesteld en hoe u deze kunt gebruiken om gegevens in Azure Monitor logboeken interactief te analyseren.

Gebruik dit artikel als uitgangs punt voor het leren over logboek query's in Azure Monitor. De oplossing beantwoordt veelgestelde vragen en bevat koppelingen naar andere documentatie met meer informatie en lessen.

## <a name="how-can-i-learn-how-to-write-queries"></a>Hoe kan ik zien hoe ik query's kan schrijven?
Als u direct naar dingen wilt gaan, kunt u beginnen met de volgende zelf studies:

- Aan de [slag met log Analytics in azure monitor](get-started-portal.md).
- Aan de [slag met logboek query's in azure monitor](get-started-queries.md).

Wanneer u de basis principes hebt, kunt u meerdere lessen door lopen met uw eigen gegevens of gegevens uit onze demo omgeving, te beginnen met: 

- [Werken met teken reeksen in Azure Monitor-logboek query's](string-operations.md)
 
## <a name="what-language-do-log-queries-use"></a>Welke taal gebruiken logboek query's?
Azure Monitor Logboeken is gebaseerd op [Azure Data Explorer](/azure/data-explorer)en logboek query's worden geschreven met dezelfde Kusto query language (KQL). Dit is een uitgebreide taal, ontworpen om gemakkelijk te lezen en te schrijven, en u kunt deze met minimale richt lijnen gaan gebruiken.

Zie de [documentatie van Azure Data Explorer KQL](/azure/kusto/query) voor volledige documentatie over KQL en naslag informatie over de verschillende beschik bare functies.<br>
Zie [aan de slag met logboek query's in azure monitor](get-started-queries.md) voor een snelle walkthrough van de taal met gegevens uit Azure monitor Logboeken.
Zie [Azure monitor taal verschillen in de logboeken](data-explorer-difference.md) voor kleine verschillen in de versie van KQL die door Azure monitor wordt gebruikt.

## <a name="what-data-is-available-to-log-queries"></a>Welke gegevens zijn beschikbaar voor logboek query's?
Alle gegevens die in Azure Monitor logboeken zijn verzameld, kunnen worden opgehaald en geanalyseerd in logboek query's. Met verschillende gegevens bronnen worden de gegevens naar verschillende tabellen geschreven, maar u kunt meerdere tabellen in één query toevoegen om gegevens te analyseren over meerdere bronnen. Wanneer u een query bouwt, moet u eerst bepalen welke tabellen de gegevens bevatten die u zoekt. u hebt dus mini maal een basis informatie over hoe gegevens in Azure Monitor logboeken zijn gestructureerd.

Zie [bronnen van Azure monitor logboeken](../platform/data-platform-logs.md#sources-of-azure-monitor-logs)voor een lijst met verschillende gegevens bronnen waarmee Azure monitor-logboeken worden ingevuld.<br>
Zie de [structuur van Azure monitor logboeken](logs-structure.md) voor een uitleg van de manier waarop de gegevens worden gestructureerd.

## <a name="what-does-a-log-query-look-like"></a>Hoe ziet een logboek query eruit?
Een query kan net zo eenvoudig zijn als een enkele tabel naam voor het ophalen van alle records uit de tabel:

```Kusto
Syslog
```

U kunt ook filteren op bepaalde records, deze samenvatten en de resultaten in een grafiek visualiseren:

```
SecurityEvent
| where TimeGenerated > ago(7d)
| where EventID == 4625
| summarize count() by Computer, bin(TimeGenerated, 1h)
| render timechart 
```

Voor complexere analyses kunt u gegevens uit meerdere tabellen ophalen met behulp van een koppeling om de resultaten samen te analyseren.

```Kusto
app("ContosoRetailWeb").requests
| summarize count() by bin(timestamp,1hr)
| join kind= inner (Perf
    | summarize avg(CounterValue) 
      by bin(TimeGenerated,1hr))
on $left.timestamp == $right.TimeGenerated
```
Zelfs als u niet bekend bent met KQL, moet u in staat zijn om ten minste de basis logica te gebruiken die wordt gebruikt door deze query's. Ze beginnen met de naam van een tabel en voegen vervolgens meerdere opdrachten toe om die gegevens te filteren en verwerken. Een query kan elk wille keurig aantal opdrachten gebruiken en u kunt complexere query's schrijven wanneer u vertrouwd raakt met de verschillende KQL-opdrachten die beschikbaar zijn.

Zie [aan de slag met logboek query's in azure monitor](get-started-queries.md) voor een zelf studie over logboek query's waarmee de taal en algemene functies worden geïntroduceerd.<br>


## <a name="what-is-log-analytics"></a>Wat is Log Analytics?
Log Analytics is het primaire hulp programma in de Azure Portal voor het schrijven van logboek query's en het interactief analyseren van de resultaten. Zelfs als een logboekquery elders in Azure Monitor wordt gebruikt, moet u de query doorgaans eerst schrijven en testen met behulp van Log Analytics.

U kunt Log Analytics vanaf verschillende locaties in de Azure Portal starten. Het bereik van de beschik bare gegevens voor Log Analytics is afhankelijk van hoe u het start. Zie [query bereik](scope.md) voor meer informatie.

- Selecteer **Logboeken** in het menu **Azure monitor** of **log Analytics werk ruimte** menu.
- Selecteer **Logboeken** op de pagina **overzicht** van een Application Insights-toepassing.
- Selecteer **Logboeken** in het menu van een Azure-resource.

![Log Analytics](media/log-query-overview/log-analytics.png)

Zie [aan de slag met log Analytics in azure monitor](get-started-portal.md) voor een zelfstudie overzicht van log Analytics waarin verschillende functies worden geïntroduceerd.

## <a name="where-else-are-log-queries-used"></a>Waar worden ook logboek query's gebruikt?
Naast het interactief werken met logboek query's en hun resultaten in Log Analytics, zijn de volgende gebieden in Azure Monitor waar u query's gaat gebruiken:

- **Waarschuwings regels.** [Waarschuwings regels](../platform/alerts-overview.md) identificeren proactief problemen van gegevens in uw werk ruimte.  Elke waarschuwings regel is gebaseerd op een zoek opdracht in logboeken die automatisch regel matig wordt uitgevoerd.  De resultaten worden gecontroleerd om te bepalen of er een waarschuwing moet worden gemaakt.
- **Dash boards.** U kunt de resultaten van een wille keurige query vastmaken aan een [Azure-dash board](../learn/tutorial-logs-dashboards.md) , zodat u logboek-en metrische gegevens samenvoegt en optioneel kunt delen met andere Azure-gebruikers.
- **Weergaven.**  U kunt visualisaties van gegevens maken die moeten worden opgenomen in gebruikers dashboards met de [weer gave Designer](../platform/view-designer.md).  Logboek query's bieden de gegevens die worden gebruikt door [tegels](../platform/view-designer-tiles.md) en [visualisatie onderdelen](../platform/view-designer-parts.md) in elke weer gave.  
- **Gegevensexporttabel.**  Wanneer u logboek gegevens importeert van Azure Monitor naar Excel of [Power bi](../platform/powerbi.md), kunt u een logboek query maken om de gegevens te definiëren die moeten worden geëxporteerd.
- **Zo.** U kunt een Power shell-script uitvoeren vanaf een opdracht regel of een Azure Automation runbook dat gebruikmaakt van [Get-AzOperationalInsightsSearchResults](/powershell/module/az.operationalinsights/get-azoperationalinsightssearchresult) om logboek gegevens op te halen van Azure monitor.  Voor deze cmdlet is een query vereist om te bepalen welke gegevens moeten worden opgehaald.
- **API voor Azure Monitor-Logboeken.**  Met de [API voor Azure monitor-logboeken](https://dev.loganalytics.io) kan elke rest API-client logboek gegevens uit de werk ruimte ophalen.  De API-aanvraag bevat een query die wordt uitgevoerd op Azure Monitor om te bepalen welke gegevens moeten worden opgehaald.


## <a name="next-steps"></a>Volgende stappen
- Door loop een [zelf studie over het gebruik van log Analytics in het Azure Portal](get-started-portal.md).
- Door loop een [zelf studie over het schrijven van query's](get-started-queries.md).
