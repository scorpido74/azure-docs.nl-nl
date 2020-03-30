---
title: 'Databaseprincipals voor Azure Data Explorer toevoegen met C #'
description: In dit artikel leert u hoe u databaseprincipals voor Azure Data Explorer toevoegt met C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 797d1253d44739f2026563e3df72bc85a8ef382e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965032"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-c"></a>Databaseprincipals voor Azure Data Explorer toevoegen met C #

> [!div class="op_single_selector"]
> * [C #](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Azure Resource Manager-sjabloon](database-principal-resource-manager.md)

Azure Data Explorer is een snelle en zeer schaalbare service voor gegevensverkenning voor telemetrische gegevens en gegevens uit logboeken. In dit artikel voegt u databaseprincipals voor Azure Data Explorer toe met C#.

## <a name="prerequisites"></a>Vereisten

* Als u Visual Studio 2019 niet hebt geïnstalleerd, u de **gratis** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)downloaden en gebruiken. Zorg ervoor dat u **Azure-ontwikkeling** inschakelt tijdens de installatie van Visual Studio.
* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* [Maak een cluster en database](create-cluster-database-csharp.md).

## <a name="install-c-nuget"></a>C# NuGet installeren

* Installeer [Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installeer [Microsoft.Rest.ClientRuntime.Azure.Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) voor verificatie.

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Een databaseprincipal toevoegen

In het volgende voorbeeld ziet u hoe u een hoofdbestandman van een database programmatisch toevoegen.

```csharp
var tenantId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Directory (tenant) ID
var clientId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";//Application ID
var clientSecret = "xxxxxxxxxxxxxx";//Client Secret
var subscriptionId = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

var serviceCreds = await ApplicationTokenProvider.LoginSilentAsync(tenantId, clientId, clientSecret);
var kustoManagementClient = new KustoManagementClient(serviceCreds)
{
    SubscriptionId = subscriptionId
};

var resourceGroupName = "testrg";
//The cluster and database that are created as part of the Prerequisites
var clusterName = "mykustocluster";
var databaseName = "mykustodatabase";
string principalAssignmentName = "databasePrincipalAssignment1";
string principalId = "xxxxxxxx";//User email, application ID, or security group name
string role = "Admin";//Admin, Ingestor, Monitor, User, UnrestrictedViewers, Viewer
string tenantIdForPrincipal = tenantId;
string principalType = "App";//User, App, or Group

var databasePrincipalAssignment = new DatabasePrincipalAssignment(principalId, role, principalType, tenantId: tenantIdForPrincipal);
await kustoManagementClient.DatabasePrincipalAssignments.CreateOrUpdateAsync(resourceGroupName, clusterName, databaseName, principalAssignmentName, databasePrincipalAssignment);
```

|**Instelling** | **Voorgestelde waarde** | **Veldbeschrijving**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Je pacht-id. Ook wel directory ID genoemd.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | De abonnements-ID die u gebruikt voor het maken van resources.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | De client-id van de toepassing die toegang heeft tot bronnen in uw tenant.|
| clientGeheim | *xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx* | Het clientgeheim van de toepassing die toegang heeft tot bronnen in uw tenant. |
| resourceGroupName | *testrg* | De naam van de resourcegroep die uw cluster bevat.|
| clusterName | *mykustocluster* | De naam van uw cluster.|
| Databasenaam | *mykustodatabase* | De naam van uw database.|
| principalAssignmentName | *databasePrincipalAssignment1* | De naam van uw database hoofdbron.|
| principalId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | De hoofd-ID, die kan worden gebruiker e-mail, applicatie-id, of beveiligingsgroep naam.|
| role | *Beheerder* | De rol van uw databaseprincipal, die 'Admin', 'Ingestor', 'Monitor', 'User', 'UnrestrictedViewers', 'Viewer' kan zijn.|
| huurderIdForPrincipal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | De huurder ID van de opdrachtgever.|
| principalType | *App* | Het type principal, dat 'Gebruiker', 'App' of 'Groep' kan zijn|

## <a name="next-steps"></a>Volgende stappen

* [Gegevens opnemen met behulp van de Node-bibliotheek voor Azure Data Explorer](node-ingest-data.md)
