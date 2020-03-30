---
title: 'Een gebeurtenisrastergegevensverbinding voor Azure Data Explorer maken met C #'
description: In dit artikel leert u hoe u een gebeurtenisrastergegevensverbinding voor Azure Data Explorer maakt met C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 03963f60cc364dd36ad55c0a28e92e3b585bb38d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255083"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-c"></a>Een gebeurtenisrastergegevensverbinding voor Azure Data Explorer maken met C #

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C #](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager-sjabloon](data-connection-event-grid-resource-manager.md)


Azure Data Explorer is een snelle en zeer schaalbare service voor gegevensverkenning voor telemetrische gegevens en gegevens uit logboeken. Azure Data Explorer biedt opname (gegevensladen) van gebeurtenishubs, IoT-hubs en blobs die zijn geschreven naar blobcontainers. In dit artikel maakt u een eventgridgegevensverbinding voor Azure Data Explorer met C#.

## <a name="prerequisites"></a>Vereisten

* Als u Visual Studio 2019 niet hebt geïnstalleerd, u de **gratis** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)downloaden en gebruiken. Zorg ervoor dat u **Azure-ontwikkeling** inschakelt tijdens de installatie van Visual Studio.
* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* Een [cluster en database maken](create-cluster-database-csharp.md)
* Tabel- [en kolomtoewijzing maken](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* [Database- en tabelbeleid](database-table-policies-csharp.md) instellen (optioneel)
* Maak een [opslagaccount aan met een Event Grid-abonnement](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account).

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Een gegevensverbinding voor gebeurtenisraster toevoegen

In het volgende voorbeeld ziet u hoe u een gebeurtenisrastergegevensverbinding programmatisch toevoegt. Zie [een eventgrid-gegevensverbinding maken in Azure Data Explorer](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) voor het toevoegen van een gebeurtenisrastergegevensverbinding via de Azure-portal.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
var authenticationContext = new AuthenticationContext($"https://login.windows.net/{tenantId}");
var credential = new ClientCredential(clientId, clientSecret);
var result = await authenticationContext.AcquireTokenAsync(resource: "https://management.core.windows.net/", clientCredential: credential);

var credentials = new TokenCredentials(result.AccessToken, result.AccessTokenType);

var kustoManagementClient = new KustoManagementClient(credentials)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
var dataConnectionName = "myeventhubconnect";
//The event hub and storage account that are created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var storageAccountResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName,
    new EventGridDataConnection(storageAccountResourceId, eventHubResourceId, consumerGroup, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Instelling** | **Voorgestelde waarde** | **Veldbeschrijving**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Je pacht-id. Ook wel directory ID genoemd.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | De abonnements-ID die u gebruikt voor het maken van resources.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | De client-id van de toepassing die toegang heeft tot bronnen in uw tenant.|
| clientGeheim | *xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx* | Het clientgeheim van de toepassing die toegang heeft tot bronnen in uw tenant. |
| resourceGroupName | *testrg* | De naam van de resourcegroep die uw cluster bevat.|
| clusterName | *mykustocluster* | De naam van uw cluster.|
| Databasenaam | *mykustodatabase* | De naam van de doeldatabase in uw cluster.|
| gegevensConnectionName | *myeventhubconnect* | De gewenste naam van uw gegevensverbinding.|
| tableName | *StormEvenementen* | De naam van de doeltabel in de doeldatabase.|
| mappingRuleName | *StormEvents_CSV_Mapping* | De naam van uw kolomtoewijzing met betrekking tot de doeltabel.|
| gegevensOpmaken | *Csv* | De gegevensindeling van het bericht.|
| eventHubResourceId | *Resource-id* | De resource-id van uw gebeurtenishub waar het gebeurtenisraster is geconfigureerd om gebeurtenissen te verzenden. |
| storageAccountResourceId | *Resource-id* | De bron-ID van uw opslagaccount die de gegevens bevat voor opname. |
| consumerGroup | *$Default* | De consumentengroep van uw Event Hub.|
| location | *VS - centraal* | De locatie van de bron voor gegevensverbindingen.|

## <a name="generate-sample-data"></a>Voorbeeldgegevens genereren

Nu Azure Data Explorer en het opslagaccount zijn verbonden, u voorbeeldgegevens maken en uploaden naar de blob-opslag.

Met dit script wordt een nieuwe container in uw opslagaccount gemaakt, wordt een bestaand bestand (als blob) naar die container geüpload en worden de blobs in de container weergegeven.

```csharp
var azureStorageAccountConnectionString=<storage_account_connection_string>;

var containerName=<container_name>;
var blobName=<blob_name>;
var localFileName=<file_to_upload>;

// Creating the container
var azureStorageAccount = CloudStorageAccount.Parse(azureStorageAccountConnectionString);
var blobClient = azureStorageAccount.CreateCloudBlobClient();
var container = blobClient.GetContainerReference(containerName);
container.CreateIfNotExists();

// Set metadata and upload file to blob
var blob = container.GetBlockBlobReference(blobName);
blob.Metadata.Add("rawSizeBytes", "4096‬"); // the uncompressed size is 4096 bytes
blob.Metadata.Add("kustoIngestionMappingReference", "mapping_v2‬");
blob.UploadFromFile(localFileName);

// List blobs
var blobs = container.ListBlobs();
```

> [!NOTE]
> Azure Data Explorer verwijdert de blobs na inname niet. Bewaar de blobs drie tot vijf dagen met behulp van de levenscyclus van [Azure Blob-opslag](https://docs.microsoft.com/azure/storage/blobs/storage-lifecycle-management-concepts?tabs=azure-portal) om blobverwijdering te beheren.

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
