---
title: Azure Cosmos DB-gegevens bewaken met Azure Diagnostic-instellingen
description: Meer informatie over het gebruik van Azure Diagnostic-instellingen om de prestaties en beschikbaarheid te controleren van gegevens die zijn opgeslagen in Azure Cosmos DB
author: SnehaGunda
services: cosmos-db
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: sngun
ms.openlocfilehash: 184fc65dae57292243be9abdca71a129512b3d0b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78252058"
---
# <a name="monitor-azure-cosmos-db-data-by-using-diagnostic-settings-in-azure"></a>Azure Cosmos DB-gegevens bewaken met diagnostische instellingen in Azure

Diagnostische instellingen in Azure worden gebruikt om bronlogboeken te verzamelen. Azure-bronlogboeken worden uitgestoten door een resource en bieden uitgebreide, frequente gegevens over de werking van die bron. Deze logboeken worden vastgelegd per aanvraag en ze worden ook wel aangeduid als "data plane logs". Enkele voorbeelden van de bewerkingen van het gegevensvlak zijn delete, insert en readFeed. De inhoud van deze logboeken verschilt per resourcetype.

Platformstatistieken en de activiteitslogboeken worden automatisch verzameld, terwijl u een diagnostische instelling moet maken om bronlogboeken te verzamelen of door te sturen buiten Azure Monitor. U diagnostische instellingen voor Azure Cosmos-accounts inschakelen met de volgende stappen:

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Navigeer naar uw Azure Cosmos-account. Open het deelvenster **Diagnostische instellingen** en selecteer vervolgens de optie Diagnostische **instelling toevoegen.**

1. Vul in het deelvenster **Diagnostische instellingen** het formulier in met de volgende details: 

    * **Naam:** Voer een naam in voor de logboeken die u wilt maken.

    * U de logboeken opslaan in **Archiveren naar een opslagaccount,** **Streamen naar een gebeurtenishub** of **Verzenden naar logboekanalyse**

1. Wanneer u een diagnostische instelling maakt, geeft u op welke categorie logboeken u wilt verzamelen. De categorieën logboeken die worden ondersteund door Azure Cosmos DB worden hieronder weergegeven, samen met voorbeeldlogboek dat door hen wordt verzameld:

 * **DataPlaneRequests**: Selecteer deze optie om back-endaanvragen in te loggen op alle API's, waaronder SQL-, Grafiek-, MongoDB-, Cassandra- en TabelAPI-accounts in Azure Cosmos DB. Belangrijke eigenschappen die `Requestcharge`moeten `statusCode` `clientIPaddress`worden `partitionID`opgemerkt zijn: , , en .

    ```
    { "time": "2019-04-23T23:12:52.3814846Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "DataPlaneRequests", "operationName": "ReadFeed", "properties": {"activityId": "66a0c647-af38-4b8d-a92a-c48a805d6460","requestResourceType": "Database","requestResourceId": "","collectionRid": "","statusCode": "200","duration": "0","userAgent": "Microsoft.Azure.Documents.Common/2.2.0.0","clientIpAddress": "10.0.0.24","requestCharge": "1.000000","requestLength": "0","responseLength": "372","resourceTokenUserRid": "","region": "East US","partitionId": "062abe3e-de63-4aa5-b9de-4a77119c59f8","keyType": "PrimaryReadOnlyMasterKey","databaseName": "","collectionName": ""}}
    ```

* **MongoRequests**: Selecteer deze optie om door gebruikers geïnitieerde aanvragen van de front-end in te loggen om aanvragen voor de API van Azure Cosmos DB voor MongoDB te serveren, dit logboektype is niet beschikbaar voor andere API-accounts. MongoDB-aanvragen worden weergegeven in MongoRequests en DataPlaneRequests. Belangrijke eigenschappen om `Requestcharge`op `opCode`te merken zijn: , .

    ```
    { "time": "2019-04-10T15:10:46.7820998Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "MongoRequests", "operationName": "ping", "properties": {"activityId": "823cae64-0000-0000-0000-000000000000","opCode": "MongoOpCode_OP_QUERY","errorCode": "0","duration": "0","requestCharge": "0.000000","databaseName": "admin","collectionName": "$cmd","retryCount": "0"}}
    ```

* **QueryRuntimeStatistics**: Selecteer deze optie om de querytekst te registreren die is uitgevoerd. Dit logboektype is alleen beschikbaar voor SQL API-accounts.

    ```
    { "time": "2019-04-14T19:08:11.6353239Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "QueryRuntimeStatistics", "properties": {"activityId": "278b0661-7452-4df3-b992-8aa0864142cf","databasename": "Tasks","collectionname": "Items","partitionkeyrangeid": "0","querytext": "{"query":"SELECT *\nFROM c\nWHERE (c.p1__10 != true)","parameters":[]}"}}
    ```

