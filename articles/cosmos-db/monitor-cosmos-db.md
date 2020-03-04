---
title: Bewakings Azure Cosmos DB | Microsoft Docs
description: Meer informatie over het bewaken van de prestaties en beschik baarheid van Azure Cosmos DB.
author: bwren
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/11/2019
ms.author: bwren
ms.custom: subject-monitoring
ms.openlocfilehash: b9b66c379714c2f4fa2421876fda3bdb500ce6c1
ms.sourcegitcommit: e4c33439642cf05682af7f28db1dbdb5cf273cc6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78250426"
---
# <a name="monitoring-azure-cosmos-db"></a>Bewakings Azure Cosmos DB
Wanneer u belang rijke toepassingen en bedrijfs processen hebt die afhankelijk zijn van Azure-resources, wilt u deze resources controleren op hun Beschik baarheid, prestaties en werking. In dit artikel worden de bewakings gegevens beschreven die worden gegenereerd door Azure Cosmos-data bases en hoe u de functies van Azure Monitor kunt gebruiken om deze gegevens te analyseren en te waarschuwen.

## <a name="what-is-azure-monitor"></a>Wat is Azure Monitor?
Azure Cosmos DB maakt bewakings gegevens met behulp van [Azure monitor](../azure-monitor/overview.md) . Dit is een volledige stack monitoring-service in azure die een volledige set functies biedt om uw Azure-resources te controleren naast bronnen in andere Clouds en on-premises. 

Als u nog niet bekend bent met het bewaken van Azure-Services, kunt u beginnen met het artikel [Azure-resources controleren met Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) waarin het volgende wordt beschreven:

- Wat is Azure Monitor?
- Kosten die zijn gekoppeld aan bewaking
- Bewaken van gegevens die zijn verzameld in azure
- Gegevens verzameling configureren
- Standaard Programma's in azure voor het analyseren en waarschuwen van bewakings gegevens

In de volgende secties vindt u een beschrijving van de specifieke gegevens die zijn verzameld uit Azure Cosmos DB en vindt u voor beelden voor het configureren van gegevens verzameling en het analyseren van deze gegevens met Azure-hulpprogram ma's.

## <a name="azure-monitor-for-cosmos-db-preview"></a>Azure Monitor voor Cosmos DB (preview-versie)
[Azure monitor voor Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md) is gebaseerd op de [werkmappen-functie van Azure monitor](../azure-monitor/app/usage-workbooks.md) en gebruikt dezelfde bewakings gegevens die zijn verzameld voor Cosmos DB beschreven in de volgende secties. Gebruik dit hulp programma voor een overzicht van de algehele prestaties, fouten, capaciteit en operationele status van al uw Azure Cosmos DB resources in een geïntegreerde interactieve ervaring, en gebruik de andere functies van Azure Monitor voor gedetailleerde analyse en waarschuwingen. 

![Azure Monitor voor Cosmos DB](media/monitor-cosmos-db/azure-monitor-cosmos-db.png)

## <a name="view-operation-level-metrics-for-azure-cosmos-db"></a>Metrische gegevens van het bewerkings niveau voor Azure Cosmos DB weer geven

