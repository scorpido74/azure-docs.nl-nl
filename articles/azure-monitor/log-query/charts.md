---
title: Grafieken en diagrammen maken op basis van Azure Monitor-logboek query's | Microsoft Docs
description: Beschrijft verschillende visualisaties in Azure Monitor om uw logboek gegevens op verschillende manieren weer te geven.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 08/16/2018
ms.openlocfilehash: 8a515f01bfa9f8ec579c51b806c997d79b629250
ms.sourcegitcommit: 747a20b40b12755faa0a69f0c373bd79349f39e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/27/2020
ms.locfileid: "77670318"
---
# <a name="creating-charts-and-diagrams-from-azure-monitor-log-queries"></a>Grafieken en diagrammen maken op basis van Azure Monitor-logboek query's

> [!NOTE]
> U moet [Geavanceerde aggregaties in azure monitor logboek query's](advanced-aggregations.md) volt ooien voordat u deze les kunt volt ooien.

[!INCLUDE [log-analytics-demo-environment](../../../includes/log-analytics-demo-environment.md)]

In dit artikel worden verschillende visualisaties in Azure Monitor beschreven om uw logboek gegevens op verschillende manieren weer te geven.

## <a name="charting-the-results"></a>Grafieken van de resultaten
Begin met het controleren van het aantal computers per besturings systeem, gedurende het afgelopen uur:

```Kusto
Heartbeat
| where TimeGenerated > ago(1h)
| summarize count(Computer) by OSType  
```

Standaard worden resultaten weer gegeven als een tabel:

![Tabel](media/charts/table-display.png)

Als u een betere weer gave wilt, selecteert u **grafiek**en kiest u de optie **cirkel** om de resultaten te visualiseren:

![Cirkel diagram](media/charts/charts-and-diagrams-pie.png)


## <a name="timecharts"></a>Timecharts
De gemiddelde, 50e en 95e percentielen van de processor tijd weer geven in de opslag locaties van 1 uur. De query genereert meerdere reeksen en u kunt vervolgens selecteren welke reeks moet worden weer gegeven in de tijd grafiek:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
```

Selecteer de weergave optie **lijn** diagram:

![Lijndiagram](media/charts/charts-and-diagrams-multiSeries.png)

### <a name="reference-line"></a>Referentie lijn

Met een referentie lijn kunt u gemakkelijk identificeren of de metriek een specifieke drempel waarde overschrijdt. Als u een lijn wilt toevoegen aan een grafiek, breidt u de gegevensset uit met een constante kolom:

```Kusto
Perf
| where TimeGenerated > ago(1d) 
| where CounterName == "% Processor Time" 
| summarize avg(CounterValue), percentiles(CounterValue, 50, 95)  by bin(TimeGenerated, 1h)
| extend Threshold = 20
```

![Referentie lijn](media/charts/charts-and-diagrams-multiSeriesThreshold.png)

## <a name="multiple-dimensions"></a>Meerdere dimensies
Meerdere expressies in de `by`-component van `summarize` meerdere rijen in de resultaten maken, één voor elke combi natie van waarden.

```Kusto
SecurityEvent
| where TimeGenerated > ago(1d)
| summarize count() by tostring(EventID), AccountType, bin(TimeGenerated, 1h)
```

Wanneer u de resultaten weergeeft als een diagram, wordt de eerste kolom uit de `by`-component gebruikt. In het volgende voor beeld ziet u een gestapeld kolom diagram met behulp van de gebeurtenis-naam _._ De dimensies moeten van het type `string` zijn, dus in dit voor beeld wordt de _gebeurtenis_ -activiteit geconverteerd naar een teken reeks. 

![Werk balk grafiek-gebeurtenis](media/charts/charts-and-diagrams-multiDimension1.png)

U kunt scha kelen tussen door de vervolg keuzelijst te selecteren met de naam van de kolom. 

![Account type van staaf diagram](media/charts/charts-and-diagrams-multiDimension2.png)

## <a name="next-steps"></a>Volgende stappen
Zie andere lessen voor het gebruik van de [Kusto-query taal](/azure/kusto/query/) met Azure monitor-logboek gegevens:

- [Teken reeks bewerkingen](string-operations.md)
- [Datum-en tijd bewerkingen](datetime-operations.md)
- [Aggregatie functies](aggregations.md)
- [Geavanceerde aggregaties](advanced-aggregations.md)
- [JSON en gegevens structuren](json-data-structures.md)
- [Geavanceerde query's schrijven](advanced-query-writing.md)
- [Joins](joins.md)
