---
title: Data base-principals voor Azure Data Explorer toevoegen met behulp van python
description: In dit artikel leert u hoe u databaseprincipal kunt toevoegen voor Azure Data Explorer met behulp van python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/03/2020
ms.openlocfilehash: 8b9c4f4d5427b326c273558db0bff808068b192a
ms.sourcegitcommit: 42517355cc32890b1686de996c7913c98634e348
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/02/2020
ms.locfileid: "76965006"
---
# <a name="add-database-principals-for-azure-data-explorer-by-using-python"></a>Data base-principals voor Azure Data Explorer toevoegen met behulp van python

> [!div class="op_single_selector"]
> * [C#](database-principal-csharp.md)
> * [Python](database-principal-python.md)
> * [Azure Resource Manager-sjabloon](database-principal-resource-manager.md)

Azure Data Explorer is een snelle en zeer schaalbare service voor gegevensverkenning voor telemetrische gegevens en gegevens uit logboeken. In dit artikel voegt u de data base-principals voor Azure Data Explorer toe met behulp van python.

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.
* [Een cluster en data base maken](create-cluster-database-python.md)

## <a name="install-python-package"></a>Python-pakket installeren

Als u het python-pakket voor Azure Data Explorer (Kusto) wilt installeren, opent u een opdracht prompt met python in het pad. Voer deze opdracht uit:

```
pip install azure-common
pip install azure-mgmt-kusto
```

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-a-database-principal"></a>Een databaseprincipal toevoegen

In het volgende voor beeld ziet u hoe u een Data Base-Principal kunt toevoegen via een programma.

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

|**Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-XXXX-XXXX-xxxxxxxxx* | Uw Tenant-ID. Ook bekend als Directory-ID.|
| subscription_id | *xxxxxxxx-xxxxx-XXXX-XXXX-xxxxxxxxx* | De abonnements-ID die u gebruikt voor het maken van resources.|
| client_id | *xxxxxxxx-xxxxx-XXXX-XXXX-xxxxxxxxx* | De client-ID van de toepassing die toegang heeft tot bronnen in uw Tenant.|
| client_secret | *xxxxxxxxxxxxxx* | Het client geheim van de toepassing die toegang heeft tot bronnen in uw Tenant. |
| resource_group_name | *testrg* | De naam van de resource groep die het cluster bevat.|
| cluster_name | *mykustocluster* | De naam van uw cluster.|
| database_name | *mykustodatabase* | De naam van uw database.|
| principal_assignment_name | *databasePrincipalAssignment1* | De naam van de principal-resource van uw data base.|
| principal_id | *xxxxxxxx-xxxxx-XXXX-XXXX-xxxxxxxxx* | De principal-ID, die gebruikers-e-mail, toepassings-ID of naam van beveiligings groep kan zijn.|
| role | *Beheerder* | De rol van de databaseprincipal, die ' admin, ' opname functie ', ' Monitor ', ' gebruiker ', ' UnrestrictedViewers ', ' Viewer ' kan zijn.|
| tenant_id_for_principal | *xxxxxxxx-xxxxx-XXXX-XXXX-xxxxxxxxx* | De Tenant-ID van de principal.|
| principal_type | *App* | Het type Principal, dat ' gebruiker ', ' app ' of ' Group ' kan zijn|

## <a name="next-steps"></a>Volgende stappen

* [Gegevens opnemen met behulp van de Azure Data Explorer python-bibliotheek](python-ingest-data.md)
