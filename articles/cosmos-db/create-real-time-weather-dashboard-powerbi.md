---
title: Een realtime dashboard maken met Azure Cosmos DB, Azure Analysis Services en Power BI
description: Meer informatie over het maken van een live weerdashboard in Power BI met Azure Cosmos DB en Azure Analysis Services.
author: bharathsreenivas
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 09/04/2019
ms.author: bharathb
ms.reviewer: sngun
ms.openlocfilehash: d225a14edddcad58c08094dbc758d67df8f834e6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70376591"
---
# <a name="create-a-real-time-dashboard-using-azure-cosmos-db-and-power-bi"></a>Een realtime dashboard maken met Azure Cosmos DB en Power BI

In dit artikel worden de stappen beschreven die nodig zijn om een live weerdashboard in Power BI te maken met Azure Cosmos DB en Azure Analysis Services. Op het Power BI-dashboard worden grafieken weergegeven om realtime informatie weer te geven over temperatuur en neerslag in een regio.

## <a name="reporting-scenarios"></a>Rapportagescenario's

Er zijn meerdere manieren om rapportagedashboards in te stellen voor gegevens die zijn opgeslagen in Azure Cosmos DB. Afhankelijk van de vereisten en de grootte van de gegevens, beschrijft de volgende tabel de rapportage-instelling voor elk scenario:


