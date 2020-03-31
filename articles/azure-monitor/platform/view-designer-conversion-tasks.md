---
title: Algemene taken voor azure-monitorweergave ontwerper naar werkmappen
description: ''
author: austonli
ms.author: aul
ms.subservice: ''
ms.topic: conceptual
ms.date: 02/07/2020
ms.openlocfilehash: 5559dac916262998d621b40757398088ec613609
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77658741"
---
# <a name="view-designer-to-workbooks-conversion-common-tasks"></a>Algemene taken voor het converteren van ontwerper naar werkmappen weergeven
[View designer](view-designer.md) is een functie van Azure Monitor waarmee u aangepaste weergaven maken om gegevens in uw Log Analytics-werkruimte te visualiseren, met grafieken, lijsten en tijdlijnen. Ze worden uitgefaseerd en vervangen door werkmappen die extra functionaliteit bieden. In dit artikel worden taken beschreven die gebruikelijk zijn bij het converteren van weergaven naar werkmappen.


## <a name="quickstart-with-preset-view-designer-templates"></a>Snelstart met vooraf ingestelde weergave-ontwerpsjablonen

Werkmappen in Logboekanalyse-werkruimten hebben al sjablonen gemaakt om een aantal weergaven in de weergaveontwerper aan te passen. Selecteer onder de categorie **Designergidsen weergeven** **de optie Ontwerpovergangshandleiding weergeven** voor meer informatie over uw opties of selecteer een van de vooraf ingestelde sjablonen.

![Voorbeeldsjablonen](media/view-designer-conversion-tasks/templates.png)

## <a name="enabling-time-range-filter"></a>Tijdbereikfilter inschakelen
De ontwerper van de weergave heeft een ingebouwd standaardtijdbereikfilter, maar in werkmappen is deze instelling standaard niet ingeschakeld. Met werkmappen kunnen gebruikers wel hun eigen tijdbereikfilters maken die mogelijk meer van toepassing zijn op hun gegevenslogboeken. De stappen om het filter te genereren worden hieronder weergegeven:

Selecteer de optie **Parameters toevoegen.** De **standaardstijl** is ingesteld op *Pillen*.

![Param toevoegen](media/view-designer-conversion-tasks/add-param.png)

 Selecteer de knop **Parameter toevoegen.**

![Parameter toevoegen](media/view-designer-conversion-tasks/add-parameter.png)

Typ *TimeRange*in het menu zijbalk in het tekstvak **Parameternaam** . **Stel parametertype** in als *tijdbereikkiezer*. Schakel het selectievakje **Vereist?** in.

![Parametermenu](media/view-designer-conversion-tasks/parameter-menu.png)

Sla de parameter op in de linkerbovenhoek van het menu zijbalk. U de vervolgkeuzelijst standaard als *uitgeschakeld* laten of een standaard **tijdbereikwaarde** selecteren, zoals *24 uur*. Selecteer **Gereed bewerken**.

Parameters kunnen worden gebruikt in query's door krullende accolades toe te {} voegen rond uw parameternaam. Meer informatie over parameters is te vinden in de [werkboekendocumentatie over parameters.](https://github.com/microsoft/Application-Insights-Workbooks/blob/master/Documentation/Parameters/Parameters.md)

## <a name="updating-queries-with-the-timerange-parameter"></a>Query's bijwerken met de parameter TimeRange

### <a name="option-1-select-timerange-from-the-time-range-dropdown"></a>Optie 1: TimeRange selecteren in de vervolgkeuzelijst Tijdbereik

![Tijdsparameter](media/view-designer-conversion-tasks/time-parameter.png)

### <a name="option-2-update-your-log-queries"></a>Optie 2: Uw logboekquery's bijwerken

Voeg in uw query `| where TimeGenerated {TimeRange}` de regel toe: zoals in het volgende voorbeeld:

Oorspronkelijke query
```KQL
search * 
| summarize count() by Type
```

Bijgewerkte query
```KQL
search * 
| where TimeGenerated {TimeRange} 
| summarize count() by Type
```

## <a name="including-a-list"></a>Inclusief een lijst
De meeste weergave-designerweergaven bevatten een lijst en u deze standaardlijst reproduceren in een werkmap.

![Lijst met tegels](media/view-designer-conversion-tasks/tile-list.png)

Voeg een visualisatie toe door te klikken op **Query toevoegen** vanuit de celopties.

![Param toevoegen](media/view-designer-conversion-tasks/add-param.png)

