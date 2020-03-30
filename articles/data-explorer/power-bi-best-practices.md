---
title: Aanbevolen procedures voor het gebruik van Power BI om Azure Data Explorer-gegevens op te vragen en te visualiseren
description: In dit artikel leert u aanbevolen procedures voor het gebruik van Power BI om Azure Data Explorer-gegevens op te vragen en te visualiseren.
author: orspod
ms.author: orspodek
ms.reviewer: gabil
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/26/2019
ms.openlocfilehash: db1d530c9cab77ae612c83a0d4f52478fb9ee270
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79251737"
---
# <a name="best-practices-for-using-power-bi-to-query-and-visualize-azure-data-explorer-data"></a>Aanbevolen procedures voor het gebruik van Power BI om Azure Data Explorer-gegevens op te vragen en te visualiseren

Azure Data Explorer is een snelle en zeer schaalbare service voor gegevensverkenning voor telemetrische gegevens en gegevens uit logboeken. [Power BI](https://docs.microsoft.com/power-bi/) is een business analytics-oplossing waarmee u uw gegevens visualiseren en de resultaten in uw hele organisatie delen. Azure Data Explorer biedt drie opties voor het maken van verbinding met gegevens in Power BI. Gebruik de [ingebouwde connector,](power-bi-connector.md) [importeer een query uit Azure Data Explorer in Power BI](power-bi-imported-query.md)of gebruik een [SQL-query](power-bi-sql-query.md). In dit artikel vindt u tips voor het opvragen en visualiseren van uw Azure Data Explorer-gegevens met Power BI. 

## <a name="best-practices-for-using-power-bi"></a>Aanbevolen procedures voor het gebruik van Power BI 

Wanneer u met terabytes aan nieuwe ruwe gegevens werkt, volgt u deze richtlijnen om Power BI-dashboards en rapporten pittig en bijgewerkt te houden:

* **Reislicht** - Breng alleen de gegevens die u nodig hebt voor uw rapporten naar Power BI. Gebruik voor diepgaande interactieve analyse de [Web-gebruikersinterface van Azure Data Explorer](web-query-data.md) die is geoptimaliseerd voor ad-hocverkenning met de Kusto Query-taal.

* **Samengesteld model** - Gebruik [samengesteld model](https://docs.microsoft.com/power-bi/desktop-composite-models) om geaggregeerde gegevens te combineren voor dashboards op het hoogste niveau met gefilterde operationele ruwe gegevens. U duidelijk bepalen wanneer u ruwe gegevens moet gebruiken en wanneer u een geaggregeerde weergave moet gebruiken. 

* **Importmodus versus DirectQuery-modus** - Gebruik **de importmodus** voor interactie met kleinere gegevenssets. Gebruik **de DirectQuery-modus** voor grote, vaak bijgewerkte gegevenssets. Maak bijvoorbeeld dimensietabellen met **de importmodus** omdat ze klein zijn en niet vaak veranderen. Stel het vernieuwingsinterval in op basis van de verwachte snelheid van gegevensupdates. Maak feitentabellen met de **DirectQuery-modus,** omdat deze tabellen groot zijn en ruwe gegevens bevatten. Gebruik deze tabellen om gefilterde gegevens weer te geven met behulp van Power [BI-drillthrough](https://docs.microsoft.com/power-bi/desktop-drillthrough).

* **Parallellisme** – Azure Data Explorer is een lineair schaalbaar dataplatform, daarom u de prestaties van dashboardrendering verbeteren door de parallellisme van de end-to-end-stroom als volgt te verhogen:

   * Het aantal [gelijktijdige verbindingen in DirectQuery in Power BI verhogen](https://docs.microsoft.com/power-bi/desktop-directquery-about#maximum-number-of-connections-option-for-directquery).

   * Gebruik [zwakke consistentie om het parallellisme te verbeteren.](/azure/kusto/concepts/queryconsistency) Dit kan gevolgen hebben voor de versheid van de gegevens.

* **Effectieve slicers** : gebruik [synchronisatieslicers](https://docs.microsoft.com/power-bi/visuals/power-bi-visualization-slicers#sync-and-use-slicers-on-other-pages) om te voorkomen dat rapporten gegevens laden voordat u er klaar voor bent. Nadat u de gegevensset hebt gestructureerd, alle visuals hebt geplaatst en alle slicers hebt markering, u de synchronisatieslicer selecteren om alleen de benodigde gegevens te laden.

* **Filters gebruiken** : gebruik zoveel mogelijk Power BI-filters om de azure data explorer-zoekopdracht te richten op de relevante gegevensscherven.

* **Efficiënte visuals** – Selecteer de meest performante visuals voor uw gegevens.

## <a name="tips-for-using-the-azure-data-explorer-connector-for-power-bi-to-query-data"></a>Tips voor het gebruik van de Azure Data Explorer-connector voor Power BI om gegevens op te vragen

In het volgende gedeelte vindt u tips en trucs voor het gebruik van Kusto-querytaal met Power BI. De [Azure Data Explorer-connector voor Power BI](power-bi-connector.md) gebruiken om gegevens te visualiseren

### <a name="complex-queries-in-power-bi"></a>Complexe query's in Power BI

Complexe query's worden gemakkelijker uitgedrukt in Kusto dan in Power Query. Ze moeten worden geïmplementeerd als [Kusto-functies](/azure/kusto/query/functions)en worden aangeroepen in Power BI. Deze methode is vereist bij `let` het gebruik van **DirectQuery** met instructies in uw Kusto-query. Omdat Power BI twee query's samenvoegt en `let` `join` instructies niet met de operator kunnen worden gebruikt, kunnen syntaxisfouten optreden. Sla daarom elk deel van de join op als kusto-functie en laat Power BI deze twee functies samenvoegen.

### <a name="how-to-simulate-a-relative-date-time-operator"></a>Een relatieve datum-tijdoperator simuleren

Power BI bevat geen *relatieve* datum-tijdoperator `ago()`zoals .
Gebruik `ago()`een combinatie van `DateTime.FixedLocalNow()` `#duration` en Power BI-functies om te simuleren.

In plaats van `ago()` deze query met de operator:

```kusto
    StormEvents | where StartTime > (now()-5d)
    StormEvents | where StartTime > ago(5d)
``` 

Gebruik de volgende gelijkwaardige query:

```powerquery-m
let
    Source = Kusto.Contents("help", "Samples", "StormEvents", []),
    #"Filtered Rows" = Table.SelectRows(Source, each [StartTime] > (DateTime.FixedLocalNow()-#duration(5,0,0,0)))
in
    #"Filtered Rows"
```

### <a name="reaching-kusto-query-limits"></a>Kusto-querylimieten bereiken 

Kusto-query's retourneren standaard tot 500.000 rijen of 64 MB, zoals beschreven in [querylimieten.](/azure/kusto/concepts/querylimits) U deze standaardinstellingen overschrijven door **geavanceerde opties** te gebruiken in het verbindingsvenster van Azure Data **Explorer (Kusto):**

![geavanceerde opties](media/power-bi-best-practices/advanced-options.png)

Met deze opties [worden overzichten](/azure/kusto/query/setstatement) met uw query ingesteld om de standaardquerylimieten te wijzigen:

  * **Als queryresultaatrecordnummer beperken,** genereert een`set truncationmaxrecords`
  * **De gegevensgrootte van queryresultaten beperken in Bytes** genereert een`set truncationmaxsize`
  * **Resultaatset-truncation uitschakelen** genereert een`set notruncation`

### <a name="using-query-parameters"></a>Queryparameters gebruiken

U [queryparameters](/azure/kusto/query/queryparametersstatement) gebruiken om uw query dynamisch te wijzigen. 

#### <a name="using-a-query-parameter-in-the-connection-details"></a>Een queryparameter gebruiken in de verbindingsdetails

Gebruik een queryparameter om informatie in de query te filteren en de queryprestaties te optimaliseren.
 
In het venster **Query's bewerken,** **Geavanceerde editor van** **Start** > 

1. Zoek het volgende gedeelte van de query:

    ```powerquery-m
    Source = Kusto.Contents("<Cluster>", "<Database>", "<Query>", [])
    ```
   
   Bijvoorbeeld:

    ```powerquery-m
    Source = Kusto.Contents("Help", "Samples", "StormEvents | where State == 'ALABAMA' | take 100", [])
    ```

1. Vervang het relevante deel van de query door uw parameter. Splits de query in meerdere delen en samen met een &, samen met de parameter.

   In de bovenstaande query nemen we bijvoorbeeld `State == 'ALABAMA'` het onderdeel op `State == '` en `'` splitsen we het `State` op: en we plaatsen de parameter ertussen:
   
    ```kusto
    "StormEvents | where State == '" & State & "' | take 100"
    ```

1. Als uw query aanhalingstekens bevat, codeert u deze correct. Bijvoorbeeld de volgende query: 

   ```kusto
   "StormEvents | where State == "ALABAMA" | take 100" 
   ```

   verschijnt in de **Geavanceerde Editor** als volgt met twee aanhalingstekens:

   ```kusto
    "StormEvents | where State == ""ALABAMA"" | take 100"
   ```

   Deze query moet worden vervangen door de volgende query met drie aanhalingstekens:

   ```kusto
   "StormEvents | where State == """ & State & """ | take 100"
   ```

#### <a name="use-a-query-parameter-in-the-query-steps"></a>Een queryparameter gebruiken in de querystappen

U een queryparameter gebruiken in elke querystap die deze ondersteunt. Filter bijvoorbeeld de resultaten op basis van de waarde van een parameter.

![filterresultaten met behulp van een parameter](media/power-bi-best-practices/filter-using-parameter.png)

### <a name="dont-use-power-bi-data-refresh-scheduler-to-issue-control-commands-to-kusto"></a>Gebruik de planner van Power BI-gegevensvernieuwing niet om besturingselementopdrachten uit te geven aan Kusto

Power BI bevat een gegevensvernieuwingsplanner die periodiek query's kan uitgeven aan een gegevensbron. Dit mechanisme mag niet worden gebruikt om besturingsopdrachten naar Kusto te plannen, omdat Power BI ervan uitgaat dat alle query's alleen-lezen zijn.

### <a name="power-bi-can-send-only-short-lt2000-characters-queries-to-kusto"></a>Power BI kan alleen&lt;korte query's (2000 tekens) naar Kusto verzenden

Als het uitvoeren van een query in Power BI resulteert in de volgende fout: _"DataSource.Error: Web.Contents failed to get contents from..."_ de query is waarschijnlijk langer dan 2000 tekens. Power BI gebruikt **PowerQuery** om Kusto op te vragen door een HTTP GET-verzoek uit te geven dat de query codeert als onderdeel van de URI die wordt opgehaald. Daarom zijn Kusto-query's die door Power BI zijn uitgegeven beperkt tot de maximale lengte van een aanvraag URI (2000 tekens, minus kleine verschuiving). Als tijdelijke oplossing u een [opgeslagen functie](/azure/kusto/query/schema-entities/stored-functions) in Kusto definiëren en Power BI die functie in de query laten gebruiken.

## <a name="next-steps"></a>Volgende stappen

[Gegevens visualiseren met de Azure Data Explorer-connector voor Power BI](power-bi-connector.md)




