---
title: Data base-principals voor Azure Data Explorer toevoegen met behulp vanC#
description: In dit artikel leert u hoe u databaseprincipal kunt toevoegen voor Azure Data Explorer met behulp C#van.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 797d1253d44739f2026563e3df72bc85a8ef382e
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965032"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-c"></a>Data base-principals voor Azure Data Explorer toevoegen met behulp vanC#

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Azure Resource Manager-sjabloon](database-principal-resource-manager.md)

Azure Data Explorer is een snelle en zeer schaalbare service voor gegevensverkenning voor telemetrische gegevens en gegevens uit logboeken. In dit artikel voegt u de data base-principals voor Azure Data Explorer C#toe met behulp van.

## <a name="prerequisites"></a>Vereisten

* Als Visual Studio 2019 niet is geïnstalleerd, kunt u de **gratis** [Visual Studio 2019 Community Edition](https://www.visualstudio.com/downloads/)downloaden en gebruiken. Zorg ervoor dat u **Azure-ontwikkeling** inschakelt tijdens de installatie van Visual Studio.
* Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* [Een cluster en data base maken](create-cluster-database-csharp.md).

## <a name="install-c-nuget"></a>NuGet C# installeren

* Installeer [micro soft. Azure. Management. kusto](https://www.nuget.org/packages/Microsoft.Azure.Management.Kusto/).
* Installeer [micro soft. rest. ClientRuntime. Azure. Authentication](https://www.nuget.org/packages/Microsoft.Rest.ClientRuntime.Azure.Authentication) voor authenticatie.

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Een databaseprincipal toevoegen

In het volgende voor beeld ziet u hoe u een Data Base-Principal kunt toevoegen via een programma.

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

|**Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**|
|---|---|---|
| tenantId | *xxxxxxxx-xxxxx-XXXX-XXXX-xxxxxxxxx* | Uw Tenant-ID. Ook bekend als Directory-ID.|
| subscriptionId | *xxxxxxxx-xxxxx-XXXX-XXXX-xxxxxxxxx* | De abonnements-ID die u gebruikt voor het maken van resources.|
| clientId | *xxxxxxxx-xxxxx-XXXX-XXXX-xxxxxxxxx* | De client-ID van de toepassing die toegang heeft tot bronnen in uw Tenant.|
| clientSecret | *xxxxxxxxxxxxxx* | Het client geheim van de toepassing die toegang heeft tot bronnen in uw Tenant. |
| resourceGroupName | *testrg* | De naam van de resource groep die het cluster bevat.|
| clusterName | *mykustocluster* | De naam van uw cluster.|
| databaseName | *mykustodatabase* | De naam van uw database.|
| principalAssignmentName | *databasePrincipalAssignment1* | De naam van de principal-resource van uw data base.|
| principalId | *xxxxxxxx-xxxxx-XXXX-XXXX-xxxxxxxxx* | De principal-ID, die gebruikers-e-mail, toepassings-ID of naam van beveiligings groep kan zijn.|
| role | *Beheerder* | De rol van de databaseprincipal, die ' admin, ' opname functie ', ' Monitor ', ' gebruiker ', ' UnrestrictedViewers ', ' Viewer ' kan zijn.|
| tenantIdForPrincipal | *xxxxxxxx-xxxxx-XXXX-XXXX-xxxxxxxxx* | De Tenant-ID van de principal.|
| principalType | *App* | Het type Principal, dat ' gebruiker ', ' app ' of ' Group ' kan zijn|

## <a name="next-steps"></a>Volgende stappen

* [Gegevens opnemen met behulp van de knoop punt bibliotheek van Azure Data Explorer](node-ingest-data.md)