* **PartitionKeyStatistics**: Selecteer deze optie om de statistieken van de partitiesleutels te registreren. Dit wordt momenteel weergegeven met de opslaggrootte (KB) van de partitiesleutels. Zie de problemen oplossen met de sectie [Diagnostische query's](#diagnostic-queries) van Azure in dit artikel. Bijvoorbeeld query's met 'PartitionKeyStatistics'. Het logboek wordt uitgezonden tegen de eerste drie partitiesleutels die de meeste gegevensopslag innemen. Dit logboek bevat gegevens zoals abonnements-ID, regionaam, databasenaam, verzamelingsnaam, partitiesleutel en opslaggrootte in KB.

    ```
    { "time": "2019-10-11T02:33:24.2018744Z", "resourceId": "/SUBSCRIPTIONS/<your_subscription_ID>/RESOURCEGROUPS/<your_resource_group>/PROVIDERS/MICROSOFT.DOCUMENTDB/DATABASEACCOUNTS/<your_database_account>", "category": "PartitionKeyStatistics", "properties": {"subscriptionId": "<your_subscription_ID>","regionName": "West US 2","databaseName": "KustoQueryResults","collectionname": "CapacityMetrics","partitionkey": "["CapacityMetricsPartition.136"]","sizeKb": "2048270"}}
    ```

* **PartitionKeyRUConsumption**: Dit logboek rapporteert het geaggregeerde RU/s-verbruik per seconde van partitiesleutels. Momenteel rapporteert Azure Cosmos DB alleen partitiesleutels voor SQL API-accounts en voor puntlees-/schrijf- en opgeslagen procedurebewerkingen. andere API's en bewerkingstypen worden niet ondersteund. Voor andere API's is de kolom partitiesleutel in de diagnostische logboektabel leeg. Dit logboek bevat gegevens zoals abonnements-ID, regionaam, databasenaam, verzamelingsnaam, partitiesleutel, bewerkingstype en aanvraagkosten. Zie de problemen oplossen met de sectie [Diagnostische query's](#diagnostic-queries) van Azure in dit artikel. Bijvoorbeeld query's met 'PartitionKeyRUConsumption'. 

* **ControlPlaneRequests:** Dit logboek bevat details over besturingsvlakbewerkingen zoals het maken van een account, het toevoegen of verwijderen van een regio, het bijwerken van accountreplicatie-instellingen enz. Dit logboektype is beschikbaar voor alle API-typen die SQL (Core), MongoDB, Gremlin, Cassandra, Table API bevatten.

* **Aanvragen:** Selecteer deze optie om metrische gegevens van Azure Cosmos DB te verzamelen naar de bestemmingen in de diagnostische instelling. Dit zijn dezelfde gegevens die automatisch worden verzameld in Azure Metrics. Verzamel metrische gegevens met bronlogboeken om beide soorten gegevens samen te analyseren en metrische gegevens buiten Azure Monitor te verzenden.

Zie [Diagnostische instelling maken om platformlogboeken en statistieken in Azure-artikel te verzamelen](../azure-monitor/platform/diagnostic-settings.md) voor gedetailleerde informatie over het maken van een diagnostische instelling met behulp van de Azure-portal, CLI of PowerShell.


## <a name="troubleshoot-issues-with-diagnostics-queries"></a><a id="diagnostic-queries"></a>Problemen met diagnostische query's oplossen

1. Hoe krijg je de aanvraagkosten voor dure query's?

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

1. Hoe te vinden welke operaties nemen de meeste van RU / s?

    ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(responseLength_s), max(requestLength_s), max(requestCharge_s), count = count() by OperationName, requestResourceType_s, userAgent_s, collectionRid_s, bin(TimeGenerated, 1h)
   ```
1. Hoe krijg je de distributie voor verschillende bewerkingen?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize count = count()  by OperationName, requestResourceType_s, bin(TimeGenerated, 1h) 
   ```

1. Wat is de maximale doorvoer die een partitie heeft verbruikt?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="DataPlaneRequests"
   | where TimeGenerated >= ago(2h) 
   | summarize max(requestCharge_s) by bin(TimeGenerated, 1h), partitionId_g
   ```

1. Hoe krijg je de informatie over de partitiesleutels RU/s-verbruik per seconde?

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s, TimeGenerated 
   | order by TimeGenerated asc 
   ```

1. Hoe krijg je de aanvraagkosten voor een specifieke partitiesleutel

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where parse_json(partitionKey_s)[0] == "2" 
   ```

1. Hoe krijg je de bovenste partitiesleutels met de meeste RU/s verbruikt in een bepaalde periode? 

   ```Kusto
   AzureDiagnostics 
   | where ResourceProvider == "MICROSOFT.DOCUMENTDB" and Category == "PartitionKeyRUConsumption" 
   | where TimeGenerated >= datetime("11/26/2019, 11:20:00.000 PM") and TimeGenerated <= datetime("11/26/2019, 11:30:00.000 PM") 
   | summarize total = sum(todouble(requestCharge_s)) by databaseName_s, collectionName_s, partitionKey_s 
   | order by total desc
    ```

1. Hoe krijg je de logboeken voor de partitiesleutels waarvan de opslaggrootte groter is dan 8 GB?

   ```Kusto
   AzureDiagnostics
   | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics"
   | where todouble(sizeKb_d) > 800000
   ```

1. Hoe krijg je partitieSleutelstatistieken om scheeftrekking en drie partities voor databaseaccount te evalueren?

    ```Kusto
    AzureDiagnostics 
    | where ResourceProvider=="MICROSOFT.DOCUMENTDB" and Category=="PartitionKeyStatistics" 
    | project SubscriptionId, regionName_s, databaseName_s, collectionname_s, partitionkey_s, sizeKb_s, ResourceId 
    ```

## <a name="next-steps"></a>Volgende stappen

* [Azure-monitor voor Azure Cosmos DB](../azure-monitor/insights/cosmosdb-insights-overview.md?toc=/azure/cosmos-db/toc.json)
* [Controleren en debuggen met statistieken in Azure Cosmos DB](use-metrics.md)
