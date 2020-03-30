---
title: 'Een gebeurtenishub-gegevensverbinding voor Azure Data Explorer maken met C #'
description: In dit artikel leert u hoe u een gebeurtenishub-gegevensverbinding voor Azure Data Explorer maakt met C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: cf2a274b4f48b31739d6abba5cf87fa2a10d4ca1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667681"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-c"></a>Een gebeurtenishub-gegevensverbinding voor Azure Data Explorer maken met C #

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-hub.md)
> * [C #](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)
> * [Azure Resource Manager-sjabloon](data-connection-event-hub-resource-manager.md)

Azure Data Explorer is een snelle en zeer schaalbare service voor gegevensverkenning voor telemetrische gegevens en gegevens uit logboeken. Azure Data Explorer biedt opname (gegevensladen) van gebeurtenishubs, IoT-hubs en blobs die zijn geschreven naar blobcontainers. In dit artikel maakt u een Event Hub-gegevensverbinding voor Azure Data Explorer met C#.

## <a name="prerequisites"></a>Vereisten

* Als u Visual Studio 2019 niet hebt geïnstalleerd, u de **gratis** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)downloaden en gebruiken. Zorg ervoor dat u **Azure-ontwikkeling** inschakelt tijdens de installatie van Visual Studio.
* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* Een [cluster en database maken](create-cluster-database-csharp.md)
* Tabel- [en kolomtoewijzing maken](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* [Database- en tabelbeleid](database-table-policies-csharp.md) instellen (optioneel)
* Maak een [gebeurtenishub met gegevens voor inname](ingest-data-event-hub.md#create-an-event-hub). 

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Een gegevensverbinding met gebeurtenishub toevoegen

In het volgende voorbeeld ziet u hoe u een gebeurtenishub-gegevensverbinding programmatisch toevoegt. Zie [verbinding maken met de gebeurtenishub](ingest-data-event-hub.md#connect-to-the-event-hub) voor het toevoegen van een Event Hub-gegevensverbinding via de Azure-portal.

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
//The event hub that is created as part of the Prerequisites
var eventHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, dataConnectionName, 
    new EventHubDataConnection(eventHubResourceId, consumerGroup, location: location, tableName: tableName, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
```

|**Instelling** | **Voorgestelde waarde** | **Veldbeschrijving**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Je pacht-id. Ook wel directory ID genoemd.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | De abonnements-ID die u gebruikt voor het maken van resources.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | De client-id van de toepassing die toegang heeft tot bronnen in uw tenant.|
| clientGeheim | *xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx* | Het clientgeheim van de toepassing die toegang heeft tot bronnen in uw tenant.|
| resourceGroupName | *testrg* | De naam van de resourcegroep die uw cluster bevat.|
| clusterName | *mykustocluster* | De naam van uw cluster.|
| Databasenaam | *mykustodatabase* | De naam van de doeldatabase in uw cluster.|
| gegevensConnectionName | *myeventhubconnect* | De gewenste naam van uw gegevensverbinding.|
| tableName | *StormEvenementen* | De naam van de doeltabel in de doeldatabase.|
| mappingRuleName | *StormEvents_CSV_Mapping* | De naam van uw kolomtoewijzing met betrekking tot de doeltabel.|
| gegevensOpmaken | *Csv* | De gegevensindeling van het bericht.|
| eventHubResourceId | *Resource-id* | De resource-id van uw gebeurtenishub die de gegevens bevat voor opname. |
| consumerGroup | *$Default* | De consumentengroep van uw Event Hub.|
| location | *VS - centraal* | De locatie van de bron voor gegevensverbindingen.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
