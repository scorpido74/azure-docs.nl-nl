---
title: Azure Cosmos DB bewaken | Microsoft Documenten
description: Meer informatie over het bewaken van de prestaties en beschikbaarheid van Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: b9b66c379714c2f4fa2421876fda3bdb500ce6c1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78250426"
---
# <a name="monitoring-azure-cosmos-db"></a>Azure Cosmos DB bewaken
Wanneer u kritieke toepassingen en bedrijfsprocessen hebt die afhankelijk zijn van Azure-resources, wilt u deze resources controleren op hun beschikbaarheid, prestaties en werking. In dit artikel worden de bewakingsgegevens beschreven die zijn gegenereerd door Azure Cosmos-databases en hoe u de functies van Azure Monitor gebruiken om deze gegevens te analyseren en te waarschuwen.

## <a name="what-is-azure-monitor"></a>Wat is Azure Monitor?
Azure Cosmos DB maakt bewakingsgegevens met [Azure Monitor,](../azure-monitor/overview.md) een volledige stackmonitoringservice in Azure die een complete set functies biedt om uw Azure-resources te bewaken, naast resources in andere clouds en on-premises. 

Als u nog niet bekend bent met het bewaken van Azure-services, begint u met het artikel [Azure-bronnen bewaken met Azure Monitor,](../azure-monitor/insights/monitor-azure-resource.md) waarin het volgende wordt beschreven:

- Wat is Azure Monitor?
- Kosten in verband met monitoring
- Gegevens controleren die zijn verzameld in Azure
- Gegevensverzameling configureren
- Standaardhulpprogramma's in Azure voor het analyseren en waarschuwen van bewakingsgegevens

In de volgende secties wordt op dit artikel voortgebouwd door de specifieke gegevens die zijn verzameld vanuit Azure Cosmos DB te beschrijven en voorbeelden te geven voor het configureren van gegevensverzameling en het analyseren van deze gegevens met Azure-hulpprogramma's.

## <a name="azure-monitor-for-cosmos-db-preview"></a>Azure-monitor voor Cosmos DB (voorbeeld)
[Azure Monitor voor Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md) is gebaseerd op de [werkmappenfunctie van Azure Monitor](../azure-monitor/app/usage-workbooks.md) en maakt gebruik van dezelfde bewakingsgegevens die zijn verzameld voor Cosmos DB die in de onderstaande secties worden beschreven. Gebruik deze tool voor een overzicht van de algehele prestaties, fouten, capaciteit en operationele status van al uw Azure Cosmos DB-resources in een uniforme interactieve ervaring en maak gebruik van de andere functies van Azure Monitor voor gedetailleerde analyse en waarschuwingen. 

![Azure-monitor voor Cosmos DB](media/monitor-cosmos-db/azure-monitor-cosmos-db.png)

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Statistieken over bewerkingsniveau weergeven voor Azure Cosmos DB

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).

1. Selecteer **Monitor op** de navigatiebalk aan de linkerkant en selecteer **Statistieken**.

   ![Deelvenster Metrische gegevens in Azure Monitor](./media/monitor-cosmos-db/monitor-metrics-blade.png)

1. Selecteer in het deelvenster **Statistieken** > **Selecteer een resource** > kies het vereiste **abonnement**en **de resourcegroep**. Selecteer **voor**het type Resource **Azure Cosmos DB-accounts**een van uw bestaande Azure Cosmos-accounts en selecteer **Toepassen**.

   ![Kies een Cosmos DB-account om statistieken weer te geven](./media/monitor-cosmos-db/select-cosmosdb-account.png)

1. Vervolgens u een statistiek selecteren in de lijst met beschikbare statistieken. U statistieken selecteren die specifiek zijn voor aanvragen, opslag, latentie, beschikbaarheid, Cassandra en anderen. Zie het artikel [Statistieken per rubriek](monitor-cosmos-db-reference.md) voor meer informatie over alle beschikbare statistieken in deze lijst. In dit voorbeeld selecteren we **Eenheden aanvragen** en **Avg** als aggregatiewaarde.

   Naast deze details u ook de **granulariteit** **tijdenen en** tijd van de statistieken selecteren. Bij max u statistieken van de afgelopen 30 dagen bekijken.  Nadat u het filter hebt toegepast, wordt een grafiek weergegeven op basis van uw filter. U het gemiddelde aantal verbruikte aanvraageenheden per minuut voor de geselecteerde periode bekijken.  

   ![Een statistiek kiezen uit de Azure-portal](./media/monitor-cosmos-db/metric-types.png)

