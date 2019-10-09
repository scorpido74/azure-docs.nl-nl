---
title: Een event hub-gegevens verbinding maken voor Azure Data Explorer met behulp van python
description: In dit artikel leert u hoe u een event hub-gegevens verbinding voor Azure Data Explorer maakt met behulp van python.
author: lucygoldbergmicrosoft
ms.author: lugoldbe
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 10/07/2019
ms.openlocfilehash: b69cdb1ee04e3824bf5fd20a7db2401161fdf6e7
ms.sourcegitcommit: 11265f4ff9f8e727a0cbf2af20a8057f5923ccda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/08/2019
ms.locfileid: "72031667"
---
# <a name="create-an-event-hub-data-connection-for-azure-data-explorer-by-using-python"></a>Een event hub-gegevens verbinding maken voor Azure Data Explorer met behulp van python

> [!div class="op_single_selector"]
> * [Portal](ingest-data-event-hub.md)
> * [C#](data-connection-event-hub-csharp.md)
> * [Python](data-connection-event-hub-python.md)

Azure Data Explorer is een snelle en zeer schaalbare service om gegevens in logboeken en telemetrie te verkennen. Azure Data Explorer biedt opname (gegevens laden) van Event Hubs, IoT hubs en blobs die zijn geschreven naar BLOB-containers. In dit artikel maakt u een event hub-gegevens verbinding voor Azure Data Explorer met behulp van python.

## <a name="prerequisites"></a>Vereisten

* Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.

* [Een cluster en data base](create-cluster-database-csharp.md) maken

* [Tabel-en kolom toewijzing](net-standard-ingest-data.md#create-a-table-on-your-test-cluster) maken

* [Data Base-en tabel beleid](database-table-policies-csharp.md) instellen (optioneel)

* Een [Event hub maken met gegevens voor opname](ingest-data-event-hub.md#create-an-event-hub). 

[!INCLUDE [data-explorer-data-connection-install-package-python](../../includes/data-explorer-data-connection-install-package-python.md)]

[!INCLUDE [data-explorer-authentication](../../includes/data-explorer-authentication.md)]

## <a name="add-an-event-hub-data-connection"></a>Een event hub-gegevens verbinding toevoegen

In het volgende voor beeld ziet u hoe u een event hub-gegevens verbinding programmatisch kunt toevoegen. Zie [verbinding maken met de Event hub](ingest-data-event-hub.md#connect-to-the-event-hub) voor het toevoegen van een event hub-gegevens verbinding met behulp van de Azure Portal.

```Python
from azure.mgmt.kusto import KustoManagementClient
from azure.mgmt.kusto.models import EventHubDataConnection
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

resource_group_name = "testrg";
#The cluster and database that are created as part of the Prerequisites
cluster_name = "mykustocluster";
database_name = "mykustodatabase";
data_connection_name = "myeventhubconnect";
#The event hub that is created as part of the Prerequisites
event_hub_resource_id = "/subscriptions/xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx/resourceGroups/xxxxxx/providers/Microsoft.EventHub/namespaces/xxxxxx/eventhubs/xxxxxx";
consumer_group = "$Default";
location = "Central US";
#The table and column mapping that are created as part of the Prerequisites
table_name = "StormEvents";
mapping_rule_name = "StormEvents_CSV_Mapping";
data_format = "csv";
#Returns an instance of LROPoller, check https://docs.microsoft.com/python/api/msrest/msrest.polling.lropoller?view=azure-python
poller = kusto_management_client.data_connections.create_or_update(resource_group_name=resource_group_name, cluster_name=cluster_name, database_name=database_name, data_connection_name=data_connection_name,
                                        parameters=EventHubDataConnection(event_hub_resource_id=event_hub_resource_id, consumer_group=consumer_group, location=location,
                                                                            table_name=table_name, mapping_rule_name=mapping_rule_name, data_format=data_format))
```

|**Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**|
|---|---|---|
| tenant_id | *xxxxxxxx-xxxxx-XXXX-XXXX-xxxxxxxxx* | Uw Tenant-ID. Ook bekend als Directory-ID.|
| subscriptionId | *xxxxxxxx-xxxxx-XXXX-XXXX-xxxxxxxxx* | De abonnements-ID die u gebruikt voor het maken van resources.|
| client_id | *xxxxxxxx-xxxxx-XXXX-XXXX-xxxxxxxxx* | De client-ID van de toepassing die toegang heeft tot bronnen in uw Tenant.|
| client_secret | *xxxxxxxxxxxxxx* | Het client geheim van de toepassing die toegang heeft tot bronnen in uw Tenant. |
| resource_group_name | *testrg* | De naam van de resource groep die het cluster bevat.|
| cluster_name | *mykustocluster* | De naam van uw cluster.|
| database | *mykustodatabase* | De naam van de doel database in uw cluster.|
| data_connection_name | *myeventhubconnect* | De gewenste naam van uw gegevens verbinding.|
| table_name | *StormEvents* | De naam van de doel tabel in de doel database.|
| mapping_rule_name | *StormEvents_CSV_Mapping* | De naam van de kolom toewijzing die is gerelateerd aan de doel tabel.|
| data_format | *bestand* | De gegevens indeling van het bericht.|
| event_hub_resource_id | *Resource-ID* | De resource-ID van uw event hub die de gegevens bevat voor opname. |
| consumer_group | *$Default* | De consumenten groep van uw event hub.|
| location | *US - centraal* | De locatie van de bron van de gegevens verbinding.|

[!INCLUDE [data-explorer-data-connection-clean-resources-python](../../includes/data-explorer-data-connection-clean-resources-python.md)]