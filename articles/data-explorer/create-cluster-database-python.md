---
title: Een Azure Data Explorer-cluster en-data base maken met behulp van python
description: Meer informatie over het maken van een Azure Data Explorer-cluster en-data base met behulp van python.
author: oflipman
ms.author: oflipman
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 06/03/2019
ms.openlocfilehash: ef0001059026421584efde4c165e882197eda7a6
ms.sourcegitcommit: 3f22ae300425fb30be47992c7e46f0abc2e68478
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71266229"
---
# <a name="create-an-azure-data-explorer-cluster-and-database-by-using-python"></a>Een Azure Data Explorer-cluster en-data base maken met behulp van python

> [!div class="op_single_selector"]
> * [Portal](create-cluster-database-portal.md)
> * [CLI](create-cluster-database-cli.md)
> * [PowerShell](create-cluster-database-powershell.md)
> * [C#](create-cluster-database-csharp.md)
> * [Python](create-cluster-database-python.md)
>  

Azure Data Explorer is een snelle, volledig beheerde service voor gegevensanalyses waarmee grote hoeveelheden gegevens van toepassingen, websites, IoT-apparaten en dergelijke in real-time kunnen worden geanalyseerd. Als u Azure Data Explorer wilt gebruiken, maakt u eerst een cluster. Daarna maakt u een of meer databases in het cluster. De volgende stap is het opnemen (laden) van gegevens in een database, zodat u er query's op kunt uitvoeren. In dit artikel maakt u een cluster en een Data Base met behulp van python.

## <a name="prerequisites"></a>Vereisten

Als u nog geen abonnement op Azure hebt, maak dan een [gratis Azure-account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="install-python-package"></a>Python-pakket installeren

Als u het python-pakket voor Azure Data Explorer (Kusto) wilt installeren, opent u een opdracht prompt met python in het pad. Voer deze opdracht uit:

```
pip install azure-mgmt-kusto
pip install adal
pip install msrestazure
```

## <a name="create-the-azure-data-explorer-cluster"></a>Het Azure Data Explorer-cluster maken

1. Maak uw cluster met behulp van de volgende opdracht:

    ```Python
    from azure.mgmt.kusto.kusto_management_client import KustoManagementClient
    from azure.mgmt.kusto.models import Cluster, AzureSku
    from adal import AuthenticationContext
    from msrestazure.azure_active_directory import AdalAuthentication

    tenant_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    client_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    client_secret = "xxxxxxxxxxxxxx"
    subscription_id = "xxxxxxxx-xxxxx-xxxx-xxxx-xxxxxxxxx"
    context = AuthenticationContext('https://login.microsoftonline.com/{}'.format(tenant_id))
    credentials = AdalAuthentication(context.acquire_token_with_client_credentials,
                                         resource="https://management.core.windows.net/",
                                         client_id=client_id,
                                         client_secret=client_secret)

    location = 'Central US'
    sku = 'D13_v2'
    capacity = 5
    resource_group_name = 'testrg'
    cluster_name = 'mykustocluster'
    cluster = Cluster(location=location, sku=AzureSku(name=sku, capacity=capacity))
    
    kustoManagementClient = KustoManagementClient(credentials, subscription_id)
    
    cluster_operations = kustoManagementClient.clusters
    
    cluster_operations.create_or_update(resource_group_name, cluster_name, cluster)
    ```

   |**Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**|
   |---|---|---|
   | cluster_name | *mykustocluster* | De gewenste naam van uw cluster.|
   | sku | *D13_v2* | De SKU die wordt gebruikt voor uw cluster. |
   | resource_group_name | *testrg* | Naam van de resourcegroep waar het cluster wordt gemaakt. |

    Er zijn aanvullende optionele parameters die u kunt gebruiken, zoals de capaciteit van het cluster.
    
1. [ *Uw referenties* instellen](/azure/python/python-sdk-azure-authenticate)

1. Voer de volgende opdracht uit om te controleren of het cluster is gemaakt:

    ```Python
    cluster_operations.get(resource_group_name = resource_group_name, cluster_name= clusterName, custom_headers=None, raw=False)
    ```

Als het resultaat `provisioningState` met waarde `Succeeded` bevat, is het maken van het cluster geslaagd.

## <a name="create-the-database-in-the-azure-data-explorer-cluster"></a>De database in het Azure Data Explorer-cluster maken

1. Maak uw database met behulp van de volgende opdracht:

    ```Python
    from azure.mgmt.kusto.models import Database
    from datetime import timedelta
    
    softDeletePeriod = timedelta(days=3650)
    hotCachePeriod = timedelta(days=3650)
    databaseName="mykustodatabase"
    
    database_operations = kusto_management_client.databases 
    _database = Database(location=location,
                        soft_delete_period=softDeletePeriod,
                        hot_cache_period=hotCachePeriod)
    
    database_operations.create_or_update(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName, parameters = _database)
    ```

   |**Instelling** | **Voorgestelde waarde** | **Beschrijving van veld**|
   |---|---|---|
   | cluster_name | *mykustocluster* | De naam van het cluster waar de database wordt gemaakt.|
   | database | *mykustodatabase* | De naam van uw database.|
   | resource_group_name | *testrg* | Naam van de resourcegroep waar het cluster wordt gemaakt. |
   | soft_delete_period | *3650 dagen, 0:00:00* | Hoe lang gegevens worden opgeslagen om query's erop te kunnen uitvoeren. |
   | hot_cache_period | *3650 dagen, 0:00:00* | Hoe lang gegevens worden opgeslagen in de cache. |

1. Voer de volgende opdracht uit om de database te bekijken die u hebt gemaakt:

    ```Python
    database_operations.get(resource_group_name = resource_group_name, cluster_name = clusterName, database_name = databaseName)
    ```

U hebt nu een cluster en een database.

## <a name="clean-up-resources"></a>Resources opschonen

* Als u van plan bent om onze andere artikelen te volgen, moet u de resources die u hebt gemaakt, blijven gebruiken.
* Als u resources wilt opschonen, moet u het cluster verwijderen. Wanneer u een cluster verwijdert, worden alle databases hierin ook verwijderd. Gebruik de volgende opdracht om uw cluster te verwijderen:

    ```Python
    cluster_operations.delete(resource_group_name = resource_group_name, cluster_name = clusterName)
    ```

## <a name="next-steps"></a>Volgende stappen

* [Gegevens opnemen met behulp van de Azure Data Explorer python-bibliotheek](python-ingest-data.md)
