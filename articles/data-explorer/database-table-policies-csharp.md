---
title: Beleidsregels maken met de Azure Data Explorer C# SDK
description: In dit artikel leert u hoe u beleid maakt met C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: 17312840b0081056ad04723f2b2c241c47902021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667290"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-by-using-c"></a>Database- en tabelbeleid voor Azure Data Explorer maken met C #

> [!div class="op_single_selector"]
> * [C #](database-table-policies-csharp.md)
> * [Python](database-table-policies-python.md)
>

Azure Data Explorer is een snelle en zeer schaalbare service voor gegevensverkenning voor telemetrische gegevens en gegevens uit logboeken. In dit artikel maakt u database- en tabelbeleid voor Azure Data Explorer met C#.

## <a name="prerequisites"></a>Vereisten

* Visual Studio 2019. Als je geen Visual Studio 2019 hebt, kun je de *gratis* [Visual Studio Community 2019](https://www.visualstudio.com/downloads/)downloaden en gebruiken. Zorg ervoor dat u **Azure-ontwikkeling** selecteert tijdens de installatie van Visual Studio.
* Een Azure-abonnement. Als dat nodig is, u een [gratis Azure-account](https://azure.microsoft.com/free/) maken voordat u begint.
* [Een testcluster en -database](create-cluster-database-csharp.md).
* [Een testtafel.](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)

## <a name="install-c-nuget"></a>C# NuGet installeren

* Installeer het [NuGet-pakket (Azure Data Explorer) (Kusto).](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/)
* Installeer het [Microsoft.Azure.Kusto.Data.NETStandard NuGet-pakket](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/). (Optioneel, voor het wijzigen van tabelbeleid.)
* Installeer het [Microsoft.IdentityModel.Clients.ActiveDirectory NuGet-pakket](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/)voor verificatie.

## <a name="authentication"></a>Authentication
Als u de voorbeelden in dit artikel wilt uitvoeren, hebt u een Azure Active Directory-toepassing en serviceprincipal (Azure Directory) nodig die toegang heeft tot bronnen. U dezelfde Azure AD-toepassing gebruiken voor verificatie vanuit [een testcluster en -database.](create-cluster-database-csharp.md#authentication) Als u een andere Azure AD-toepassing wilt gebruiken, [raadpleegt u een Azure AD-toepassing maken](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) om een gratis Azure AD-toepassing te maken en roltoewijzing toe te voegen aan het abonnementsbereik. Dit artikel laat ook `Directory (tenant) ID`zien `Application ID`hoe `Client secret`je de , en . Mogelijk moet u de nieuwe Azure AD-toepassing als hoofd in de database toevoegen. Zie [Azure Data Explorer-databasemachtigingen beheren](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions)voor meer informatie.

## <a name="alter-database-retention-policy"></a>Beleid voor het bewaren van gegevens wijzigen
Hiermee stelt u een bewaarbeleid in met een soft-delete-periode van 10 dagen.
    
```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
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
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(softDeletePeriod: TimeSpan.FromDays(10)));
```

## <a name="alter-database-cache-policy"></a>Databasecachebeleid wijzigen
Hiermee stelt u een cachebeleid in voor de database. De afgelopen vijf dagen van de gegevens zullen worden op het cluster SSD.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
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
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(hotCachePeriod: TimeSpan.FromDays(5)));
```

## <a name="alter-table-cache-policy"></a>Beleid voor tabelcache wijzigen
Hiermee stelt u een cachebeleid in voor de tabel. De afgelopen vijf dagen van de gegevens zullen worden op het cluster SSD.

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var databaseName = "<DatabaseName>";
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client secret
var tableName = "<TableName>"

var kustoConnectionStringBuilder =
    new KustoConnectionStringBuilder(kustoUri)
    {
        FederatedSecurity = true,
        InitialCatalog = databaseName,
        ApplicationClientId = clientId,
        ApplicationKey = clientSecret,
        Authority = tenantId
    };

using (var kustoClient = KustoClientFactory.CreateCslAdminProvider(kustoConnectionStringBuilder))
{
    //dataHotSpan and indexHotSpan should have the same value
    var hotSpan = TimeSpan.FromDays(5);
    var command1 = CslCommandGenerator.GenerateAlterTableCachingPolicyCommand(tableName: tableName,
                    dataHotSpan: hotSpan, indexHotSpan: hotSpan);

    kustoClient.ExecuteControlCommand(command);
}
```

## <a name="add-a-new-principal-for-the-database"></a>Een nieuw hoofdvoor de database toevoegen
Hiermee voegt u een nieuwe Azure AD-toepassing toe als beheerdersprincipal voor de database.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var clientIdToAdd = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
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
//The cluster and database that are created as part of the prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
await kustoManagementClient.Databases.AddPrincipalsAsync(resourceGroupName, clusterName, databaseName,
                new DatabasePrincipalListRequest()
                {
                    Value = new List<DatabasePrincipal>()
                    {
                        new DatabasePrincipal("Admin", "<database_principle_name>", "App", appId: clientIdToAdd, tenantName:tenantId)
                    }
                });
```
## <a name="next-steps"></a>Volgende stappen

* [Lees meer over database- en tabelbeleid](https://docs.microsoft.com/azure/kusto/management/policies)
