---
title: Voeg cluster-principals voor Azure Data Explorer toe met behulp vanC#
description: In dit artikel leert u hoe u cluster-principals voor Azure Data Explorer kunt toevoegen met C#behulp van.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: e6c3970890dfe2c669dee1acf631e9dd45ab1085
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965058"
---
# <a name="add-cluster-principals-for-azure-data-explorer-by-using-c"></a>Voeg cluster-principals voor Azure Data Explorer toe met behulp vanC#

> [!div class="op_single_selector"]
> * [C#](cluster-principal-csharp.md)
> * [Python](cluster-principal-python.md)
> * [Azure Resource Manager-sjabloon](cluster-principal-resource-manager.md)

Azure Data Explorer is een snelle en zeer schaalbare service voor gegevensverkenning voor telemetrische gegevens en gegevens uit logboeken. In dit artikel voegt u cluster-principals voor Azure Data Explorer toe met C#behulp van.

## <a name="prerequisites"></a>Vereisten

* Als Visual Studio 2019 niet is geïnstalleerd, kunt u de **gratis** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)downloaden en gebruiken. Zorg ervoor dat u **Azure-ontwikkeling** inschakelt tijdens de installatie van Visual Studio.
* Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* [Maak een cluster](create-cluster-database-csharp.md).

## <a name="install-c-nuget"></a>NuGet C# installeren

* Installeer [micro soft. Azure. Management. kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installeer [micro soft. rest. ClientRuntime. Azure. Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) voor authenticatie.

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-cluster-principal"></a>Een cluster-Principal toevoegen

In het volgende voor beeld ziet u hoe u een cluster-Principal kunt toevoegen via een programma.

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

|**Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-XXXX-XXXX-xxxxxxxxx* | Uw Tenant-ID. Ook bekend als Directory-ID.|
| subscriptionId | *xxxxxxxx-xxxxx-XXXX-XXXX-xxxxxxxxx* | De abonnements-ID die u gebruikt voor het maken van resources.|
| clientId | *xxxxxxxx-xxxxx-XXXX-XXXX-xxxxxxxxx* | De client-ID van de toepassing die toegang heeft tot bronnen in uw Tenant.|
| clientSecret | *xxxxxxxxxxxxxx* | Het client geheim van de toepassing die toegang heeft tot bronnen in uw Tenant. |
| resourceGroupName | *testrg* | De naam van de resource groep die het cluster bevat.|
| clusterName | *mykustocluster* | De naam van uw cluster.|
| principalAssignmentName | *clusterPrincipalAssignment1* | De naam van de hoofd resource van uw cluster.|
| principalId | *xxxxxxxx-xxxxx-XXXX-XXXX-xxxxxxxxx* | De principal-ID, die gebruikers-e-mail, toepassings-ID of naam van beveiligings groep kan zijn.|
| role | *AllDatabasesAdmin* | De rol van de cluster-principal. Dit kan AllDatabasesAdmin' of AllDatabasesViewer zijn.|
| tenantIdForPrincipal | *xxxxxxxx-xxxxx-XXXX-XXXX-xxxxxxxxx* | De Tenant-ID van de principal.|
| principalType | *App* | Het type Principal, dat ' gebruiker ', ' app ' of ' Group ' kan zijn|

## <a name="next-steps"></a>Volgende stappen

* [Databaseprincipal toevoegen](database-principal-csharp.md)
