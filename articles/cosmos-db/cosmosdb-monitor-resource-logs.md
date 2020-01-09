---
title: Azure Cosmos DB gegevens bewaken met behulp van de diagnostische instellingen van Azure
description: Meer informatie over het gebruik van diagnostische instellingen van Azure voor het bewaken van de prestaties en beschik baarheid van gegevens die zijn opgeslagen in Azure Cosmos DB
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: sngun
ms.openlocfilehash: 670797eb833b0a145a18e20c6bba711ca11609bc
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75483282"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Azure Cosmos DB gegevens bewaken met behulp van diagnostische instellingen in azure

Diagnostische instellingen in Azure worden gebruikt om bron logboeken te verzamelen. Azure-resource logboeken worden verzonden door een resource en bieden uitgebreide, frequente gegevens over de werking van die resource. Deze logboeken worden vastgelegd per aanvraag en ze worden ook wel ' gegevens vlak logboeken ' genoemd. Enkele voor beelden van gegevens vlak bewerkingen zijn Delete, INSERT en readFeed. Resourcetype is afhankelijk van de inhoud van deze logboeken.

Metrische platform gegevens en de activiteiten logboeken worden automatisch verzameld, terwijl u een diagnostische instelling moet maken om bron logboeken te verzamelen of deze buiten Azure Monitor door te sturen. U kunt de diagnostische instelling voor Azure Cosmos-accounts inschakelen met behulp van de volgende stappen:

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

1. Navigeer naar uw Azure Cosmos-account. Open het deel venster **Diagnostische instellingen** en selecteer optie **Diagnostische instelling toevoegen** .

1. In het deel venster **Diagnostische instellingen** vult u het formulier in met de volgende details: 

    * **Naam**: Voer een naam voor de logboeken om te maken.

    * U kunt de logboeken opslaan om te **archiveren naar een opslag account**, **naar een event hub streamen** of **verzenden naar log Analytics**

1. Wanneer u een diagnostische instelling maakt, geeft u op welke logboek categorie u wilt verzamelen. De categorieën logboeken die door Azure Cosmos DB worden ondersteund, worden hieronder weer gegeven, samen met het voorbeeld logboek dat door hen wordt verzameld:

 * **DataPlaneRequests**: Selecteer deze optie om back-end-aanvragen te registreren voor alle api's, waaronder SQL-, Graph-, MongoDb-, Cassandra-en Table-API-accounts in azure Cosmos db. De belangrijkste eigenschappen die u moet weten, zijn: `Requestcharge`, `statusCode`, `clientIPaddress`en `partitionID`.

    ```
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**: Selecteer deze optie als u door de gebruiker geïnitieerde aanvragen van de front-end wilt registreren om de api van Azure Cosmos DB voor MongoDb te verwerken. dit logboek type is niet beschikbaar voor andere API-accounts. MongoDB-aanvragen worden weer gegeven in MongoRequests en DataPlaneRequests. De belangrijkste eigenschappen die u moet weten, zijn: `Requestcharge`, `opCode`.

    ```
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **QueryRuntimeStatistics**: Selecteer deze optie om de uitgevoerde query tekst te registreren. Dit logboek type is alleen beschikbaar voor SQL-API-accounts.

    ```
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: Selecteer deze optie om de statistieken van de partitie sleutels te registreren. Dit wordt momenteel weer gegeven met de opslag grootte (KB) van de partitie sleutels. Zie de sectie [problemen oplossen met behulp van Azure diagnostische query's](#diagnostic-queries) in dit artikel. Bijvoorbeeld query's die gebruikmaken van ' PartitionKeyStatistics '. Het logboek wordt verzonden op basis van de eerste drie partitie sleutels die de meeste gegevens opslag in beslag nemen. Dit logboek bevat gegevens, zoals de abonnements-ID, de naam van de regio, de database naam, de naam van de verzameling, de partitie sleutel en de opslag grootte in KB.

    ```
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **PartitionKeyRUConsumption**: in dit logboek wordt het geaggregeerde aantal exemplaren per seconde van partitie sleutels gerapporteerd. Op dit moment rapporteert Azure Cosmos DB alleen partitie sleutels voor SQL API-accounts en voor bewerkingen voor lezen/schrijven en opgeslagen procedures. andere Api's en bewerkings typen worden niet ondersteund. Voor andere Api's is de partitie sleutel kolom in de tabel met Diagnostische logboeken leeg. Dit logboek bevat gegevens zoals de abonnements-ID, de regio naam, de database naam, de naam van de verzameling, de partitie sleutel, het bewerkings type en de kosten voor aanvragen. Zie de sectie [problemen oplossen met behulp van Azure diagnostische query's](#diagnostic-queries) in dit artikel. Bijvoorbeeld query's die gebruikmaken van ' PartitionKeyRUConsumption '. 

* **ControlPlaneRequests**: dit logboek bevat details over besturings vlak bewerkingen zoals het maken van een account, het toevoegen of verwijderen van een regio, het bijwerken van de replicatie-instellingen voor het account, enzovoort. Dit logboek type is beschikbaar voor alle API-typen die SQL (core), MongoDB, Gremlin, Cassandra, Table-API, bevatten.

* **Aanvragen**: Selecteer deze optie als u metrische gegevens wilt verzamelen van Azure Cosmos DB op de doelen in de diagnostische instelling. Dit zijn dezelfde gegevens die automatisch worden verzameld in azure Metrics. Verzamelen van metrische gegevens met resource logboeken voor het analyseren van beide soorten gegevens en het verzenden van metrische gegevens buiten Azure Monitor.

Zie voor gedetailleerde informatie over het maken van een diagnostische instelling met behulp van de Azure Portal, CLI of Power shell de [Diagnostische instelling maken voor het verzamelen van platform logboeken en metrische gegevens in azure](../azure-monitor/platform/diagnostic-settings.md) article.


## <a id="diagnostic-queries"></a>Problemen met diagnostische query's oplossen

1. Hoe kunt u de aanvraag kosten voor dure query's ophalen?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests" and todouble(requestCharge_s) > 10.0
   | project activityId_g, requestCharge_s
   | join kind= inner (
   AzureDiagnostics
   | where ResourceProvider =="MICROSOFT.DOCUMENTDB" and Category == "QueryRuntimeStatistics"
   | project activityId_g, querytext_s
   ) on $left.activityId_g == $right.activityId_g
   | order by requestCharge_s desc
   | limit 100
   ```

1. Hoe kunt u vinden welke bewerkingen het meren deel zijn van RU/s?

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
1. Hoe kan ik de distributie voor verschillende bewerkingen verkrijgen?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. Wat is de maximale door Voer die een partitie biedt?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. Informatie over het gebruik van de partitie sleutels RU/s per seconde ophalen?

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. De aanvraag kosten voor een specifieke partitie sleutel ophalen

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. De belangrijkste partitie sleutels met de meeste RU/s ophalen in een specifieke periode 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. Hoe kan ik de logboeken ophalen voor de partitie sleutels waarvan de opslag ruimte groter is dan 8 GB?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

1. Hoe kan ik de statistieken van een partitie sleutel ophalen om het hellen van de drie partities voor het database account te evalueren?

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="next-steps"></a>Volgende stappen

* [Azure Monitor voor Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [Controleren en fouten opsporen met metrische gegevens in Azure Cosmos DB](use-metrics.md)
