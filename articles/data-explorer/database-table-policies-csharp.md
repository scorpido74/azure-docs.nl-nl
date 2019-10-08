---
title: Beleids regels maken met behulp C# van de Azure Data Explorer SDK
description: In dit artikel leert u hoe u beleids regels kunt maken met behulp van c#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: fa2dd4993dbf70bcbc6ea97726f8cd7254123429
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/07/2019
ms.locfileid: "71997226"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-using-c"></a>Data Base-en tabel beleid maken voor Azure-Data Explorer met behulp vanC#

> [!div class="op_single_selector"]
> * [C#](database-table-policies-csharp.md)
> * [Python](database-table-policies-python.md)
>

Azure Data Explorer is een snelle en zeer schaalbare service om gegevens in logboeken en telemetrie te verkennen. In dit artikel maakt u een Data Base-en tabel beleid voor Azure C#Data Explorer met behulp van.

## <a name="prerequisites"></a>Vereisten

* Als Visual Studio 2019 niet is geïnstalleerd, kunt u de **gratis** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)downloaden en gebruiken. Zorg ervoor dat u **Azure-ontwikkeling** inschakelt tijdens de installatie van Visual Studio.

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.

* [Een cluster en database voor testdoeleinden](create-cluster-database-csharp.md)

* [Een test tabel](net-standard-ingest-data.md#create-a-table-on-your-test-cluster)

## <a name="install-c-nuget"></a>Nuget C# installeren

* Installeer het [Azure Data Explorer (Kusto) nuget-pakket](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).

* Installeer het [micro soft. Azure. Kusto. data. netstandard nuget-pakket](https://www.nuget.org/packages/Microsoft.Azure.Kusto.Data.NETStandard/) (optioneel, voor het wijzigen van het beleid van de tabel).

* Installeer het [nuget-pakket micro soft. Identity model. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) voor verificatie.

## <a name="authentication"></a>Authentication
Voor het uitvoeren van de voor beelden in dit artikel hebben we een Azure AD-toepassing en service-principal nodig die toegang hebben tot resources. U kunt dezelfde Azure AD-toepassing gebruiken voor verificatie vanuit [een test cluster en data base](create-cluster-database-csharp.md#authentication). Als u een andere Azure AD-toepassing wilt gebruiken, raadpleegt u [een Azure AD-toepassing maken](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) om een gratis Azure AD-toepassing te maken en roltoewijzing toe te voegen aan het abonnements bereik. U ziet ook hoe u de `Directory (tenant) ID`, `Application ID` en `Client Secret` kunt ophalen. U moet mogelijk de nieuwe Azure AD-toepassing toevoegen als een principal in de data base. Zie [Azure Data Explorer-database machtigingen beheren](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions)voor meer informatie.   

## <a name="alter-database-retention-policy"></a>Beleid voor het bewaren van data bases wijzigen
Hiermee stelt u een Bewaar beleid in met een periode van 10 dagen voor zacht verwijderen.
    
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
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(softDeletePeriod: TimeSpan.FromDays(10)));
```

## <a name="alter-database-cache-policy"></a>Cache beleid Alter data base
Hiermee stelt u een cache beleid in voor de data base die de laatste vijf dagen aan gegevens op de cluster SSD heeft.

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
await kustoManagementClient.Databases.UpdateAsync(resourceGroupName, clusterName, databaseName, new DatabaseUpdate(hotCachePeriod: TimeSpan.FromDays(5)));
```

## <a name="alter-table-cache-policy"></a>ALTER TABLE cache Policy
Hiermee stelt u een cache beleid in voor de tabel die de laatste vijf dagen aan gegevens op de cluster SSD heeft.

```csharp
var kustoUri = "https://<ClusterName>.<Region>.kusto.windows.net:443/";
var databaseName = "<DatabaseName>";
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
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

## <a name="add-a-new-principal-for-database"></a>Een nieuwe Principal toevoegen voor de data base
Een nieuwe Azure AD-toepassing toevoegen als admin-principal voor de data base

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
//The cluster and database that are created as part of the Prerequisites
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

* [Meer informatie over data base-en tabel beleid](https://docs.microsoft.com/azure/kusto/management/policies)
