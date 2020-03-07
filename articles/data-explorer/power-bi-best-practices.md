---
title: Aanbevolen procedures voor het gebruik van Power BI om gegevens van Azure Data Explorer te doorzoeken en te visualiseren
description: In dit artikel leert u aanbevolen procedures voor het gebruik van Power BI voor het opvragen en visualiseren van Azure Data Explorer-gegevens.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: db1d530c9cab77ae612c83a0d4f52478fb9ee270
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2020
ms.locfileid: "78395227"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>Aanbevolen procedures voor het gebruik van Power BI om gegevens van Azure Data Explorer te doorzoeken en te visualiseren

Azure Data Explorer is een snelle en zeer schaalbare service om gegevens in logboeken en telemetriegegevens te verkennen. [Power bi](https://docs.microsoft.com/power-bi/) is een Business Analytics-oplossing waarmee u uw gegevens kunt visualiseren en de resultaten van uw organisatie kan delen. Azure Data Explorer biedt drie opties voor het maken van verbinding met gegevens in Power BI. Gebruik de [ingebouwde connector](power-bi-connector.md), [Importeer een query van Azure Data Explorer in Power bi](power-bi-imported-query.md)of gebruik een [SQL-query](power-bi-sql-query.md). In dit artikel vindt u tips voor het uitvoeren van query's en het visualiseren van uw Azure-Data Explorer gegevens met Power BI. 

## <a name="best-practices-for-using-power-bi"></a>Aanbevolen procedures voor het gebruik van Power BI 

Wanneer u werkt met terabytes aan verse onbewerkte gegevens, volgt u deze richt lijnen om Power BI Dash boards en rapporten Snappy en bijgewerkt te houden:

* **Travel Light** : breng alleen de gegevens die u nodig hebt om uw rapporten te Power bi. Voor een diepe interactieve analyse gebruikt u de [Web-gebruikers interface van Azure Data Explorer](web-query-data.md) die is geoptimaliseerd voor het verkennen van ad-hoc met de Kusto-query taal.

* **Samengesteld model** : gebruik een [samengesteld model](https://docs.microsoft.com/power-bi/desktop-composite-models) om geaggregeerde gegevens te combi neren voor dash boards op het hoogste niveau met gefilterde operationele onbewerkte gegevens. U kunt duidelijk bepalen wanneer onbewerkte gegevens worden gebruikt en wanneer een geaggregeerde weer gave moet worden gebruikt. 

* **Import modus versus DirectQuery-modus** : gebruik de **import** modus voor interactie van kleinere gegevens sets. Gebruik de modus **DirectQuery** voor grote, vaak bijgewerkte gegevens sets. Maak bijvoorbeeld dimensie tabellen met de **import** modus, omdat ze klein zijn en niet vaak veranderen. Stel het Vernieuwings interval in op basis van de verwachte frequentie van gegevens updates. Maak feiten tabellen met behulp van de **DirectQuery** -modus omdat deze tabellen groot zijn en onbewerkte gegevens bevatten. Gebruik deze tabellen om gefilterde gegevens te presen teren met Power BI [drillthrough](https://docs.microsoft.com/power-bi/desktop-drillthrough).

* **Parallellisme** : Azure Data Explorer is een lineair schaalbaar gegevens platform. Daarom kunt u de prestaties van de dashboard weergave verbeteren door de parallelle defactor van de end-to-end-stroom als volgt te verhogen:

   * Verhoog het aantal [gelijktijdige verbindingen in DirectQuery in Power bi](https://docs.microsoft.com/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery).

   * Gebruik [zwakke consistentie om de parallelle afzwakking te verbeteren](/azure/kusto/concepts/queryconsistency). Dit kan invloed hebben op de versheid van de gegevens.

* **Efficiënte Slicers** : gebruik [synchronisatie Slicers](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages) om te voor komen dat rapporten gegevens laden voordat u klaar bent. Nadat u de gegevensset hebt gestructureerd, alle visuals hebt geplaatst en alle Slicers markeert, kunt u de slicer synchroniseren selecteren om alleen de benodigde gegevens te laden.

* **Filters gebruiken** : zoveel mogelijke Power bi filters gebruiken om de Azure Data Explorer zoeken naar de relevante gegevens Shards te richten.

* **Efficiënte visuele elementen** – Selecteer de meest visuele visualisaties voor uw gegevens.

## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>Tips voor het gebruik van de Azure Data Explorer-connector voor Power BI om gegevens op te vragen

De volgende sectie bevat tips en trucs voor het gebruik van Kusto-query taal met Power BI. De [Azure Data Explorer-connector voor Power bi](power-bi-connector.md) gebruiken om gegevens te visualiseren

### <a name="complex-queries-in-power-bi"></a>Complexe query's in Power BI

Complexe query's zijn eenvoudiger te zien in Kusto dan in Power Query. Ze moeten worden geïmplementeerd als [Kusto-functies](/azure/kusto/query/functions)en worden aangeroepen in Power bi. Deze methode is vereist voor het gebruik van **DirectQuery** met `let`-instructies in uw Kusto-query. Omdat Power BI twee query's samenvoegt en `let`-instructies niet kunnen worden gebruikt met de operator `join`, kunnen er syntaxis fouten optreden. Sla daarom elk deel van de samen voeging op als een Kusto-functie en sta Power BI toe deze twee functies samen te voegen.

### <a name="how-to-simulate-a-relative-date-time-operator"></a>Een relatieve datum-tijd operator simuleren

Power BI bevat geen *relatieve* datum-tijd operator, zoals `ago()`.
Als u `ago()`wilt simuleren, gebruikt u een combi natie van `DateTime.FixedLocalNow()` en `#duration` Power BI-functies.

In plaats van deze query met behulp van de operator `ago()`:

```kusto
    StormEvents | where StartTime > (now()-5d)
    StormEvents | where StartTime > ago(5d)
``` 

Gebruik de volgende overeenkomende query:

```powerquery-m
let
    Source = Kusto.Contents("help", "Samples", "StormEvents", []),
    #"Filtered Rows" = Table.SelectRows(Source, each [StartTime] > (DateTime.FixedLocalNow()-#duration(5,0,0,0)))
in
    #"Filtered Rows"
```

### <a name="reaching-kusto-query-limits"></a>Query limieten van Kusto bereiken 

Kusto-query's retour neren standaard Maxi maal 500.000 rijen of 64 MB, zoals wordt beschreven in [query limieten](/azure/kusto/concepts/querylimits). U kunt deze standaard waarden negeren door gebruik te maken van **Geavanceerde opties** in het **Azure Data Explorer (Kusto)-** verbindings venster:

![Geavanceerde opties](media/power-bi-best-practices/advanced-options.png)

Met deze opties geeft u de instructies voor het [instellen](/azure/kusto/query/setstatement) van een query om de standaard query limieten te wijzigen:

  * Hiermee wordt een **`set truncationmaxrecords` gegenereerd**
  * De **gegevens grootte van het query resultaat beperken in bytes** genereert een `set truncationmaxsize`
  * De **afbreking van resultaten sets uitschakelen** genereert een `set notruncation`

### <a name="using-query-parameters"></a>Query parameters gebruiken

U kunt [query parameters](/azure/kusto/query/queryparametersstatement) gebruiken om uw query dynamisch te wijzigen. 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>Een query parameter gebruiken in de verbindings Details

Gebruik een query parameter om gegevens in de query te filteren en de query prestaties te optimaliseren.
 
In het venster **Query's bewerken** **Start** > **Geavanceerde editor**

1. Zoek de volgende sectie van de query:

    ```powerquery-m
    Source = Kusto.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```
   
   Bijvoorbeeld:

    ```powerquery-m
    Source = Kusto.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. Vervang het relevante deel van de query door de para meter. Splits de query in meerdere delen en voeg deze opnieuw toe met behulp van een en-teken (&), samen met de para meter.

   In de bovenstaande query nemen we de `State == 'ALABAMA'` deel, en splitsen we deze naar: `State == '` en `'` en de `State` para meter ertussen worden geplaatst:
   
    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. Als uw query aanhalings tekens bevat, kunt u deze op de juiste wijze coderen. U kunt bijvoorbeeld de volgende query uitvoeren: 

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100" 
   ```

   wordt als volgt in de **Geavanceerde editor** weer gegeven met twee aanhalings tekens:

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   Deze moet worden vervangen door de volgende query met drie dubbele aanhalings tekens:

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>Een query parameter gebruiken in de query stappen

U kunt een query parameter gebruiken in elke query stap die deze ondersteunt. U kunt bijvoorbeeld de resultaten filteren op basis van de waarde van een para meter.

![resultaten filteren met behulp van een para meter](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>Gebruik Scheduler voor gegevens vernieuwing niet Power BI om besturings opdrachten te geven aan Kusto

Power BI bevat een scheduler voor het vernieuwen van gegevens waarmee periodiek query's kunnen worden uitgevoerd op een gegevens bron. Dit mechanisme mag niet worden gebruikt om besturings opdrachten te plannen voor Kusto, omdat Power BI ervan uitgaan dat alle query's alleen-lezen zijn.

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>Power BI kunt alleen korte (&lt;2000 tekens) query's verzenden naar Kusto

Als het uitvoeren van een query in Power BI resulteert in de volgende fout: _Data Source. error: Web. Contents kan de inhoud niet ophalen...._ de query is waarschijnlijk langer dan 2000 tekens. Power BI gebruikt **PowerQuery** voor het opvragen van Kusto door een HTTP GET-aanvraag uit te geven die de query codeert als onderdeel van de URI die wordt opgehaald. Daarom zijn Kusto-query's die zijn uitgegeven door Power BI beperkt tot de maximum lengte van een aanvraag-URI (2000 tekens, min kleine offset). Als tijdelijke oplossing kunt u een [opgeslagen functie](/azure/kusto/query/schema-entities/stored-functions) definiëren in Kusto en deze functie Power bi gebruiken in de query.

## <a name="next-steps"></a>Volgende stappen

[Gegevens visualiseren met de Azure Data Explorer-connector voor Power BI](power-bi-connector.md)




