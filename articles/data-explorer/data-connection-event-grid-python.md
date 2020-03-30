---
title: Een gebeurtenisrastergegevensverbinding voor Azure Data Explorer maken met Python
description: In dit artikel leert u hoe u een gebeurtenisrastergegevensverbinding voor Azure Data Explorer maakt met Python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: 1439383598517f57bc77e718d4ded7f53941d3bb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77444193"
---
# <a name="create-an-event-grid-data-connection-for-azure-data-explorer-by-using-python"></a>Een gebeurtenisrastergegevensverbinding voor Azure Data Explorer maken met Python

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-grid.md)
> * [C #](data-connection-event-grid-csharp.md)
> * [Python](data-connection-event-grid-python.md)
> * [Azure Resource Manager-sjabloon](data-connection-event-grid-resource-manager.md)

In dit artikel maakt u een eventgrid-gegevensverbinding voor Azure Data Explorer met Python. Azure Data Explorer is een snelle en zeer schaalbare service voor gegevensverkenning voor telemetrische gegevens en gegevens uit logboeken. Azure Data Explorer biedt opname of het laden van gegevens vanuit Gebeurtenishubs, IoT-hubs en blobs die zijn geschreven naar blobcontainers.

## <a name="prerequisites"></a>Vereisten

* Een Azure-account met een actief abonnement. [Maak er gratis een.](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio)

* [Python 3.4+](https://www.python.org/downloads/).

* [Een cluster en database](create-cluster-database-python.md).

* [Tabel- en kolomtoewijzing](net-standard-ingest-data.md#create-a-table-on-your-test-cluster).

* [Database- en tabelbeleid](database-table-policies-csharp.md) (optioneel).

* [Een opslagaccount met een Event Grid-abonnement](ingest-data-event-grid.md#create-an-event-grid-subscription-in-your-storage-account).

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-grid-data-connection"></a>Een gegevensverbinding voor gebeurtenisraster toevoegen

In het volgende voorbeeld ziet u hoe u een gebeurtenisrastergegevensverbinding programmatisch toevoegt. Zie [een eventgrid-gegevensverbinding maken in Azure Data Explorer](ingest-data-event-grid.md#create-an-event-grid-data-connection-in-azure-data-explorer) voor het toevoegen van een gebeurtenisrastergegevensverbinding via de Azure-portal.


```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventGridDataConnection
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
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster"
database_name = "mykustodatabase"
data_connection_name = "myeventhubconnect"
#The event hub and storage account that are created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx"
storage_account_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.Storage/storageAccounts/xxxxxx"
consumer_group = "$Default"
location = "Central US"
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents"
mapping_rule_name = "StormEvents_CSV_Mapping"
data_format = "csv"

#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                            parameters=EventGridDataConnection(storage_account_resource_id=storage_account_resource_id, event_hub_resource_id=event_hub_resource_id, 
                                                                                consumer_group=consumer_group, table_name=table_name, location=location, mapping_rule_name=mapping_rule_name, data_format=data_format))
```
|**Instelling** | **Voorgestelde waarde** | **Veldbeschrijving**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | Je pacht-id. Ook wel directory ID genoemd.|
| subscription_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | De abonnements-ID die u gebruikt voor het maken van resources.|
| client_id | *xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx* | De client-id van de toepassing die toegang heeft tot bronnen in uw tenant.|
| client_secret | *xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx* | Het clientgeheim van de toepassing die toegang heeft tot bronnen in uw tenant. |
| resource_group_name | *testrg* | De naam van de resourcegroep die uw cluster bevat.|
| cluster_name | *mykustocluster* | De naam van uw cluster.|
| Database_name | *mykustodatabase* | De naam van de doeldatabase in uw cluster.|
| data_connection_name | *myeventhubconnect* | De gewenste naam van uw gegevensverbinding.|
| Table_name | *StormEvenementen* | De naam van de doeltabel in de doeldatabase.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | De naam van uw kolomtoewijzing met betrekking tot de doeltabel.|
| data_format | *Csv* | De gegevensindeling van het bericht.|
| event_hub_resource_id | *Resource-id* | De resource-id van uw gebeurtenishub waar het gebeurtenisraster is geconfigureerd om gebeurtenissen te verzenden. |
| storage_account_resource_id | *Resource-id* | De bron-ID van uw opslagaccount die de gegevens bevat voor opname. |
| consumer_group | *$Default* | De consumentengroep van uw Event Hub.|
| location | *VS - centraal* | De locatie van de bron voor gegevensverbindingen.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]