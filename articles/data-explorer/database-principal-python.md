---
title: Databaseprincipals voor Azure Data Explorer toevoegen met Python
description: In dit artikel leert u hoe u databaseprincipals voor Azure Data Explorer toevoegt met Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 8b9c4f4d5427b326c273558db0bff808068b192a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76965006"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-python"></a>Databaseprincipals voor Azure Data Explorer toevoegen met Python

> [!div class="op_single_selector"]
> * [C #](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Azure Resource Manager-sjabloon](database-principal-resource-manager.md)

Azure Data Explorer is een snelle en zeer schaalbare service voor gegevensverkenning voor telemetrische gegevens en gegevens uit logboeken. In dit artikel voegt u databaseprincipals voor Azure Data Explorer toe met Python.

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* [Een cluster en database maken](create-cluster-database-python.md)

## <a name="install-python-package"></a>Python-pakket installeren

Als u het Python-pakket voor Azure Data Explorer (Kusto) wilt installeren, opent u een opdrachtprompt met Python op zijn pad. Voer deze opdracht uit:

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Een databaseprincipal toevoegen

In het volgende voorbeeld ziet u hoe u een hoofdbestandman van een database programmatisch toevoegen.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabasePrincipalAssignment
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg"
#The cluster and database that is created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
principal_assignment_name = "clusterPrincipalAssignment1"
#User email, application ID, or security group name
principal_id = "xxxxxxxx"
#AllDatabasesAdmin or AllDatabasesViewer
role = "AllDatabasesAdmin"
tenant_id_for_principal = tenantId
#User, App, or Group
principal_type = "App"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.database_principal_assignments.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, principal_assignment_name= principal_assignment_name, parameters=DatabasePrincipalAssignment(principal_id=principal_id, role=role, tenant_id=tenant_id_for_principal, principal_type=principal_type))
```

|**Instelling** | **Voorgestelde waarde** | **Veldbeschrijving**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Je pacht-id. Ook wel directory ID genoemd.|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | De abonnements-ID die u gebruikt voor het maken van resources.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | De client-id van de toepassing die toegang heeft tot bronnen in uw tenant.|
| client_secret | *xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx* | Het clientgeheim van de toepassing die toegang heeft tot bronnen in uw tenant. |
| resource_group_name | *testrg* | De naam van de resourcegroep die uw cluster bevat.|
| cluster_name | *mykustocluster* | De naam van uw cluster.|
| Database_name | *mykustodatabase* | De naam van uw database.|
| principal_assignment_name | *databasePrincipalAssignment1* | De naam van uw database hoofdbron.|
| principal_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | De hoofd-ID, die kan worden gebruiker e-mail, applicatie-id, of beveiligingsgroep naam.|
| role | *Beheerder* | De rol van uw databaseprincipal, die 'Admin', 'Ingestor', 'Monitor', 'User', 'UnrestrictedViewers', 'Viewer' kan zijn.|
| tenant_id_for_principal | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | De huurder ID van de opdrachtgever.|
| principal_type | *App* | Het type principal, dat 'Gebruiker', 'App' of 'Groep' kan zijn|

## <a name="next-steps"></a>Volgende stappen

* [Gegevens opnemen met behulp van de Python-bibliotheek voor Azure Data Explorer](python-ingest-data.md)