|Scenario |Instellen |
|---------|---------|
|1. Ad-hocrapporten genereren (geen vernieuwing)    |  [Power BI Azure Cosmos DB-connector met importmodus](powerbi-visualize.md)       |
|2. Ad-hocrapporten genereren met periodieke vernieuwing   |  [Power BI Azure Cosmos DB-connector met importmodus (Geplande periodieke vernieuwing)](powerbi-visualize.md)       |
|3. Rapportage over grote gegevenssets (< 10 GB)     |  Power BI Azure Cosmos DB-connector met incrementele vernieuwing       |
|4. Real-time rapporteren over grote datasets    |  Power BI Azure Analysis Services-connector met directe query + Azure Analysis Services (Azure Cosmos DB-connector)       |
|5. Rapportage over live gegevens met aggregaten     |  [Power BI Spark-connector met directe query + Azure Databricks + Cosmos DB Spark-connector.](https://github.com/Azure/azure-cosmosdb-spark/wiki/Connecting-Cosmos-DB-with-PowerBI-using-spark-and-databricks-premium)       |
|6. Rapportage over live gegevens met aggregaten over grote gegevenssets   |  Power BI Azure Analysis Services-connector met direct query + Azure Analysis Services + Azure Databricks + Cosmos DB Spark-connector.       |

Scenario's 1 en 2 kunnen eenvoudig worden ingesteld met de Azure Cosmos DB Power BI-connector. In dit artikel worden de opstellingen voor scenario 3 en 4 beschreven.

### <a name="power-bi-with-incremental-refresh"></a>Power BI met incrementele vernieuwing

Power BI heeft een modus waarin incrementele vernieuwing kan worden geconfigureerd. Met deze modus hoeft u geen Azure Analysis Services-partities meer te maken en te beheren. Incrementele vernieuwing kan worden ingesteld om alleen de nieuwste updates in grote gegevenssets te filteren. Deze modus werkt echter alleen met de Power BI Premium-service met een gegevenssetbeperking van 10 GB.

### <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis connector + Azure Analysis Services

Azure Analysis Services biedt een volledig beheerd platform als een service die bedrijfsgegevensmodellen van bedrijfsniveau in de cloud host. Enorme gegevenssets kunnen vanuit Azure Cosmos DB worden geladen in Azure Analysis Services. Om te voorkomen dat de hele gegevensset voortdurend wordt opgevraagd, kunnen de gegevenssets worden onderverdeeld in Azure Analysis Services-partities, die onafhankelijk van elkaar kunnen worden vernieuwd op verschillende frequenties.

## <a name="power-bi-incremental-refresh"></a>Incrementele vernieuwing van Power BI

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Weergegevens innemen in Azure Cosmos DB

Stel een innamepijplijn in om [weergegevens](https://catalog.data.gov/dataset/local-weather-archive) te laden voor Azure Cosmos DB. U een [ADF-taak (Azure Data Factory)](../data-factory/connector-azure-cosmos-db.md) instellen om periodiek de nieuwste weersgegevens in Azure Cosmos DB te laden met behulp van de HTTP Source en Cosmos DB-sink.


### <a name="connect-power-bi-to-azure-cosmos-db"></a>Power BI verbinden met Azure Cosmos DB

1. **Azure Cosmos-account verbinden met Power BI** - Open de Power BI-bureaublad en gebruik de Azure Cosmos DB-connector om de juiste database en container te selecteren.

   ![Azure Cosmos DB - Power BI-connector](./media/create-real-time-weather-dashboard-powerbi/cosmosdb-powerbi-connector.png)

1. **Incrementele vernieuwing configureren** : Volg de stappen in [incrementele vernieuwing met het Power BI-artikel](/power-bi/service-premium-incremental-refresh) om incrementele vernieuwing voor de gegevensset te configureren. Voeg de parameters **RangeStart** en **RangeEnd** toe zoals weergegeven in de volgende schermafbeelding:

   ![Bereikparameters configureren](./media/create-real-time-weather-dashboard-powerbi/configure-range-parameters.png)

   Aangezien de gegevensset een kolom Datum heeft die zich in tekstvorm bevindt, moeten de parameters **RangeStart** en **RangeEnd** worden getransformeerd om het volgende filter te gebruiken. Wijzig in het deelvenster **Geavanceerde editor** de volgende tekst om de rijen te filteren op basis van de parameters RangeStart en RangeEnd:

   ```
   #"Filtered Rows" = Table.SelectRows(#"Expanded Document", each [Document.date] > DateTime.ToText(RangeStart,"yyyy-MM-dd") and [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd"))
   ```
   
   Afhankelijk van welke kolom en gegevenstype aanwezig is in de brongegevensset, u de velden RangeStart en RangeEnd dienovereenkomstig wijzigen

   
   |Eigenschap  |Gegevenstype  |Filteren  |
   |---------|---------|---------|
   |_ts     |   Numeriek      |  [_ts] > Duration.TotalSeconds(RangeStart - #datetime(1970, 1, 1, 0, 0, 0)) en [_ts] < Duration.TotalSeconds(RangeEnd - #datetime(1970, 1, 1, 0, 0, 0)))       |
   |Datum (bijvoorbeeld:- 2019-08-19)     |   Tekenreeks      | [Document.date]> DateTime.ToText(RangeStart,"yyyy-MM-dd") en [Document.date] < DateTime.ToText(RangeEnd,"yyyy-MM-dd")        |
   |Datum (bijvoorbeeld:- 2019-08-11 12:00:00)   |  Tekenreeks       |  [Document.date]> DateTime.ToText(RangeStart," yyyy-mm-dd HH:mm:ss") en [Document.date] < DateTime.ToText(RangeEnd,"yyyy-mm-dd HH:mm:ss")       |


1. **Het vernieuwingsbeleid definiëren** : definieer het vernieuwingsbeleid door te navigeren naar het tabblad **Incrementeren vernieuwen** in het **contextmenu** voor de tabel. Stel het vernieuwingsbeleid in om **elke dag** te vernieuwen en de gegevens van de laatste maand op te slaan.

   ![Vernieuwingsbeleid definiëren](./media/create-real-time-weather-dashboard-powerbi/define-refresh-policy.png)

   Negeer de waarschuwing waarin staat dat *de M-query niet kan worden bevestigd om te worden gevouwen.* De Azure Cosmos DB-connector vouwt filterquery's.

1. **Laad de gegevens en genereer de rapporten** - Met behulp van de gegevens die u eerder hebt geladen, maakt u de grafieken om te rapporteren over temperatuur en neerslag.

   ![Gegevens laden en rapport genereren](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

1. **Het rapport publiceren naar Power BI premium** - Aangezien incrementele vernieuwing een functie alleen Voor Extra is, staat het publicatiedialoogvenster alleen selectie van een werkruimte op Premium-capaciteit toe. De eerste vernieuwing kan langer duren omdat de historische gegevens moeten worden geïmporteerd. Latere gegevensvernieuwingen zijn veel sneller omdat ze incrementele vernieuwing gebruiken.


## <a name="power-bi-azure-analysis-connector--azure-analysis-services"></a>Power BI Azure Analysis connector + Azure Analysis Services 

### <a name="ingest-weather-data-into-azure-cosmos-db"></a>Weergegevens innemen in Azure Cosmos DB 

Stel een innamepijplijn in om [weergegevens](https://catalog.data.gov/dataset/local-weather-archive) te laden voor Azure Cosmos DB. U een ADF-taak (Azure Data Factory) instellen om periodiek de nieuwste weersgegevens in Azure Cosmos DB te laden met behulp van de HTTP Source en Cosmos DB Sink.

### <a name="connect-azure-analysis-services-to-azure-cosmos-account"></a>Azure Analysis Services verbinden met Azure Cosmos-account

1. **Maak een nieuw Azure Analysis Services-cluster** - [Maak een exemplaar van Azure Analysis-services](../analysis-services/analysis-services-create-server.md) in dezelfde regio als het Azure Cosmos-account en het Cluster Databricks.

1. **Maak een nieuw Analysis Services Tabular Project in Visual Studio** -  [Installeer de SQL Server Data Tools (SSDT)](/sql/ssdt/download-sql-server-data-tools-ssdt?view=sql-server-2017) en maak een Analysis Services Tabular project in Visual Studio.

   ![Azure Analysis Services-project maken](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-project.png)

   Kies de instantie **Integrated Workspace** en het compatibiliteitsniveau instellen op SQL **Server 2017 / Azure Analysis Services (1400)**

   ![Tabelmodelontwerper azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/tabular-model-designer.png)

1. **Voeg de Azure Cosmos DB-gegevensbron toe** : Navigeer naar**gegevensbronnen** >  **voor modellen**> **Nieuwe gegevensbron** en voeg de Azure Cosmos DB-gegevensbron toe zoals weergegeven in de volgende schermafbeelding:

   ![Cosmos DB-gegevensbron toevoegen](./media/create-real-time-weather-dashboard-powerbi/add-data-source.png)

   Maak verbinding met Azure Cosmos DB door het **account URI,** de **naam van**de database en de **containernaam**op te geven . U nu zien dat de gegevens uit de Azure Cosmos-container worden geïmporteerd in Power BI.

   ![Voorbeeld van Azure Cosmos DB-gegevens](./media/create-real-time-weather-dashboard-powerbi/preview-cosmosdb-data.png)

1. **Het analysis services-model construeren** - Open de queryeditor en voer de vereiste bewerkingen uit om de geladen gegevensset te optimaliseren:

   * Haal alleen de weergerelateerde kolommen (temperatuur en neerslag)

   * Haal de maandinformatie uit de tabel. Deze gegevens zijn handig bij het maken van partities zoals beschreven in de volgende sectie.

   * De temperatuurkolommen converteren naar getal

   De resulterende M-expressie is als volgt:

   ```
    let
        Source=#"DocumentDB/https://[ACCOUNTNAME].documents.azure.com:443/",
        #"Expanded Document" = Table.ExpandRecordColumn(Source, "Document", {"id", "_rid", "_self", "_etag", "fogground", "snowfall", "dust", "snowdepth", "mist", "drizzle", "hail", "fastest2minwindspeed", "thunder", "glaze", "snow", "ice", "fog", "temperaturemin", "fastest5secwindspeed", "freezingfog", "temperaturemax", "blowingsnow", "freezingrain", "rain", "highwind", "date", "precipitation", "fogheavy", "smokehaze", "avgwindspeed", "fastest2minwinddir", "fastest5secwinddir", "_attachments", "_ts"}, {"Document.id", "Document._rid", "Document._self", "Document._etag", "Document.fogground", "Document.snowfall", "Document.dust", "Document.snowdepth", "Document.mist", "Document.drizzle", "Document.hail", "Document.fastest2minwindspeed", "Document.thunder", "Document.glaze", "Document.snow", "Document.ice", "Document.fog", "Document.temperaturemin", "Document.fastest5secwindspeed", "Document.freezingfog", "Document.temperaturemax", "Document.blowingsnow", "Document.freezingrain", "Document.rain", "Document.highwind", "Document.date", "Document.precipitation", "Document.fogheavy", "Document.smokehaze", "Document.avgwindspeed", "Document.fastest2minwinddir", "Document.fastest5secwinddir", "Document._attachments", "Document._ts"}),
        #"Select Columns" = Table.SelectColumns(#"Expanded Document",{"Document.temperaturemin", "Document.temperaturemax", "Document.rain", "Document.date"}),
        #"Duplicated Column" = Table.DuplicateColumn(#"Select Columns", "Document.date", "Document.month"),
        #"Extracted First Characters" = Table.TransformColumns(#"Duplicated Column", {{"Document.month", each Text.Start(_, 7), type text}}),
        #"Sorted Rows" = Table.Sort(#"Extracted First Characters",{{"Document.date", Order.Ascending}}),
        #"Changed Type" = Table.TransformColumnTypes(#"Sorted Rows",{{"Document.temperaturemin", type number}, {"Document.temperaturemax", type number}}),
        #"Filtered Rows" = Table.SelectRows(#"Changed Type", each [Document.month] = "2019-07")
    in
        #"Filtered Rows"
   ```

   Wijzig bovendien het gegevenstype van de temperatuurkolommen in Decimal om ervoor te zorgen dat deze waarden kunnen worden uitgezet in Power BI.

1. **Azure Analysis-partities maken** - Maak partities in Azure Analysis Services om de gegevensset op te delen in logische partities die onafhankelijk en op verschillende frequenties kunnen worden vernieuwd. In dit voorbeeld maakt u twee partities die de gegevensset verdelen in de gegevens van de meest recente maand en al het andere.

   ![Analyseservicespartities maken](./media/create-real-time-weather-dashboard-powerbi/create-analysis-services-partitions.png)

   Maak de volgende twee partities in Azure Analysis Services:

   * **Laatste maand** - `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] = "2019-07")`
   * **Historische** -  `#"Filtered Rows" = Table.SelectRows(#"Sorted Rows", each [Document.month] <> "2019-07")`

1. **Implementeer het model naar de Azure Analysis Server** - Klik met de rechtermuisknop op het Azure Analysis Services-project en kies **Implementeren**. Voeg de servernaam toe aan het deelvenster **Eigenschappen van implementatieserver.**

   ![Azure Analysis Services-model implementeren](./media/create-real-time-weather-dashboard-powerbi/analysis-services-deploy-model.png)

1. **Partitievernieuwingen en -bewerkingen configureren** - Azure Analysis Services maakt onafhankelijke verwerking van partities mogelijk. Aangezien we willen dat de **partitie van de laatste maand** voortdurend wordt bijgewerkt met de meest recente gegevens, stelt u het vernieuwingsinterval in op 5 minuten. Het is niet vereist om de gegevens in historische partitie te vernieuwen. Bovendien moet u een code schrijven om de laatste maandpartitie te consolideren naar de historische partitie en een nieuwe laatste maandpartitie te maken.


## <a name="connect-power-bi-to-analysis-services"></a>Power BI verbinden met analyseservices

1. **Maak verbinding met de Azure Analysis Server via de Azure Analysis Services-databaseconnector** : Kies de **live-modus** en maak verbinding met de instantie Azure Analysis Services zoals weergegeven in de volgende schermafbeelding:

   ![Gegevens ophalen uit Azure Analysis Services](./media/create-real-time-weather-dashboard-powerbi/analysis-services-get-data.png)

1. **Laad de gegevens en genereer rapporten** - Maak met behulp van de gegevens die u eerder hebt geladen grafieken om te rapporteren over temperatuur en neerslag. Aangezien u een liveverbinding maakt, moeten de query's worden uitgevoerd op de gegevens in het Azure Analysis Services-model dat u in de vorige stap hebt geïmplementeerd. De temperatuurdiagrammen worden binnen vijf minuten nadat de nieuwe gegevens zijn geladen in Azure Cosmos DB bijgewerkt.

   ![De gegevens laden en rapporten genereren](./media/create-real-time-weather-dashboard-powerbi/load-data-generate-report.png)

## <a name="next-steps"></a>Volgende stappen

* Zie Aan de slag [met Power BI](https://powerbi.microsoft.com/documentation/powerbi-service-get-started/)voor meer informatie over Power BI.

* [Verbind Qlik Sense met Azure Cosmos DB en visualiseer uw gegevens](visualize-qlik-sense.md)