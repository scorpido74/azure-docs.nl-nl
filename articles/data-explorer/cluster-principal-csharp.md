---
title: 'Clusterprincipals voor Azure Data Explorer toevoegen met C #'
description: In dit artikel leert u hoe u clusterprincipals voor Azure Data Explorer toevoegt met C#.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: e6c3970890dfe2c669dee1acf631e9dd45ab1085
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965058"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-c"></a>Clusterprincipals voor Azure Data Explorer toevoegen met C #

> [!div class="op_single_selector"]
> * [C #](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Azure Resource Manager-sjabloon](cluster-principal-resource-manager.md)

Azure Data Explorer is een snelle en zeer schaalbare service voor gegevensverkenning voor telemetrische gegevens en gegevens uit logboeken. In dit artikel voegt u clusterprincipals voor Azure Data Explorer toe met C#.

## <a name="prerequisites"></a>Vereisten

* Als u Visual Studio 2019 niet hebt geïnstalleerd, u de **gratis** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)downloaden en gebruiken. Zorg ervoor dat u **Azure-ontwikkeling** inschakelt tijdens de installatie van Visual Studio.
* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* [Een cluster maken](create-cluster-database-csharp.md).

## <a name="install-c-nuget"></a>C# NuGet installeren

* Installeer [Microsoft.Azure.Management.kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installeer [Microsoft.Rest.ClientRuntime.Azure.Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) voor verificatie.

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-cluster-principal"></a>Een clusterprincipal toevoegen

In het volgende voorbeeld ziet u hoe u een clusterprincipal programmatisch toevoegt.

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
//The cluster that is created as part of the Prerequisites
var clusterName = "mykustocluster";
string principalAssignmentName = "clusterPrincipalAssignment1";
string principalId = "xxxxxxxx";//User email, application ID, or security group name
string role = "AllDatabasesAdmin";//AllDatabasesAdmin or AllDatabasesViewer
string tenantIdForPrincipal = tenantId;
string principalType = "App";//User, App, or Group

var clusterPrincipalAssignment = new ClusterPrincipalAssignment(principalId, role, principalType, tenantId: tenantIdForPrincipal);
await kustoManagementClient.ClusterPrincipalAssignments.CreateOrUpdateAsync(resourceGroupName, clusterName, principalAssignmentName, clusterPrincipalAssignment);
```

|**Instelling** | **Voorgestelde waarde** | **Veldbeschrijving**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Je pacht-id. Ook wel directory ID genoemd.|
| subscriptionId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | De abonnements-ID die u gebruikt voor het maken van resources.|
| clientId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | De client-id van de toepassing die toegang heeft tot bronnen in uw tenant.|
| clientGeheim | *xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx* | Het clientgeheim van de toepassing die toegang heeft tot bronnen in uw tenant. |
| resourceGroupName | *testrg* | De naam van de resourcegroep die uw cluster bevat.|
| clusterName | *mykustocluster* | De naam van uw cluster.|
| principalAssignmentName | *clusterPrincipalAssignment1* | De naam van uw clusterhoofdbron.|
| principalId | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | De hoofd-ID, die kan worden gebruiker e-mail, applicatie-id, of beveiligingsgroep naam.|
| role | *AllDatabasesAdmin* | De rol van uw clusterprincipal, die 'AllDatabasesAdmin' of 'AllDatabasesViewer' kan zijn.|
| huurderIdForPrincipal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | De huurder ID van de opdrachtgever.|
| principalType | *App* | Het type principal, dat 'Gebruiker', 'App' of 'Groep' kan zijn|

## <a name="next-steps"></a>Volgende stappen

* [Databaseprincipals toevoegen](database-principal-csharp.md)
