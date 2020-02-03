---
title: End-to-end-BLOB-opname in azure Data Explorer viaC#
description: In dit artikel leert u hoe u blobs kunt opnemen in azure Data Explorer met een end-to-end-voor beeld C#waarin wordt gebruikt.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 0711484c4fff24c5dcd3c18effce596a92bc30c3
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76964512"
---
# <a name="end-to-end-blob-ingestion-into-azure-data-explorer-through-c"></a>End-to-end-BLOB-opname in azure Data Explorer viaC#

> [!div class="op_single_selector"]
> * [C#](end-to-end-csharp.md)
> * [Python](end-to-end-python.md)
>

Azure Data Explorer is een snelle en schaal bare service voor gegevens exploratie voor logboek-en telemetriegegevens. In dit artikel vindt u een end-to-end-voor beeld van het opnemen van gegevens uit Azure Blob Storage in azure Data Explorer. 

U leert hoe u via programma code een resource groep, een opslag account en een container, een Event Hub en een Azure Data Explorer-cluster en-data base maakt. U leert ook hoe u Azure Data Explorer programmatisch kunt configureren om gegevens op te nemen uit het nieuwe opslag account.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="install-c-nuget"></a>NuGet C# installeren

* Installeer [micro soft. Azure. Management. kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installeer [micro soft. Azure. Management. Resource Manager](https://www.nuget.org/packages/Microsoft.Azure.Management.ResourceManager).
* Installeer [micro soft. Azure. Management. EventGrid](https://www.nuget.org/packages/Microsoft.Azure.Management.EventGrid/).
* Installeer [micro soft. Azure. storage. blob](https://www.nuget.org/packages/Microsoft.Azure.Storage.Blob/).
* Installeer [micro soft. rest. ClientRuntime. Azure. Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) voor authenticatie.

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

[!INCLUDE [data-explorer-e2e-event-grid-resource-template](../../includes/data-explorer-e2e-event-grid-resource-template.md)]

## <a name="code-example"></a>Voorbeeld van code 

Het volgende code voorbeeld geeft u een stapsgewijs proces dat resulteert in de opname van gegevens in azure Data Explorer. 

U maakt eerst een resource groep. U maakt ook Azure-resources, zoals een opslag account en een container, een Event Hub, een Azure Data Explorer cluster en data base, en toevoegen van principals. Vervolgens maakt u een Azure Event Grid-abonnement, samen met een tabel-en kolom toewijzing, in de Azure Data Explorer-data base. Ten slotte maakt u de gegevens verbinding om Azure Data Explorer te configureren voor het opnemen van gegevens van het nieuwe opslag account. 

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
string location = "West Europe";
string locationSmallCase = "westeurope";
string azureResourceTemplatePath = @"xxxxxxxxx\template.json";//Path to the Azure Resource Manager template JSON from the previous section

string deploymentName = "e2eexample";
string resourceGroupName = deploymentName + "resourcegroup";
string eventHubName = deploymentName + "eventhub";
string eventHubNamespaceName = eventHubName + "ns";
string storageAccountName = deploymentName + "storage";
string storageContainerName = deploymentName + "storagecontainer";
string eventGridSubscriptionName = deploymentName + "eventgrid";
string kustoClusterName = deploymentName + "kustocluster";
string kustoDatabaseName = deploymentName + "kustodatabase";
string kustoTableName = "Events";
string kustoColumnMappingName = "Events_CSV_Mapping";
string kustoDataConnectionName = deploymentName + "kustoeventgridconnection";

//principals
string principalIdForCluster = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
string roleForClusterPrincipal = "AllDatabasesAdmin";
string tenantIdForClusterPrincipal = tenantId;
string principalTypeForCluster = "App";
string principalIdForDatabase = "xxxxxxxx@xxxxxxxx.com";//User Email
string roleForDatabasePrincipal = "Admin";
string tenantIdForDatabasePrincipal = tenantId;
string principalTypeForDatabase = "User";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var resourceManagementClient = new ResourceManagementClient(serviceCreds);
Console.WriteLine("Step 1: Create a new resource group in your Azure subscription to manage all the resources for using Azure Data Explorer.");
resourceManagementClient.SubscriptionId = subscriptionId;
await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync(resourceGroupName,
    new ResourceGroup() { Location = locationSmallCase });

Console.WriteLine(
    "Step 2: Create a Blob Storage, a container in the Storage account, an Event Hub, an Azure Data Explorer cluster, database, and add principals by using an Azure Resource Manager template.");
var parameters = new Dictionary<string, Dictionary<string, object>>();
parameters["eventHubNamespaceName"] = new Dictionary<string, object>(capacity: 1) {{"value", eventHubNamespaceName}};
parameters["eventHubName"] = new Dictionary<string, object>(capacity: 1) {{"value", eventHubName }};
parameters["storageAccountName"] = new Dictionary<string, object>(capacity: 1) {{"value", storageAccountName }};
parameters["containerName"] = new Dictionary<string, object>(capacity: 1) {{"value", storageContainerName }};
parameters["kustoClusterName"] = new Dictionary<string, object>(capacity: 1) {{"value", kustoClusterName }};
parameters["kustoDatabaseName"] = new Dictionary<string, object>(capacity: 1) {{"value", kustoDatabaseName }};
parameters["principalIdForCluster"] = new Dictionary<string, object>(capacity: 1) {{"value", principalIdForCluster }};
parameters["roleForClusterPrincipal"] = new Dictionary<string, object>(capacity: 1) {{"value", roleForClusterPrincipal }};
parameters["tenantIdForClusterPrincipal"] = new Dictionary<string, object>(capacity: 1) {{"value", tenantIdForClusterPrincipal }};
parameters["principalTypeForCluster"] = new Dictionary<string, object>(capacity: 1) {{"value", principalTypeForCluster }};
parameters["principalIdForDatabase"] = new Dictionary<string, object>(capacity: 1) {{"value", principalIdForDatabase }};
parameters["roleForDatabasePrincipal"] = new Dictionary<string, object>(capacity: 1) {{"value", roleForDatabasePrincipal }};
parameters["tenantIdForDatabasePrincipal"] = new Dictionary<string, object>(capacity: 1) {{"value", tenantIdForDatabasePrincipal }};
parameters["principalTypeForDatabase"] = new Dictionary<string, object>(capacity: 1) {{"value", principalTypeForDatabase }};
            
string template = File.ReadAllText(azureResourceTemplatePath, Encoding.UTF8);
await resourceManagementClient.Deployments.CreateOrUpdateAsync(resourceGroupName, deploymentName,
    new Deployment(new DeploymentProperties(DeploymentMode.Incremental, template: template,
        parameters: parameters)));

Console.WriteLine(
    "Step 3: Create an Event Grid subscription to publish blob events created in a specific container to an Event Hub.");
var eventGridClient = new EventGridManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};
string storageResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}";
string eventHubResourceId = $"/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.EventHub/namespaces/{eventHubNamespaceName}/eventhubs/{eventHubName}";
await eventGridClient.EventSubscriptions.CreateOrUpdateAsync(storageResourceId, eventGridSubscriptionName,
    new EventSubscription()
    {
        Destination = new EventHubEventSubscriptionDestination(eventHubResourceId),
        Filter = new EventSubscriptionFilter()
        {
            SubjectBeginsWith = $"/blobServices/default/containers/{storageContainerName}",
            IncludedEventTypes = new List<string>(){"Microsoft.Storage.BlobCreated"}
        }
    });

Console.WriteLine("Step 4: Create a table (with three columns: EventTime, EventId, and EventSummary) and column mapping in your Azure Data Explorer database.");
var kustoUri = $"https://{kustoClusterName}.{locationSmallCase}.kusto.windows.net";
var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(kustoUri)
{
    InitialCatalog = kustoDatabaseName,
    FederatedSecurity = true,
    ApplicationClientId = clientId,
    ApplicationKey = clientSecret,
    Authority = tenantId
};

using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    var command =
        CslCommandGenerator.GenerateTableCreateCommand(
            kustoTableName,
            new[]
            {
                Tuple.Create("EventTime", "System.DateTime"),
                Tuple.Create("EventId", "System.Int32"),
                Tuple.Create("EventSummary", "System.String"),
            });

    kustoClient.ExecuteControlCommand(command);

    command = CslCommandGenerator.GenerateTableCsvMappingCreateCommand(
        kustoTableName,
        kustoColumnMappingName,
        new[]
        {
            new CsvColumnMapping { ColumnName = "EventTime", CslDataType="dateTime", Ordinal = 0 },
            new CsvColumnMapping { ColumnName = "EventId", CslDataType="int", Ordinal = 1 },
            new CsvColumnMapping { ColumnName = "EventSummary", CslDataType="string", Ordinal = 2 },
        });
    kustoClient.ExecuteControlCommand(command);
}

Console.WriteLine("Step 5: Add an Event Grid data connection. Azure Data Explorer will automatically ingest the data when new blobs are created.");
var kustoManagementClient = new KustoManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};
await kustoManagementClient.DataConnections.CreateOrUpdateAsync(resourceGroupName, kustoClusterName,
                kustoDatabaseName, dataConnectionName: kustoDataConnectionName, new EventGridDataConnection(storageResourceId, eventHubResourceId, consumerGroup: "$Default", location: location, tableName:kustoTableName, mappingRuleName: kustoColumnMappingName, dataFormat: "csv"));

```
| **Instelling** | **Beschrijving van veld** |
|---|---|---|
| tenantId | Uw Tenant-ID. Het wordt ook wel een directory-ID genoemd.|
| subscriptionId | De abonnements-ID die u gebruikt voor het maken van resources.|
| clientId | De client-ID van de toepassing die toegang heeft tot bronnen in uw Tenant.|
| clientSecret | Het client geheim van de toepassing die toegang heeft tot bronnen in uw Tenant. |

## <a name="test-the-code-example"></a>Het code voorbeeld testen

1. Upload een bestand naar het opslag account.

    ```csharp
    string storageConnectionString = "DefaultEndpointsProtocol=https;AccountName=xxxxxxxxxxxxxx;AccountKey=xxxxxxxxxxxxxx;EndpointSuffix=core.windows.net";
    var cloudStorageAccount = CloudStorageAccount.Parse(storageConnectionString);
    CloudBlobClient blobClient = cloudStorageAccount.CreateCloudBlobClient();
    CloudBlobContainer container = blobClient.GetContainerReference(storageContainerName);
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("test.csv");
    var blobContent = @"2007-01-01 00:00:00.0000000,2592,Several trees down
    2007-01-01 00:00:00.0000000,4171,Winter Storm";
    await blockBlob.UploadTextAsync(blobContent);
    ```
    |**Instelling** | **Beschrijving van veld**|
    |---|---|---|
    | storageConnectionString | De connection string van het programmatisch gemaakte opslag account.|

2. Voer een test query uit in azure Data Explorer.

    ```csharp
    var kustoUri = $"https://{kustoClusterName}.{locationSmallCase}.kusto.windows.net";
    var kustoConnectionStringBuilder = new KustoConnectionStringBuilder(kustoUri)
    {
        InitialCatalog = kustoDatabaseName,
        FederatedSecurity = true,
        ApplicationClientId = clientId,
        ApplicationKey = clientSecret,
        Authority = tenantId
    };
    using (var kustoClient = KustoClientFactory.CreateCslQueryProvider(kustoConnectionStringBuilder))
    {
        var query = $"{kustoTableName} | take 10";
        using (var reader = kustoClient.ExecuteQuery(query) as DataTableReader2)
        {// Print the contents of each of the result sets. 
            while (reader.Read())
            {
                Console.WriteLine($"{reader[0]}, {reader[1]}, {reader[2]}");
            }
        }
    }
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Als u de resource groep wilt verwijderen en resources wilt opschonen, gebruikt u de volgende opdracht:

```csharp
await resourceManagementClient.ResourceGroups.DeleteAsync(resourceGroupName);
```

## <a name="next-steps"></a>Volgende stappen

*  Zie [een Azure Data Explorer-cluster en-data base maken](create-cluster-database-csharp.md)voor meer informatie over andere manieren om een cluster en Data Base te maken.
* Zie voor meer informatie over opname methoden [Azure Data Explorer gegevens opname](ingest-data-overview.md).
* Voor meer informatie over de webtoepassing raadpleegt u [Quick Start: Query's uitvoeren in de Web-UI van Azure Data Explorer](web-query-data.md).
* [Schrijf query's](write-queries.md) met een Kusto-query taal.