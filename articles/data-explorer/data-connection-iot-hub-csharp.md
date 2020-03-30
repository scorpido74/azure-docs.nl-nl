---
title: 'Een IoT Hub-gegevensverbinding voor Azure Data Explorer maken met C #'
description: In dit artikel leert u hoe u een IoT Hub-gegevensverbinding voor Azure Data Explorer maakt met C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 0cac03e50bf46910f8430b745803107b60905769
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667387"
---
# <a name="create-an-iot-hub-data-connection-for-azure-data-explorer-by-using-c-preview"></a>Een IoT Hub-gegevensverbinding voor Azure Data Explorer maken met C# (Voorbeeld)

> [!div class="op_single_selector"]
> * [Portal](ingest-data-iot-hub.md)
> * [C #](data-connection-iot-hub-csharp.md)
> * [Python](data-connection-iot-hub-python.md)
> * [Azure Resource Manager-sjabloon](data-connection-iot-hub-resource-manager.md)

Azure Data Explorer is een snelle en zeer schaalbare service voor gegevensverkenning voor telemetrische gegevens en gegevens uit logboeken. Azure Data Explorer biedt opname (gegevensladen) van gebeurtenishubs, IoT-hubs en blobs die zijn geschreven naar blobcontainers. In dit artikel maakt u een IoT Hub-gegevensverbinding voor Azure Data Explorer met C#.

## <a name="prerequisites"></a>Vereisten

* Als u Visual Studio 2019 niet hebt geïnstalleerd, u de **gratis** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)downloaden en gebruiken. Zorg ervoor dat u **Azure-ontwikkeling** inschakelt tijdens de installatie van Visual Studio.
* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* Een [cluster en database maken](create-cluster-database-csharp.md)
* Tabel- [en kolomtoewijzing maken](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)
* [Database- en tabelbeleid](database-table-policies-csharp.md) instellen (optioneel)
* Maak een [IoT-hub met een beleid voor gedeelde toegang geconfigureerd](ingest-data-iot-hub.md#create-an-iot-hub).

[!INCLUDE [data-explorer-data-connection-install-nuget-csharp](../../includes/data-explorer-data-connection-install-nuget-csharp.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-iot-hub-data-connection"></a>Een IoT Hub-gegevensverbinding toevoegen 

In het volgende voorbeeld ziet u hoe u een IoT Hub-gegevensverbinding programmatisch toevoegt. Zie [Azure Data Explorer-tabel verbinden met IoT Hub](ingest-data-iot-hub.md#connect-azure-data-explorer-table-to-iot-hub) voor het toevoegen van een Iot Hub-gegevensverbinding via de Azure-portal.

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
//The IoT hub that is created as part of the Prerequisites
var iotHubResourceId = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Devices/IotHubs/xxxxxx";
var sharedAccessPolicyName = "iothubforread";
var consumerGroup = "$Default";
var location = "Central US";
//The table and column mapping are created as part of the Prerequisites
var tableName = "StormEvents";
var mappingRuleName = "StormEvents_CSV_Mapping";
var dataFormat = DataFormat.CSV;

await kustoManagementClient.DataConnections.CreateOrUpdate(resourceGroupName, clusterName, databaseName, dataConnectionName,
            new IotHubDataConnection(iotHubResourceId, consumerGroup, sharedAccessPolicyName, tableName: tableName, location: location, mappingRuleName: mappingRuleName, dataFormat: dataFormat));
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
| iotHubResourceId | *Resource-id* | De resource-ID van uw IoT-hub die de gegevens bevat voor opname. |
| sharedAccessPolicyName | *iothubforread* | De naam van het beleid voor gedeelde toegang dat de machtigingen definieert voor apparaten en services om verbinding te maken met IoT Hub. |
| consumerGroup | *$Default* | De consumentengroep van uw eventhub.|
| location | *VS - centraal* | De locatie van de bron voor gegevensverbindingen.|

[!INCLUDE [data-explorer-data-connection-clean-resources-csharp](../../includes/data-explorer-data-connection-clean-resources-csharp.md)]
