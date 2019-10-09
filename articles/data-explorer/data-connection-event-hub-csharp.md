---
title: Een event hub-gegevens verbinding maken voor Azure Data Explorer met behulp vanC#
description: In dit artikel leert u hoe u een event hub-gegevens verbinding voor Azure Data Explorer maakt met behulp C#van.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 741232bb7ed6ecf3d20711c1f6248f8e6d6215e8
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72031680"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-c"></a>Een event hub-gegevens verbinding maken voor Azure Data Explorer met behulp vanC#

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)

Azure Data Explorer is een snelle en zeer schaalbare service om gegevens in logboeken en telemetrie te verkennen. Azure Data Explorer biedt opname (gegevens laden) van Event Hubs, IoT hubs en blobs die zijn geschreven naar BLOB-containers. In dit artikel maakt u een event hub-gegevens verbinding voor Azure Data Explorer met behulp C#van.

## <a name="prerequisites"></a>Vereisten

* Als Visual Studio 2019 niet is geïnstalleerd, kunt u de **gratis** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)downloaden en gebruiken. Zorg ervoor dat u **Azure-ontwikkeling** inschakelt tijdens de installatie van Visual Studio.

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.

* [Een cluster en data base](create-cluster-database-csharp.md) maken

* [Tabel-en kolom toewijzing](net-standard-ingest-data.md#create-a-table-on-your-test-cluster) maken

* [Data Base-en tabel beleid](database-table-policies-csharp.md) instellen (optioneel)

* Een [Event hub maken met gegevens voor opname](ingest-data-event-hub.md#create-an-event-hub). 

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Een event hub-gegevens verbinding toevoegen

In het volgende voor beeld ziet u hoe u een event hub-gegevens verbinding programmatisch kunt toevoegen. Zie [verbinding maken met de Event hub](ingest-data-event-hub.md#connect-to-the-event-hub) voor het toevoegen van een event hub-gegevens verbinding met behulp van de Azure Portal.

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

|**Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-XXXX-XXXX-xxxxxxxxx* | Uw Tenant-ID. Ook bekend als Directory-ID.|
| subscriptionId | *xxxxxxxx-xxxxx-XXXX-XXXX-xxxxxxxxx* | De abonnements-ID die u gebruikt voor het maken van resources.|
| clientId | *xxxxxxxx-xxxxx-XXXX-XXXX-xxxxxxxxx* | De client-ID van de toepassing die toegang heeft tot bronnen in uw Tenant.|
| clientSecret | *xxxxxxxxxxxxxx* | Het client geheim van de toepassing die toegang heeft tot bronnen in uw Tenant.|
| resourceGroupName | *testrg* | De naam van de resource groep die het cluster bevat.|
| clusterName | *mykustocluster* | De naam van uw cluster.|
| databaseName | *mykustodatabase* | De naam van de doel database in uw cluster.|
| dataConnection | *myeventhubconnect* | De gewenste naam van uw gegevens verbinding.|
| tableName | *StormEvents* | De naam van de doel tabel in de doel database.|
| mappingRuleName | *StormEvents_CSV_Mapping* | De naam van de kolom toewijzing die is gerelateerd aan de doel tabel.|
| dataFormat | *bestand* | De gegevens indeling van het bericht.|
| eventHubResourceId | *Resource-ID* | De resource-ID van uw event hub die de gegevens bevat voor opname. |
| consumerGroup | *$Default* | De consumenten groep van uw event hub.|
| location | *US - centraal* | De locatie van de bron van de gegevens verbinding.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
