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
ms.openlocfilehash: 9a36b46d11657ef52051f8bf8df1e4944051da23
ms.sourcegitcommit: 12d902e78d6617f7e78c062bd9d47564b5ff2208
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/24/2019
ms.locfileid: "74454273"
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

## <a name="monitoring-data-collected-from-azure-cosmos-db"></a>Bewakings gegevens die zijn verzameld van Azure Cosmos DB
Azure Cosmos DB worden dezelfde soorten bewakings gegevens verzameld als andere Azure-resources die worden beschreven in [gegevens van Azure-resources bewaken](../azure-monitor/insights/monitor-azure-resource.md#monitoring-data). Zie [Azure Cosmos DB monitoring data Naslag informatie](monitor-cosmos-db-reference.md) voor een gedetailleerde Naslag informatie over de logboeken en metrische gegevens die door Azure Cosmos DB zijn gemaakt.

De **overzichts** pagina in de Azure portal voor elke Azure Cosmos-Data Base bevat een korte weer gave van het database gebruik, inclusief de aanvraag en het facturerings gebruik per uur. Dit is nuttige informatie, maar slechts een kleine hoeveelheid beschik bare bewakings gegevens. Sommige van deze gegevens worden automatisch verzameld en beschikbaar voor analyse zodra u de data base maakt terwijl u extra gegevens verzameling kunt inschakelen met een bepaalde configuratie.

![Overzichts pagina](media/monitor-cosmos-db/overview-page.png)



## <a name="diagnostic-settings"></a>Diagnostische instellingen
De metrische gegevens van het platform en het activiteiten logboek worden automatisch verzameld, maar u moet een diagnostische instelling maken om bron logboeken te verzamelen of deze buiten Azure Monitor door te sturen. Zie [Diagnostische instelling maken voor het verzamelen van platform logboeken en metrische gegevens in azure](../azure-monitor/platform/diagnostic-settings.md) voor het gedetailleerde proces voor het maken van een diagnostische instelling met behulp van de Azure Portal, CLI of Power shell.

Wanneer u een diagnostische instelling maakt, geeft u op welke categorieën logboeken u wilt verzamelen. De categorieën voor Azure Cosmos DB worden hieronder weer gegeven, samen met voorbeeld gegevens.

 * **DataPlaneRequests**: Selecteer deze optie als u wilt dat de back-end-aanvragen worden geregistreerd bij alle api's, waaronder SQL-, Graph-, MongoDb-, Cassandra-en Table-API-accounts in azure Cosmos db. De belangrijkste eigenschappen die u moet weten, zijn: Requestcharge, status code, clientIPaddress en partitionID.

    ```
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**: Selecteer deze optie om door de gebruiker geïnitieerde aanvragen van de front-end te registreren om aanvragen voor de Azure Cosmos DB-API voor MongoDb te leveren. MongoDB-aanvragen worden weer gegeven in MongoRequests en DataPlaneRequests. De belangrijkste eigenschappen die u moet weten, zijn: Requestcharge, opcode.

    ```
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **QueryRuntimeStatistics**: Selecteer deze optie om de uitgevoerde query tekst te registreren. 

    ```
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: Selecteer deze optie om de statistieken van de partitie sleutels te registreren. Dit wordt momenteel weer gegeven met de opslag grootte (KB) van de partitie sleutels. Het logboek wordt verzonden op basis van de eerste drie partitie sleutels die de meeste gegevens opslag in beslag nemen.

    ```
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **Metrische aanvragen**: Selecteer deze optie als u metrische gegevens wilt verzamelen van Azure Cosmos DB op de doelen in de diagnostische instelling. Dit zijn dezelfde gegevens die automatisch worden verzameld in azure Metrics. Verzamelen van metrische gegevens met resource logboeken voor het analyseren van beide soorten gegevens en het verzenden van metrische gegevens buiten Azure Monitor.

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
