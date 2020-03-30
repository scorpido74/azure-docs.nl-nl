---
title: Conversies van Azure Monitor-weergave voor werkmappen
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: f07d15521c787dfd588c285bff57616059caa2f3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658623"
---
# <a name="azure-monitor-view-designer-tile-conversions"></a>Conversies voor designertegels in Azure Monitor-weergave
[View designer](view-designer.md) is een functie van Azure Monitor waarmee u aangepaste weergaven maken om gegevens in uw Log Analytics-werkruimte te visualiseren, met grafieken, lijsten en tijdlijnen. Ze worden uitgefaseerd en vervangen door werkmappen die extra functionaliteit bieden. In dit artikel vindt u details voor het converteren van verschillende tegels naar werkmappen.

## <a name="donut--list-tile"></a>&-lijsttegel Donut &

![Donutlijst](media/view-designer-conversion-tiles/donut-list.png)

Het opnieuw maken van de & lijsttegel in werkmappen omvat twee afzonderlijke visualisaties. Voor de donut gedeelte zijn er twee opties.
Voor beide beginnen met het selecteren **van Query toevoegen** en plakken de oorspronkelijke query van weergaveontwerper in de cel.

**Optie 1:** **Cirkeldiagram selecteren** in de ![ **vervolgkeuzelijst Visualisatie:** menu cirkeldiagramvisualisatie](media/view-designer-conversion-tiles/pie-chart.png)

**Optie 2:** Selecteer **Instellen op query** in de **vervolgkeuzelijst Visualisatie** en voeg aan de query toe: `| render piechart`

 ![Visualisatiemenu](media/view-designer-conversion-tiles/set-by-query.png)

**Voorbeeld**

Oorspronkelijke query
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc
```

Bijgewerkte query
```KQL
search * 
| summarize AggregatedValue = count() by Type 
| order by AggregatedValue desc 
| render piechart
```

Zie het artikel over [veelvoorkomende taken](view-designer-conversion-tasks.md)voor het maken van een lijst en het inschakelen van sparklines.

Hieronder volgt een voorbeeld van hoe de &-lijsttegel van de donut opnieuw kan worden geïnterpreteerd in werkmappen:

![Werkmappen met donutlijst](media/view-designer-conversion-tiles/donut-workbooks.png)

## <a name="line-chart--list-tile"></a>Lijndiagram & lijsttegel
![Lijst met lijndiagrammen](media/view-designer-conversion-tiles/line-list.png) 

Als u de query als volgt wilt opnieuw maken, wordt de query als volgt bijgewerkt:

Oorspronkelijke query
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Bijgewerkte query
```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type
```

Er zijn twee opties voor het visualiseren van het lijndiagram

**Optie 1:** Selecteer **Lijndiagram** in de **vervolgkeuzelijst Visualisatie:**
 
 ![Menu Lijndiagram](media/view-designer-conversion-tiles/line-visualization.png)

**Optie 2:** Selecteer **Instellen op query** in de **vervolgkeuzelijst Visualisatie** en voeg aan de query toe: `| render linechart`

 ![Visualisatiemenu](media/view-designer-conversion-tiles/set-by-query.png)

**Voorbeeld**

```KQL
search * 
| make-series Count = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type 
| render linechart_
```

Zie het artikel over [veelvoorkomende taken](view-designer-conversion-tasks.md)voor het maken van een lijst en het inschakelen van sparklines.

Hieronder volgt een voorbeeld van hoe het lijndiagram & lijsttegel opnieuw kan worden geïnterpreteerd in werkmappen:

![Werkmappen met lijndiagramlijst](media/view-designer-conversion-tiles/line-workbooks.png)

## <a name="number--list-tile"></a>Menu & nummer

 ![Lijst met tegels](media/view-designer-conversion-tiles/tile-list-example.png)

Werk de query bij voor de getaltegel als volgt:

Oorspronkelijke query
```KQL
search * 
| summarize AggregatedValue = count() by Computer | count
```

Bijgewerkte query
```KQL
search *
| summarize AggregatedValue = count() by Computer 
| summarize Count = count()
```

Wijzig de vervolgkeuzelijst Visualisatie in **Tegels** en selecteer **tegelinstellingen**.
 ![Tegelvisualisatie](media/view-designer-conversion-tiles/tile-visualization.png)

Laat de sectie **Titel** leeg en selecteer **Links**. De waarde voor **gebruik wijzigen:** **tellen**en **Kolomrenderer** in **Groot getal:**

![Tegelinstellingen](media/view-designer-conversion-tiles/tile-settings.png)

 
Zie het artikel over [veelvoorkomende taken](view-designer-conversion-tasks.md)voor het maken van een lijst en het inschakelen van sparklines.

Hieronder volgt een voorbeeld van hoe het getal & lijsttegel opnieuw kan worden geïnterpreteerd in werkmappen:

![Werkmappen met nummerlijst](media/view-designer-conversion-tiles/number-workbooks.png)

## <a name="timeline--list"></a>Tijdlijn en lijst

 ![Tijdlijnlijst](media/view-designer-conversion-tiles/time-list.png)

Update uw query als volgt voor de tijdlijn:

Oorspronkelijke query
```KQL
search * 
| sort by TimeGenerated desc
```

Bijgewerkte query
```KQL
search * 
| summarize Count = count() by Computer, bin(TimeGenerated,{TimeRange:grain})
```

Er zijn twee opties voor het visualiseren van de query als een staafdiagram:

**Optie 1:** **Staafdiagram selecteren** in de ![ **vervolgkeuzelijst Visualisatie:** Visualisatie van staafdiagram](media/view-designer-conversion-tiles/bar-visualization.png)
 
**Optie 2:** Selecteer **Instellen op query** in de **vervolgkeuzelijst Visualisatie** en voeg aan de query toe: `| render barchart`

 ![Menu Visualisatie](media/view-designer-conversion-tiles/set-by-query.png)

 
Zie het artikel over [veelvoorkomende taken](view-designer-conversion-tasks.md)voor het maken van een lijst en het inschakelen van sparklines.

Hieronder volgt een voorbeeld van hoe de & lijsttegel van de tijdlijn opnieuw kan worden geïnterpreteerd in werkmappen:

![Werkmappen met tijdlijnlijst](media/view-designer-conversion-tiles/time-workbooks.png)

## <a name="next-steps"></a>Volgende stappen

- [Overzicht van de overgang van weergaveontwerper naar werkmappen](view-designer-conversion-overview.md)
