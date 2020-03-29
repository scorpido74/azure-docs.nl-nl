---
title: Lees-replica's beheren - Azure CLI, REST API - Azure Database voor PostgreSQL - Single Server
description: Meer informatie over het beheren van leesreplica's in Azure Database voor PostgreSQL - Single Server vanuit de Azure CLI- en REST-API
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/23/2020
ms.openlocfilehash: b10ac3b4bc9dacd723b8b1265911df721b781189
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76774805"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Lees-replica's maken en beheren vanuit de Azure CLI, REST API

In dit artikel leert u hoe u leesreplica's maakt en beheert in Azure Database voor PostgreSQL met behulp van de Azure CLI- en REST-API. Zie het [overzicht](concepts-read-replicas.md)voor meer informatie over gelezen replica's.

## <a name="azure-cli"></a>Azure-CLI
U leesreplica's maken en beheren met de Azure CLI.

### <a name="prerequisites"></a>Vereisten

- [Installeer Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Een [Azure-database voor PostgreSQL-server](quickstart-create-server-up-azure-cli.md) als hoofdserver.


### <a name="prepare-the-master-server"></a>De hoofdserver voorbereiden
Deze stappen moeten worden gebruikt om een hoofdserver voor te bereiden in de lagen Algemeen Doel of Geheugengeoptimaliseerd.

De `azure.replication_support` parameter moet zijn ingesteld op **REPLICA** op de hoofdserver. Wanneer deze statische parameter wordt gewijzigd, is een serveropnieuw opstarten vereist om de wijziging van kracht te laten worden.

1. Ingesteld `azure.replication_support` op REPLICA.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

> [!NOTE]
> Als u de fout 'Ongeldige waarde gegeven' krijgt terwijl u azure.replication_support van de Azure CLI probeert in te stellen, is het waarschijnlijk dat uw server al standaard REPLICA heeft ingesteld. Een bug voorkomt dat deze instelling correct wordt weergegeven op nieuwere servers waar REPLICA de interne standaard is. <br><br>
> U de hoofdstappen voor bereiden overslaan en de replica maken. <br><br>
> Als u wilt bevestigen dat uw server zich in deze categorie bevindt, gaat u naar de replicatiepagina van de server in de Azure-portal. 'Replicatie uitschakelen' wordt grijs weergegeven en 'Replica toevoegen' is actief op de werkbalk.

2. Start de server opnieuw om de wijziging toe te passen.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Een gelezen replica maken

De opdracht voor het [maken van replica's voor az-postgres-server](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) vereist de volgende parameters:

| Instelling | Voorbeeldwaarde | Beschrijving  |
| --- | --- | --- |
| resource-group | myResourceGroup |  De brongroep waar de replicaserver wordt gemaakt.  |
| name | mydemoserver-replica | De naam van de nieuwe replicaserver die is gemaakt. |
| source-server | mydemoserver | De naam of bron-id van de bestaande hoofdserver om van te repliceren. |

In het onderstaande VOORBEELD CLI wordt de replica gemaakt in dezelfde regio als de master.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Als u een replica voor `--location` het gebied sinterland wilt maken, gebruikt u de parameter. In het onderstaande VOORBEELD CLI wordt de replica in West-US gemaakt.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Ga voor meer informatie over welke regio's u een replica maken in het [artikel Voor replicaconcepten](concepts-read-replicas.md). 

Als u de `azure.replication_support` parameter niet hebt ingesteld op **REPLICA** op een hoofdserver voor algemeen gebruik of geheugengeoptimaliseerden en de server opnieuw hebt opgestart, ontvangt u een fout. Voer deze twee stappen uit voordat u een replica maakt.

Er wordt een replica gemaakt met dezelfde reken- en opslaginstellingen als het stramien. Nadat een replica is gemaakt, kunnen verschillende instellingen onafhankelijk van de hoofdserver worden gewijzigd: rekengeneratie, vCores, opslag en back-upbewaarperiode. De prijscategorie kan ook onafhankelijk worden gewijzigd, behalve van of naar de basislaag.

> [!IMPORTANT]
> Voordat een hoofdserverinstelling wordt bijgewerkt naar een nieuwe waarde, werkt u de replica-instelling bij naar een evengrote of grotere waarde. Met deze actie kan de replica gelijke tred houden met eventuele wijzigingen in het model.

### <a name="list-replicas"></a>Replica's weergeven
U de lijst met replica's van een hoofdserver bekijken met de opdracht replicalijst van [az postgres server.](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list)

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Replicatie naar een replicaserver stoppen
U de replicatie tussen een hoofdserver en een gelezen replica stoppen met de opdracht [Replicastop voor AZ-postgresserver.](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop)

Nadat u de replicatie naar een hoofdserver en een gelezen replica hebt gestopt, kan deze niet ongedaan worden gemaakt. De gelezen replica wordt een standalone server die zowel leest als schrijft ondersteunt. De standalone server kan niet opnieuw worden omgezet in een replica.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-master-or-replica-server"></a>Een stramien- of replicaserver verwijderen
Als u een stramien- of replicaserver wilt verwijderen, gebruikt u de opdracht voor het verwijderen van de [AZ-postgres-server.](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete)

Wanneer u een hoofdserver verwijdert, wordt replicatie naar alle gelezen replica's gestopt. De gelezen replica's worden standalone servers die nu ondersteuning voor zowel leest en schrijft.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST API
U leesreplica's maken en beheren met de [Azure REST API.](/rest/api/azure/)

### <a name="prepare-the-master-server"></a>De hoofdserver voorbereiden
Deze stappen moeten worden gebruikt om een hoofdserver voor te bereiden in de lagen Algemeen Doel of Geheugengeoptimaliseerd.

De `azure.replication_support` parameter moet zijn ingesteld op **REPLICA** op de hoofdserver. Wanneer deze statische parameter wordt gewijzigd, is een serveropnieuw opstarten vereist om de wijziging van kracht te laten worden.

1. Ingesteld `azure.replication_support` op REPLICA.

   ```http
   PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

   ```JSON
   {
    "properties": {
    "value": "replica"
    }
   }
   ```

2. [Start de server opnieuw](/rest/api/postgresql/servers/restart) om de wijziging toe te passen.

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>Een gelezen replica maken
U een leesreplica maken met de [API maken:](/rest/api/postgresql/servers/create)

```http
PUT https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{replicaName}?api-version=2017-12-01
```

```json
{
  "location": "southeastasia",
  "properties": {
    "createMode": "Replica",
    "sourceServerId": "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}"
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
U de lijst met replica's van een hoofdserver bekijken met behulp van de [API voor replicalijst:](/rest/api/postgresql/replicas/listbyserver)

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Replicatie naar een replicaserver stoppen
U de replicatie tussen een hoofdserver en een gelezen replica stoppen met behulp van de [update-API.](/rest/api/postgresql/servers/update)

Nadat u de replicatie naar een hoofdserver en een gelezen replica hebt gestopt, kan deze niet ongedaan worden gemaakt. De gelezen replica wordt een standalone server die zowel leest als schrijft ondersteunt. De standalone server kan niet opnieuw worden omgezet in een replica.

```http
PATCH https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}?api-version=2017-12-01
```

```json
{
  "properties": {
    "replicationRole":"None"  
   }
}
```

### <a name="delete-a-master-or-replica-server"></a>Een stramien- of replicaserver verwijderen
Als u een stramien- of replicaserver wilt verwijderen, gebruikt u de [delete-API:](/rest/api/postgresql/servers/delete)

Wanneer u een hoofdserver verwijdert, wordt replicatie naar alle gelezen replica's gestopt. De gelezen replica's worden standalone servers die nu ondersteuning voor zowel leest en schrijft.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over [leesreplica's in Azure Database voor PostgreSQL](concepts-read-replicas.md).
* Meer informatie over het [maken en beheren van leesreplica's in de Azure-portal.](howto-read-replicas-portal.md)