1. Meld u aan bij de [Azure-portal](https://portal.azure.com/).

1. Selecteer **monitor** in de navigatie balk aan de linkerkant en selecteer **metrische gegevens**.

   ![Deel venster metrische gegevens in Azure Monitor](./media/monitor-cosmos-db/monitor-metrics-blade.png)

1. Selecteer in het deel venster **metrieken** > **een resource selecteren** > Kies het vereiste **abonnement**en de **resource groep**. Voor het **bron type**selecteert u **Azure Cosmos DB accounts**, kiest u een van uw bestaande Azure Cosmos-accounts en selecteert u **Toep assen**.

   ![Kies een Cosmos DB account om de metrische gegevens weer te geven](./media/monitor-cosmos-db/select-cosmosdb-account.png)

1. Vervolgens kunt u een metriek selecteren in de lijst met beschik bare metrische gegevens. U kunt metrische gegevens selecteren die specifiek zijn voor aanvraag eenheden, opslag, latentie, Beschik baarheid, Cassandra en andere. Zie het artikel [metrische gegevens per categorie](monitor-cosmos-db-reference.md) voor meer informatie over alle beschik bare metrische gegevens in deze lijst. In dit voor beeld selecteren we **aanvraag eenheden** en **Gem** als de aggregatie waarde.

   Naast deze details kunt u ook het **tijds bereik** en de **tijd granulatie** van de metrische gegevens selecteren. U kunt Maxi maal de metrische gegevens weer geven voor de afgelopen 30 dagen.  Nadat u het filter hebt toegepast, wordt een grafiek weer gegeven op basis van het filter. U kunt het gemiddelde aantal verbruikte aanvraag eenheden per minuut voor de geselecteerde periode bekijken.  

   ![Kies een waarde in het Azure Portal](./media/monitor-cosmos-db/metric-types.png)

### <a name="add-filters-to-metrics"></a>Filters toevoegen aan metrische gegevens

U kunt ook de metrische gegevens en de grafiek die worden weer gegeven met een specifieke **verzamelingnaam**, **DATABASENAME**, **OperationType**, **Region**en **status**code filteren. Als u de metrische gegevens wilt filteren, selecteert u **filter toevoegen** en kiest u de vereiste eigenschap, zoals **OperationType** , en selecteert u een waarde zoals **query**. In de grafiek worden vervolgens de verbruikte aanvraag eenheden voor de query bewerking voor de geselecteerde periode weer gegeven. De bewerkingen die zijn uitgevoerd via een opgeslagen procedure, worden niet geregistreerd, zodat ze niet beschikbaar zijn onder de metrische waarde voor OperationType.

![Een filter toevoegen om de metrische granulatie te selecteren](./media/monitor-cosmos-db/add-metrics-filter.png)

U kunt metrische gegevens groeperen met behulp van de optie **splitsing Toep assen** . U kunt bijvoorbeeld de aanvraag eenheden per bewerkings type groeperen en de grafiek voor alle bewerkingen tegelijk bekijken, zoals wordt weer gegeven in de volgende afbeelding:

![Splitsings filter Toep assen toevoegen](./media/monitor-cosmos-db/apply-metrics-splitting.png)

Hier volgt nog een voor beeld van het weer geven van de latentie gegevens aan de server zijde voor een specifieke data base, container of bewerking:

![Metrische latentie gegevens aan de server zijde](./media/monitor-cosmos-db/serverside-latency-metric.png)

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Bewakings gegevens die zijn verzameld van Azure Cosmos DB

Azure Cosmos DB worden dezelfde soorten bewakings gegevens verzameld als andere Azure-resources die worden beschreven in [gegevens van Azure-resources bewaken](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Zie [Azure Cosmos DB monitoring data Naslag informatie](monitor-cosmos-db-reference.md) voor een gedetailleerde Naslag informatie over de logboeken en metrische gegevens die door Azure Cosmos DB zijn gemaakt.

De **overzichts** pagina in de Azure portal voor elke Azure Cosmos-Data Base bevat een korte weer gave van het database gebruik, inclusief de aanvraag en het facturerings gebruik per uur. Dit is nuttige informatie, maar slechts een kleine hoeveelheid beschik bare bewakings gegevens. Sommige van deze gegevens worden automatisch verzameld en beschikbaar voor analyse zodra u de data base maakt terwijl u extra gegevens verzameling kunt inschakelen met een bepaalde configuratie.

![Overzichts pagina](media/monitor-cosmos-db/overview-page.png)

## <a name="analyzing-metric-data"></a>Metrische gegevens analyseren

Azure Cosmos DB biedt een aangepaste ervaring voor het werken met metrische gegevens. Zie [Azure Cosmos DB metrische gegevens controleren en fouten opsporen in azure monitor](cosmos-db-azure-monitor-metrics.md) voor meer informatie over het gebruik van deze ervaring en voor het analyseren van verschillende Azure Cosmos DB scenario's.

U kunt metrische gegevens voor Azure Cosmos DB met metrische gegevens uit andere Azure-Services analyseren door **metrische gegevens** te openen in het menu **Azure monitor** . Zie [aan de slag met Azure Metrics Explorer](../azure-monitor/platform/metrics-getting-started.md) voor meer informatie over het gebruik van dit hulp programma. Alle metrische gegevens voor Azure Cosmos DB bevinden zich in de naam ruimte **Cosmos DB standaard metrische gegevens**. U kunt de volgende dimensies met deze metrische gegevens gebruiken bij het toevoegen van een filter aan een grafiek:

- collectionName
- DatabaseName
- OperationType
- Regio
- Status code


## <a name="analyzing-log-data"></a>Logboek gegevens analyseren
Gegevens in Azure Monitor logboeken worden opgeslagen in tabellen waarvan elke tabel een eigen set unieke eigenschappen heeft. Azure Cosmos DB slaat gegevens op in de volgende tabellen.

| Tabel | Beschrijving |
|:---|:---|
| AzureDiagnostics | Algemene tabel die door meerdere services wordt gebruikt voor het opslaan van resource Logboeken. Bron logboeken van Azure Cosmos DB kunnen worden geïdentificeerd met `MICROSOFT.DOCUMENTDB`.   |
| AzureActivity    | Algemene tabel waarin alle records uit het activiteiten logboek worden opgeslagen. 


> [!IMPORTANT]
> Wanneer u **Logboeken** in het Azure Cosmos DB menu selecteert, wordt log Analytics geopend met het query bereik dat is ingesteld op de huidige Azure Cosmos-data base. Dit betekent dat logboek query's alleen gegevens van die bron bevatten. Als u een query wilt uitvoeren die gegevens uit andere data bases of gegevens uit andere Azure-Services bevat, selecteert u **Logboeken** in het **Azure monitor** menu. Zie de [logboek query bereik en het tijds bereik in Azure Monitor Log Analytics](../azure-monitor/log-query/scope.md) voor meer informatie.

### <a name="azure-cosmos-db-log-analytics-queries-in-azure-monitor"></a>Azure Cosmos DB Log Analytics query's in Azure Monitor

Hier volgen enkele query's die u kunt invoeren in de zoek balk Zoeken naar **Logboeken** om uw Azure Cosmos-containers te bewaken. Deze query's werken met de [nieuwe taal](../log-analytics/log-analytics-log-search-upgrade.md).

Hieronder vindt u query's die u kunt gebruiken om uw Azure Cosmos-data bases te bewaken.

* Zoeken naar alle diagnostische logboeken van Azure Cosmos DB voor een opgegeven periode:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"

    ```

* Als u wilt zoeken naar de 10 meest recent geregistreerde gebeurtenissen:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | limit 10
    ```

* Query uitvoeren voor alle bewerkingen, gegroepeerd op bewerkingstype:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName
    ```

* Om te zoeken naar alle bewerkingen, gegroepeerd op resource:

    ```Kusto
    AzureActivity 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource

    ```

* Zoeken naar alle gebruikersactiviteiten, gegroepeerd op resource:

    ```Kusto
    AzureActivity 
    | where Caller == "test@company.com" and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by Resource
    ```
* Als u alle query's wilt ophalen die groter zijn dan 100 RUs, gekoppeld aan gegevens uit **DataPlaneRequests** en **QueryRunTimeStatistics**.

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

* Query uitvoeren op waarvoor operations duurt langer dan 3 milliseconden:

    ```Kusto
    AzureDiagnostics 
    | where toint(duration_s) > 3 and ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by clientIpAddress_s, TimeGenerated
    ```

* Query uitvoeren voor welke agent de bewerkingen wordt uitgevoerd:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | summarize count() by OperationName, userAgent_s
    ```

* Zoeken naar wanneer de langlopende bewerkingen zijn uitgevoerd:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" 
    | project TimeGenerated , duration_s 
    | summarize count() by bin(TimeGenerated, 5s)
    | render timechart
    ```
    
* Voor het ophalen van partitie sleutel statistieken voor het evalueren van schuin trekking over de belangrijkste drie partities voor het database account:

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="monitor-azure-cosmos-db-programmatically"></a>Azure Cosmos DB controleren via een programma
De account metingen op het niveau beschikbaar in de portal, zoals account opslag gebruik en totaal aantal aanvragen, zijn niet beschikbaar via de SQL-API's. U kunt echter gebruiksgegevens op het niveau verzameling ophalen met behulp van de SQL-API's. Om op te halen op gegevens te verzamelen, het volgende doen:

* Als u de REST API wilt gebruiken, moet u [een Get-bewerking uitvoeren op de verzameling](https://msdn.microsoft.com/library/mt489073.aspx). De quota en het gebruik de informatie voor de verzameling wordt in de x-ms-resource-quota en x-ms-resource-usage-headers in het antwoord geretourneerd.
* Als u de .NET SDK wilt gebruiken, gebruikt u de methode [DocumentClient. ReadDocumentCollectionAsync](https://msdn.microsoft.com/library/microsoft.azure.documents.client.documentclient.readdocumentcollectionasync.aspx) , die een [ResourceResponse](https://msdn.microsoft.com/library/dn799209.aspx) retourneert dat een aantal gebruiks eigenschappen bevat, zoals **CollectionSizeUsage**, **DatabaseUsage**, **DocumentUsage**en meer.

Gebruik de [SDK van Azure monitor](https://www.nuget.org/packages/Microsoft.Azure.Insights)om toegang te krijgen tot extra metrische gegevens. Beschikbare metrische definities kunnen worden opgehaald door aan te roepen:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metricDefinitions?api-version=2015-04-08

Query's om op te halen van afzonderlijke metrische gegevens over gebruik de volgende indeling:

    https://management.azure.com/subscriptions/{SubscriptionId}/resourceGroups/{ResourceGroup}/providers/Microsoft.DocumentDb/databaseAccounts/{DocumentDBAccountName}/metrics?api-version=2015-04-08&$filter=%28name.value%20eq%20%27Total%20Requests%27%29%20and%20timeGrain%20eq%20duration%27PT5M%27%20and%20startTime%20eq%202016-06-03T03%3A26%3A00.0000000Z%20and%20endTime%20eq%202016-06-10T03%3A26%3A00.0000000Z



## <a name="next-steps"></a>Volgende stappen

- Zie [Azure Cosmos DB Naslag informatie over bewakings gegevens](monitor-cosmos-db-reference.md) voor een verwijzing naar de logboeken en metrieken die zijn gemaakt door Azure Cosmos db.
- Zie [Azure-resources bewaken met Azure monitor](../azure-monitor/insights/monitor-azure-resource.md) voor meer informatie over het bewaken van Azure-resources.
