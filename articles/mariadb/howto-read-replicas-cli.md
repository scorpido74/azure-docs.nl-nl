---
title: Lees-replica's beheren - Azure CLI, REST API - Azure Database voor MariaDB
description: In dit artikel wordt beschreven hoe u leesreplica's in Azure Database voor MariaDB instellen en beheren met behulp van de Azure CLI- en REST-API.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: abf80e98881b73bed53c5a939a79bc8b3a9de2db
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79530577"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli-and-rest-api"></a>Lees-replica's maken en beheren in Azure Database voor MariaDB met behulp van de Azure CLI- en REST-API

In dit artikel leert u hoe u leesreplica's maakt en beheert in de Azure Database for MariaDB-service met behulp van de Azure CLI- en REST-API.

## <a name="azure-cli"></a>Azure-CLI
U leesreplica's maken en beheren met de Azure CLI.

### <a name="prerequisites"></a>Vereisten

- [Installeer Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Een [Azure-database voor MariaDB-server](quickstart-create-mariadb-server-database-using-azure-portal.md) die wordt gebruikt als hoofdserver. 

> [!IMPORTANT]
> De functie leesreplica is alleen beschikbaar voor Azure Database voor MariaDB-servers in de prijzenlagen Algemeen Doel of Geheugengeoptimaliseerd. Zorg ervoor dat de hoofdserver zich in een van deze prijsniveaus bevindt.

### <a name="create-a-read-replica"></a>Een gelezen replica maken

Er kan een leesreplicaserver worden gemaakt met de volgende opdracht:

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

De `az mariadb server replica create` opdracht vereist de volgende parameters:

| Instelling | Voorbeeldwaarde | Beschrijving  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  De brongroep waarde replicaserver wordt gemaakt.  |
| name | mydemoreplicaserver | De naam van de nieuwe replicaserver die is gemaakt. |
| source-server | mydemoserver | De naam of id van de bestaande hoofdserver om van te repliceren. |

Als u een replica voor `--location` het gebied sinterland wilt maken, gebruikt u de parameter. 

> [!NOTE]
> Replicatie tussen regio's is in preview.

In het onderstaande VOORBEELD CLI wordt de replica in West-US gemaakt.

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Ga voor meer informatie over welke regio's u een replica maken in het [artikel Voor replicaconcepten](concepts-read-replicas.md). 

> [!NOTE]
> Leesreplica's worden gemaakt met dezelfde serverconfiguratie als de stramien. De configuratie van de replicaserver kan worden gewijzigd nadat deze is gemaakt. Het wordt aanbevolen dat de configuratie van de replicaserver op gelijke of grotere waarden wordt gehouden dan de master om ervoor te zorgen dat de replica het model kan bijhouden.

### <a name="list-replicas-for-a-master-server"></a>Replica's voor een hoofdserver weergeven

Voer de volgende opdracht uit om alle replica's voor een bepaalde hoofdserver weer te geven: 

```azurecli-interactive
az mariadb server replica list --server-name mydemoserver --resource-group myresourcegroup
```

De `az mariadb server replica list` opdracht vereist de volgende parameters:

| Instelling | Voorbeeldwaarde | Beschrijving  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  De brongroep waarde replicaserver wordt gemaakt.  |
| servernaam | mydemoserver | De naam of id van de hoofdserver. |

### <a name="stop-replication-to-a-replica-server"></a>Replicatie naar een replicaserver stoppen

> [!IMPORTANT]
> Het stoppen van replicatie naar een server is onomkeerbaar. Zodra replicatie is gestopt tussen een stramien en replica, kan deze niet ongedaan worden gemaakt. De replicaserver wordt dan een standalone server en ondersteunt nu zowel lezen als schrijven. Deze server kan niet opnieuw worden omgezet in een replica.

Replicatie naar een leesreplicaserver kan worden gestopt met de volgende opdracht:

```azurecli-interactive
az mariadb server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

De `az mariadb server replica stop` opdracht vereist de volgende parameters:

| Instelling | Voorbeeldwaarde | Beschrijving  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  De brongroep waarin de replicaserver bestaat.  |
| name | mydemoreplicaserver | De naam van de replicaserver om de replicatie te stoppen. |

### <a name="delete-a-replica-server"></a>Een replicaserver verwijderen

Het verwijderen van een lees-replicaserver kan worden gedaan door de opdracht **[az mariadb-server verwijderen uit te](/cli/azure/mariadb/server)** voeren.

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-master-server"></a>Een hoofdserver verwijderen

> [!IMPORTANT]
> Als u een hoofdserver verwijdert, wordt de replicatie naar alle replicaservers gestopt en wordt de hoofdserver zelf verwijderd. Replicaservers worden zelfstandige servers die nu zowel lees-als schrijfbewerkingen ondersteunen.

Als u een hoofdserver wilt verwijderen, u de opdracht voor het verwijderen van de **[AZ-mariadb-server uitvoeren.](/cli/azure/mariadb/server)**

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="rest-api"></a>REST API
U leesreplica's maken en beheren met de [Azure REST API.](/rest/api/azure/)

### <a name="create-a-read-replica"></a>Een gelezen replica maken
U een leesreplica maken met de [API maken:](/rest/api/mariadb/servers/create)

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}"
  }
}
```

> [!NOTE]
> Ga voor meer informatie over welke regio's u een replica maken in het [artikel Voor replicaconcepten](concepts-read-replicas.md). 

Als u de `azure.replication_support` parameter niet hebt ingesteld op **REPLICA** op een hoofdserver voor algemeen gebruik of geheugengeoptimaliseerden en de server opnieuw hebt opgestart, ontvangt u een fout. Voer deze twee stappen uit voordat u een replica maakt.

Er wordt een replica gemaakt met dezelfde reken- en opslaginstellingen als het stramien. Nadat een replica is gemaakt, kunnen verschillende instellingen onafhankelijk van de hoofdserver worden gewijzigd: rekengeneratie, vCores, opslag en back-upbewaarperiode. De prijscategorie kan ook onafhankelijk worden gewijzigd, behalve van of naar de basislaag.


> [!IMPORTANT]
> Voordat een hoofdserverinstelling wordt bijgewerkt naar een nieuwe waarde, werkt u de replica-instelling bij naar een evengrote of grotere waarde. Met deze actie kan de replica gelijke tred houden met eventuele wijzigingen in het model.

### <a name="list-replicas"></a>Replica's weergeven
U de lijst met replica's van een hoofdserver bekijken met behulp van de [API voor replicalijst:](/rest/api/mariadb/replicas/listbyserver)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Replicatie naar een replicaserver stoppen
U de replicatie tussen een hoofdserver en een gelezen replica stoppen met behulp van de [update-API.](/rest/api/mariadb/servers/update)

Nadat u de replicatie naar een hoofdserver en een gelezen replica hebt gestopt, kan deze niet ongedaan worden gemaakt. De gelezen replica wordt een standalone server die zowel leest als schrijft ondersteunt. De standalone server kan niet opnieuw worden omgezet in een replica.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>Een stramien- of replicaserver verwijderen
Als u een stramien- of replicaserver wilt verwijderen, gebruikt u de [delete-API:](/rest/api/mariadb/servers/delete)

Wanneer u een hoofdserver verwijdert, wordt replicatie naar alle gelezen replica's gestopt. De gelezen replica's worden standalone servers die nu ondersteuning voor zowel leest en schrijft.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [gelezen replica's](concepts-read-replicas.md)
