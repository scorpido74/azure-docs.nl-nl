---
title: Grafieken en diagrammen maken op azure monitorlogboekquery's | Microsoft Documenten
description: Beschrijft verschillende visualisaties in Azure Monitor om uw logboekgegevens op verschillende manieren weer te geven.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 8a515f01bfa9f8ec579c51b806c997d79b629250
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77670318"
---
# <a name="creating-charts-and-diagrams-from-azure-monitor-log-queries"></a>Grafieken en diagrammen maken op azure monitor-logboekquery's

> [!NOTE]
> U moet [geavanceerde aggregaties in Azure Monitor-logboekquery's](advanced-aggregations.md) voltooien voordat u deze les voltooit.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

In dit artikel worden verschillende visualisaties in Azure Monitor beschreven om uw logboekgegevens op verschillende manieren weer te geven.

## <a name="charting-the-results"></a>De resultaten in kaart brengen
Controleer eerst hoeveel computers er per besturingssysteem zijn, in het afgelopen uur:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Standaard worden resultaten weergegeven als een tabel:

![Tabel](media/charts/table-display.png)

Als u een beter beeld wilt krijgen, selecteert u **Grafiek**en kiest u de optie **Cirkel** om de resultaten te visualiseren:

![Cirkeldiagram](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>Tijddiagrammen
Toon de gemiddelde, 50e en 95e percentile van processortijd in opslagbakken van 1 uur. De query genereert meerdere reeksen en u vervolgens selecteren welke reeksen u wilt weergeven in het tijddiagram:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Selecteer de optie **Grafiek lijn:**

![Lijndiagram](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>Referentieregel

Een referentieregel kan u helpen eenvoudig te identificeren of de statistiek een specifieke drempelwaarde heeft overschreden. Als u een lijn aan een grafiek wilt toevoegen, breidt u de gegevensset uit met een constante kolom:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Referentieregel](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>Meerdere dimensies
Meerdere expressies `by` in `summarize` de clausule van meerdere rijen maken in de resultaten, één voor elke combinatie van waarden.

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

Wanneer u de resultaten als een grafiek bekijkt, `by` wordt de eerste kolom van de component gebruikt. In het volgende voorbeeld wordt een gestapeld kolomdiagram weergegeven met de _Gebeurtenis-ID._ Afmetingen moeten van `string` type zijn, dus in dit voorbeeld wordt de _EventID_ naar de tekenreeks gegoten. 

![Gebeurtenis-ID voor staafdiagram](media/charts/charts-and-diagrams-multiDimension1.png)

U schakelen tussen door de vervolgkeuzelijst met de kolomnaam te selecteren. 

![Rekeningtekst voor staafdiagram](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>Volgende stappen
Bekijk andere lessen voor het gebruik van de [Kusto-querytaal](/azure/kusto/query/) met Azure Monitor-logboekgegevens:

- [Tekenreeksbewerkingen](string-operations.md)
- [Datum- en tijdbewerkingen](datetime-operations.md)
- [Aggregatiefuncties](aggregations.md)
- [Geavanceerde aggregaties](advanced-aggregations.md)
- [JSON en gegevensstructuren](json-data-structures.md)
- [Geavanceerde query's schrijven](advanced-query-writing.md)
- [Joins](joins.md)