De ontwerper van de weergave gebruikt een standaardquery die overeenkomt met de syntaxis uit het voorbeeld Origineel. Dit kan worden bijgewerkt door de query te wijzigen in het bijgewerkte formulier zoals in het volgende voorbeeld:

Oorspronkelijke query
```KQL
search * 
| summarize AggregatedValue = count() by Type
```

Bijgewerkte query
```KQL
search * 
| summarize Count = count() by Type
```

Hiermee wordt een lijst gegenereerd die lijkt op het volgende:

![Voorbeeld van lijst](media/view-designer-conversion-tasks/list-example.png)

## <a name="enabling-sparklines"></a>Sparklines inschakelen
Een veelvoorkomende functie voor rasters is het toevoegen van sparklines om verschillende gegevenspatronen in de loop van de tijd samen te vatten. View designer biedt de functie **Sparklines inschakelen** voor alle lijsten, net als werkmappen. Als u sparklines wilt opnemen in uw gegevens die overeenkomen met de ontwerper van de weergave, sluit u de gegevens aan bij uw oorspronkelijke query, zoals in het volgende voorbeeld:

Oorspronkelijke query
```KQL
search *
| summarize AggregatedValue = count() by Type) on Type
```

Bijgewerkte query
```KQL
search * 
| summarize AggregatedValue = count() by Type
| join kind = inner (search * 
    | make-series Trend = count() default=0 on TimeGenerated from {TimeRange:start} to {TimeRange:end} step {TimeRange:grain} by Type) on Type
| project Type, AggregatedValue, Trend
```

Selecteer **Kolominstellingen**.
![Kolominstellingen](media/view-designer-conversion-tasks/column-settings.png)

Werk de **vervolgkeuzelijst Kolomrenderer** bij als een *Spark-gebied*.
![Sparklines](media/view-designer-conversion-tasks/sparkline.png)

Sla de instellingen op en voer de query opnieuw uit om de tabel bij te werken met een sparkline.

Het resulterende raster ziet er ![op de volgende manier uit: Sparkline-voorbeeld](media/view-designer-conversion-tasks/sparkline-example.png)

## <a name="advanced-cell-settings"></a>Geavanceerde celinstellingen
Als u de ontwerper van de spiegelspiegel wilt spiegelen, u taken uitvoeren, zoals het wijzigen van de grootte van werkmapcellen of het toevoegen van pins en externe koppelingen aan logboeken.

Als u geavanceerde **instellingen wilt openen,** selecteert u het tandwielpictogram onder aan elke cel.

![Geavanceerde instellingen](media/view-designer-conversion-tasks/advanced-settings.png)

Dit geeft een menu weer met verschillende opties:

![Geavanceerde instellingen](media/view-designer-conversion-tasks/advanced-settings-settings.png)

Als u een pincode en een koppeling wilt toevoegen aan een externe query, schakelt u de bijbehorende selectievakjes in. Als u een titel aan uw cel wilt toevoegen, typt u de gewenste titel in de sectie **Grafiektitel.**

Standaard is elke werkmapcel ingesteld op het nemen van de volledige paginabreedte, maar u dit aanpassen door de cel omlaag te schalen onder het tabblad **Stijl** van het menu **Geavanceerde instellingen**

![Stijl voor geavanceerde instellingen](media/view-designer-conversion-tasks/advanced-settings-style.png)

 
## <a name="additional-parameters"></a>Aanvullende parameters
Selecteer **Parameter toevoegen** om een nieuwe parameter in uw werkmap te maken. 

Als u een abonnement wilt selecteren, typt u *Abonnement* in het veld **Parameternaam** in het zijmenu en selecteert *u Subscription Picker* in de vervolgkeuzelijst **Parametertype**

![Abonnementsmenu](media/view-designer-conversion-tasks/subscription-filter.png)

Als u een resource wilt selecteren, typt u *Resource* in het veld **Parameternaam** in het zijmenu en selecteert *u Resourcekiezer* in de vervolgkeuzelijst **Parametertype.**

![Resourcemenu](media/view-designer-conversion-tasks/resource-filter.png)

Hiermee worden vervolgteksten ingevoegd om u toegang te geven tot uw verschillende abonnementen en bronnen.

![Vervolgkeuzelijst voor abonnementsbronnen](media/view-designer-conversion-tasks/subscription-resource.png)


## <a name="next-steps"></a>Volgende stappen
- [Tegelconversies](view-designer-conversion-tiles.md)
