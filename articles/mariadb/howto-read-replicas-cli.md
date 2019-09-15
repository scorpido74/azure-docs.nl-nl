---
title: Lees replica's maken en beheren in Azure Database for MariaDB-AZURE CLI, REST API
description: In dit artikel wordt beschreven hoe u in Azure Database for MariaDB Lees replica's instelt en beheert met behulp van de Azure CLI en REST API.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/13/2019
ms.openlocfilehash: 2f5029ccbf80551721ecc363aa4c3930961d9154
ms.sourcegitcommit: 1752581945226a748b3c7141bffeb1c0616ad720
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/14/2019
ms.locfileid: "70993062"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli-and-rest-api"></a>Lees replica's maken en beheren in Azure Database for MariaDB met behulp van de Azure CLI en REST API

In dit artikel wordt beschreven hoe u in de Azure Database for MariaDB-service Lees replica's maakt en beheert met behulp van de Azure CLI en REST API.

## <a name="azure-cli"></a>Azure-CLI
U kunt met behulp van de Azure CLI Lees replica's maken en beheren.

### <a name="prerequisites"></a>Vereisten

- [Installeer Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Een [Azure database for MariaDB-server](quickstart-create-mariadb-server-database-using-azure-portal.md) die wordt gebruikt als de hoofd server. 

> [!IMPORTANT]
> De functie voor het lezen van replica's is alleen beschikbaar voor Azure Database for MariaDB-servers in de prijs Categorieën Algemeen of geoptimaliseerd voor geheugen. Controleer of de hoofd-server in een van deze Prijscategorieën.

### <a name="create-a-read-replica"></a>Maken van een replica lezen

Een lezen-replica-server kan worden gemaakt met de volgende opdracht:

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup
```

De `az mariadb server replica create` opdracht moet de volgende parameters:

| Instelling | Voorbeeldwaarde | Description  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  De resourcegroep waar de replica-server om te worden gemaakt.  |
| naam | mydemoreplicaserver | De naam van de nieuwe replicaserver die is gemaakt. |
| source-server | mydemoserver | De naam of ID van de bestaande hoofd-server om te repliceren van. |

Gebruik de `--location` para meter om een lees replica te maken. In het CLI-voor beeld hieronder wordt de replica in VS West gemaakt.

```azurecli-interactive
az mariadb server replica create --name mydemoreplicaserver --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Ga naar het [artikel concepten van replica's lezen](concepts-read-replicas.md)voor meer informatie over de regio's die u kunt maken in de replica. 

> [!NOTE]
> Lezen-replica's worden gemaakt met de configuratie van de dezelfde server als de master. De configuratie van de replica-server kan worden gewijzigd nadat deze is gemaakt. Het wordt aanbevolen dat de configuratie van de replica-server moet worden opgeslagen op de waarden gelijk zijn aan of groter zijn dan het model om te controleren of dat de replica kan houden met de master.

### <a name="list-replicas-for-a-master-server"></a>Lijst met replica's voor een hoofd-server

Als u wilt weergeven van alle replica's voor een bepaalde hoofd-server, moet u de volgende opdracht uitvoeren: 

```azurecli-interactive
az mariadb server replica list --server-name mydemoserver --resource-group myresourcegroup
```

De `az mariadb server replica list` opdracht moet de volgende parameters:

| Instelling | Voorbeeldwaarde | Description  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  De resourcegroep waar de replica-server om te worden gemaakt.  |
| servernaam | mydemoserver | De naam of ID van de hoofd-server. |

### <a name="stop-replication-to-a-replica-server"></a>Replicatie naar een replica-server stoppen

> [!IMPORTANT]
> Replicatie naar een server stoppen is niet ongedaan worden gemaakt. Wanneer u replicatie tussen een model en de replica is gestopt, kunnen deze kan niet ongedaan worden gemaakt. De replica-server vervolgens wordt een zelfstandige server en biedt nu ondersteuning voor zowel lees- en schrijfbewerkingen. Deze server kan niet opnieuw worden gemaakt in een replica.

Replicatie naar een lezen-replica-server kan worden gestopt met de volgende opdracht:

```azurecli-interactive
az mariadb server replica stop --name mydemoreplicaserver --resource-group myresourcegroup
```

De `az mariadb server replica stop` opdracht moet de volgende parameters:

| Instelling | Voorbeeldwaarde | Description  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  De resourcegroep waarin de replica-server bestaat.  |
| naam | mydemoreplicaserver | De naam van de replica-server om replicatie te stoppen op. |

### <a name="delete-a-replica-server"></a>Een replica-server verwijderen

Het verwijderen van een lees replica-server kan worden uitgevoerd door de opdracht **[AZ mariadb server delete](/cli/azure/mariadb/server)** uit te voeren.

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

### <a name="delete-a-master-server"></a>Een hoofd-server verwijderen

> [!IMPORTANT]
> Verwijderen van een hoofd-server-replicatie naar alle replicaservers stopt en Hiermee verwijdert u de hoofd-server zelf. Replica-servers worden zelfstandige servers die bieden nu ondersteuning voor zowel lees- en schrijfbewerkingen.

Als u een master server wilt verwijderen, kunt u de opdracht **[AZ mariadb server delete](/cli/azure/mariadb/server)** uitvoeren.

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="rest-api"></a>REST-API
U kunt met behulp van de [Azure rest API](/rest/api/azure/)Lees replica's maken en beheren.

### <a name="create-a-read-replica"></a>Maken van een replica lezen
U kunt een lees replica maken met behulp van de [API Create](/rest/api/mariadb/servers/create):

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
> Ga naar het [artikel concepten van replica's lezen](concepts-read-replicas.md)voor meer informatie over de regio's die u kunt maken in de replica. 

Als u de `azure.replication_support` para meter niet op **replica** hebt ingesteld op een master server met algemeen of geoptimaliseerd voor geheugen en de server opnieuw hebt opgestart, wordt een fout bericht weer gegeven. Voer de volgende twee stappen uit voordat u een replica maakt.

Een replica wordt gemaakt met behulp van dezelfde berekenings-en opslag instellingen als de hoofd server. Nadat een replica is gemaakt, kunnen verschillende instellingen onafhankelijk van de hoofd server worden gewijzigd: generatie van compute, vCores, opslag en back-up van Bewaar periode. De prijs categorie kan ook onafhankelijk worden gewijzigd, met uitzonde ring van of van de Basic-laag.


> [!IMPORTANT]
> Werk de replica-instelling bij naar een gelijke of grotere waarde voordat een master server-instelling wordt bijgewerkt naar een nieuwe waarde. Met deze actie wordt de replica zo aangepast dat er wijzigingen in de master worden aangebracht.

### <a name="list-replicas"></a>Replica's weer geven
U kunt de lijst met replica's van een hoofd server weer geven met behulp van de [replica lijst-API](/rest/api/mariadb/replicas/listbyserver):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Replicatie naar een replica-server stoppen
U kunt de replicatie tussen een hoofd server en een lees replica stoppen met de [Update-API](/rest/api/mariadb/servers/update).

Nadat u de replicatie naar een hoofd server en een lees replica hebt gestopt, kunt u deze niet meer ongedaan maken. De Lees replica wordt een zelfstandige server die zowel lees-als schrijf bewerkingen ondersteunt. De zelfstandige server kan niet opnieuw in een replica worden gemaakt.

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

### <a name="delete-a-master-or-replica-server"></a>Een Master-of replica server verwijderen
Als u een Master-of replica server wilt verwijderen, gebruikt u de [API verwijderen](/rest/api/mariadb/servers/delete):

Wanneer u een master-server verwijdert, wordt de replicatie naar alle Lees replica's gestopt. De Lees replica's worden zelfstandige servers die nu zowel lees-als schrijf bewerkingen ondersteunen.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforMariaDB/servers/{serverName}?api-version=2017-12-01
```


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [lezen replica's](concepts-read-replicas.md)