### <a name="add-filters-to-metrics"></a>Filters toevoegen aan statistieken

U ook statistieken en de grafiek filteren die wordt weergegeven door een specifieke **CollectionName**, **DatabaseName,** **OperationType**, **Regio**en **StatusCode**. Als u de statistieken wilt filteren, selecteert u **Filter toevoegen** en kiest u de vereiste eigenschap zoals **OperationType** en selecteert u een waarde zoals **Query**. In de grafiek worden vervolgens de aanvraageenheden weergegeven die zijn verbruikt voor de querybewerking voor de geselecteerde periode. De bewerkingen die via de procedure Opgeslagen worden uitgevoerd, worden niet geregistreerd, zodat ze niet beschikbaar zijn onder de metriek OperationType.

![Een filter toevoegen om de metrische granulariteit te selecteren](./media/monitor-cosmos-db/add-metrics-filter.png)

U statistieken groeperen met de optie **Splitsen toepassen.** U bijvoorbeeld de aanvraageenheden per bewerkingstype groeperen en de grafiek voor alle bewerkingen tegelijk bekijken, zoals in de volgende afbeelding wordt weergegeven:

![Add apply splitting filter Add apply splitting filter Add apply splitting filter Add apply](./media/monitor-cosmos-db/apply-metrics-splitting.png)

Hier is nog een voorbeeld om de latentiestatistieken aan de serverzijde voor een specifieke database, container of een bewerking weer te geven:

