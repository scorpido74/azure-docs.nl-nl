---
title: 'Beleidsregels maken voor azure data explorer-cluster en -database met de Python-bibliotheek van Azure Data Explorer '
description: In dit artikel leert u hoe u beleid maakt met Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2019
ms.openlocfilehash: a0fe86e2dcb802b822cb08ed0922b5da9c5cfd1c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74667277"
---
# <a name="create-database-and-table-policies-for-azure-data-explorer-by-using-python"></a>Database- en tabelbeleid voor Azure Data Explorer maken met Python

> [!div class="op_single_selector"]
> * [C #](database-table-policies-csharp.md)
> * [Python](database-table-policies-python.md)
>

Azure Data Explorer is een snelle en zeer schaalbare service voor gegevensverkenning voor telemetrische gegevens en gegevens uit logboeken. In dit artikel maakt u database- en tabelbeleid voor Azure Data Explorer met Python.

## <a name="prerequisites"></a>Vereisten

* Als u geen Azure-abonnement hebt, maakt u een [gratis Azure-account](https://azure.microsoft.com/free/) voordat u begint.
* [Een cluster en database voor testdoeleinden](create-cluster-database-python.md)
* [Een testtabel](python-ingest-data.md#create-a-table-on-your-cluster)

## <a name="install-the-data-libraries"></a>De gegevensbibliotheken installeren

```
pip install azure-common
pip install azure-mgmt-kusto
pip install azure-kusto-data (Optional, for changing table's policies)
```

## <a name="authentication"></a>Authentication
Voor het uitvoeren van de voorbeelden in dit artikel hebben we een Azure AD-toepassing en serviceprincipal nodig die toegang heeft tot bronnen. U dezelfde Azure AD-toepassing gebruiken voor verificatie vanuit [een testcluster en -database.](create-cluster-database-csharp.md#authentication) Zie [Een Azure AD-toepassing maken](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal) om een gratis Azure AD-toepassing te maken en roltoewijzing toe te voegen aan het abonnementsbereik als u een andere Azure AD-toepassing wilt gebruiken. Het laat ook zien `Directory (tenant) ID` `Application ID`hoe `Client Secret`je de , , en . Mogelijk moet u de nieuwe Azure AD-toepassing als hoofd in de database toevoegen, zie [Machtigingen voor Azure Data Explorer-database beheren.](https://docs.microsoft.com/azure/data-explorer/manage-database-permissions)    

## <a name="alter-database-retention-policy"></a>Beleid voor het bewaren van gegevens wijzigen
Hiermee stelt u een bewaarbeleid in met een soft-delete-periode van 10 dagen.

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabaseUpdate
from azure.common.credentials import ServicePrincipalCredentials
import datetime

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

resource_group_name = "testrg";
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster";
database_name = "mykustodatabase";

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kustoManagementClient.databases.update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name,
                                           parameters=DatabaseUpdate(soft_delete_period=datetime.timedelta(days=10)))
```

## <a name="alter-database-cache-policy"></a>Databasecachebeleid wijzigen
Hiermee stelt u een cachebeleid in voor de database die de laatste vijf dagen van gegevens op de cluster-SSD staan.

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabaseUpdate
from azure.common.credentials import ServicePrincipalCredentials
import datetime

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

resource_group_name = "testrg";
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster";
database_name = "mykustodatabase";

#Returns an instance of LROPoller, see https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kustoManagementClient.databases.update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name,
                                           parameters=DatabaseUpdate(hot_cache_period=datetime.timedelta(days=5)))
```

## <a name="alter-table-cache-policy"></a>Beleid voor tabelcache wijzigen
Hiermee stelt u een cachebeleid in voor de tabel die de afgelopen vijf dagen aan gegevens op de cluster-SSD staan.

```python
from azure.kusto.data.request import KustoClient, KustoConnectionStringBuilder
kusto_uri = "https://<ClusterName>.<Region>.kusto.windows.net"
database_name = "<DatabaseName>"
#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
#Application ID
client_id_to_add = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";

kusto_connection_string_builder = KustoConnectionStringBuilder.with_aad_application_key_authentication(connection_string=kusto_uri, aad_app_id=client_id, app_key=client_secret, authority_id=tenant_id)

#The table that is created as part of the Prerequisites
table_name = "<TableName>"
caching_policy = r'hot = 5d'
command = '.alter table {} policy caching '.format(table_name) +  caching_policy
kusto_client.execute_mgmt(database_name, command)
```

## <a name="add-a-new-principal-for-database"></a>Een nieuw hoofdvoor database toevoegen
Een nieuwe Azure AD-toepassing toevoegen als beheerdersprincipal voor de database

```python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import DatabasePrincipal
from azure.common.credentials import ServicePrincipalCredentials

#Directory (tenant) ID
tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Application ID
client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
#Client Secret
client_secret = "xxxxxxxxxxxxxx"
#Application ID
client_id_to_add = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx";
subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
credentials = ServicePrincipalCredentials(
        client_id=client_id,
        secret=client_secret,
        tenant=tenant_id
    )
kusto_management_client = KustoManagementClient(credentials, subscription_id)

resource_group_name = "testrg";
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster";
database_name = "mykustodatabase";
role = "Admin"
principle_name = "<database_principle_name>";
type_name = "App"
kustoManagementClient.databases.add_principals(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name,
                         value=[DatabasePrincipal(role=role, name=principle_name, type=type_name, app_id=client_id_to_add, tenant_name=tenant_id)])
```

## <a name="next-steps"></a>Volgende stappen

* [Lees meer over database- en tabelbeleid](https://docs.microsoft.com/azure/kusto/management/policies)