![Latentiestatistieken aan serverzijde](./media/monitor-cosmos-db/serverside-latency-metric.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Monitoring gegevens verzameld van Azure Cosmos DB

Azure Cosmos DB verzamelt dezelfde soorten bewakingsgegevens als andere Azure-resources die worden beschreven in [Monitoringgegevens van Azure-bronnen.](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data) Zie [azure cosmos DB-gegevensreferentie](monitor-cosmos-db-reference.md) voor een gedetailleerde referentie van de logboeken en statistieken die zijn gemaakt door Azure Cosmos DB.

De **overzichtspagina** in de Azure-portal voor elke Azure Cosmos-database bevat een korte weergave van het databasegebruik, inclusief de aanvraag en het factureringsgebruik per uur. Dit is nuttige informatie, maar slechts een kleine hoeveelheid van de beschikbare monitoringgegevens. Sommige van deze gegevens worden automatisch verzameld en beschikbaar voor analyse zodra u de database maakt, terwijl u extra gegevensverzameling met een bepaalde configuratie inschakelen.

![Overzichtspagina](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>Metrische gegevens analyseren

Azure Cosmos DB biedt een aangepaste ervaring voor het werken met statistieken. Zie [Azure Cosmos DB-statistieken van Azure Monitor controleren en debuggen](cosmos-db-azure-monitor-metrics.md) voor meer informatie over het gebruik van deze ervaring en voor het analyseren van verschillende Azure Cosmos DB-scenario's.

U statistieken voor Azure Cosmos DB analyseren met statistieken uit andere Azure-services met behulp van Metrics explorer door **Statistieken** te openen in het menu **Azure Monitor.** Zie [Aan de slag met Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md) voor meer informatie over het gebruik van deze tool. Alle statistieken voor Azure Cosmos DB staan in de standaardstatistieken van namespace **Cosmos DB.** U de volgende dimensies met deze statistieken gebruiken wanneer u een filter aan een grafiek toevoegt:

- CollectionName
- DatabaseName
- OperationType
- Regio
- Statuscode


## <a name="analyzing-log-data"></a>Loggegevens analyseren
Gegevens in Azure Monitor-logboeken worden opgeslagen in tabellen die elke tabel zijn eigen set unieke eigenschappen heeft. Azure Cosmos DB slaat gegevens op in de volgende tabellen.

| Tabel | Beschrijving |
|:---|:---|
| AzureDiagnostics | Algemene tabel die door meerdere services wordt gebruikt om bronlogboeken op te slaan. Bronlogboeken van Azure Cosmos DB `MICROSOFT.DOCUMENTDB`kunnen worden geïdentificeerd met .   |
| AzureActivity    | Algemene tabel die alle records opslaat in het logboek Activiteit. 


> [!IMPORTANT]
> Wanneer u **Logboeken** selecteert in het menu Azure Cosmos DB, wordt Log Analytics geopend met de queryscope die is ingesteld op de huidige Azure Cosmos-database. Dit betekent dat logboekquery's alleen gegevens uit die bron bevatten. Als u een query wilt uitvoeren die gegevens uit andere databases of gegevens uit andere Azure-services bevat, selecteert u **Logboeken** in het menu **Azure Monitor.** Zie [Querybereik en -tijdsbereik logboeken in Azure Monitor Log Analytics](../azure-monitor/log-query/scope.md) voor meer informatie.

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Azure Cosmos DB Log Analytics-query's in Azure Monitor

Hier volgen enkele query's die u invoeren in de zoekbalk **Logboek** om u te helpen uw Azure Cosmos-containers te controleren. Deze query's werken met de [nieuwe taal](../log-analytics/log-analytics-log-search-upgrade.md).

Hieronder volgen query's die u gebruiken om uw Azure Cosmos-databases te controleren.

* Ga als volgende voor alle diagnostische logboeken van Azure Cosmos DB voor een bepaalde periode:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"

    ```

* Ga als het gaat om een query voor de 10 meest recent geregistreerde gebeurtenissen:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | limit 10
    ```

* Als u voor alle bewerkingen wilt zoeken, gegroepeerd op bewerkingstype:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName
    ```

* Als u wilt zoeken naar alle bewerkingen, gegroepeerd op resource:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* U wilt zoeken naar alle gebruikersactiviteiten, gegroepeerd op resource:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```
* Als u alle query's van meer dan 100 NL wilt ophalen, worden samengevoegd met gegevens van **DataPlaneRequests** en **QueryRunTimeStatistics**.

    ```Kusto
    AzureDiagnostics
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 100.0
    | project activityId_g, requestCharge_s
    | join kind= inner (
           AzureDiagnostics
           | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
           | project activityId_g, querytext_s
    ) on $left.activityId_g == $right.activityId_g
    | order by requestCharge_s desc
    | limit 100
    ```

* Ga als u wilt opvragen voor welke bewerkingen langer dan 3 milliseconden duren:

    ```Kusto
    AzureDiagnostics 
    | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Ga als een query uit voor welke agent de bewerkingen uitvoert:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName, userAgent_s
    ```

* Ga als een query uit voor wanneer de langlopende bewerkingen zijn uitgevoerd:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | project TimeGenerated , duration_s 
    | summarize count() by bin(TimeGenerated, 5s)
    | render timechart
    ```
    
* Ga als u de statistieken van Partitiesleutel wilt gebruiken om scheeftrekking en top 3-partities voor databaseaccount te evalueren:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a>Azure Cosmos DB programmatisch controleren
De statistieken op accountniveau die beschikbaar zijn in de portal, zoals het gebruik van accountopslag en het totale aantal aanvragen, zijn niet beschikbaar via de SQL-API's. U echter gebruiksgegevens op verzamelingsniveau ophalen met behulp van de SQL-API's. Ga als volgt te werk om gegevens op het verzamelingsniveau op te halen:

* Als u de REST-API wilt [gebruiken, voert u een GET uit op de verzameling](https://msdn.microsoft.com/library/mt489073.aspx). De quotum- en gebruiksgegevens voor de verzameling worden geretourneerd in de headers x-ms-resource-quota en x-ms-resource-gebruik in het antwoord.
* Als u de .NET SDK wilt gebruiken, gebruikt u de methode [DocumentClient.ReadDocumentCollectionAsync,](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) waarmee een [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) wordt geretourneerd die een aantal gebruikseigenschappen bevat, zoals **CollectionSizeUsage**, **DatabaseUsage,** **DocumentUsage**en meer.

Als u toegang wilt krijgen tot aanvullende statistieken, gebruikt u de [Azure Monitor SDK](https://www.nuget.org/packages/Microsoft.Azure.Insights). Beschikbare metrische definities kunnen worden opgehaald door aan te roepen:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Query's om afzonderlijke statistieken op te halen, gebruiken de volgende indeling:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z



## <a name="next-steps"></a>Volgende stappen

- Zie [Azure Cosmos DB-monitoringgegevensreferentie](monitor-cosmos-db-reference.md) voor een referentie van de logboeken en statistieken die zijn gemaakt door Azure Cosmos DB.
- Zie [Azure-resources bewaken met Azure Monitor](../azure-monitor/insights/monitor-azure-resource.md) voor meer informatie over het bewaken van Azure-resources.
