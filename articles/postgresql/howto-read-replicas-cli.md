---
title: Lees replica's beheren-Azure CLI, REST API-Azure Database for PostgreSQL-één server
description: Meer informatie over het beheren van Lees replica's in Azure Database for PostgreSQL-één server van de Azure CLI en REST API
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 07/10/2020
ms.openlocfilehash: de74258cadcdf81da211561a84ff06927830e690
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/11/2020
ms.locfileid: "86274404"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli-rest-api"></a>Maak en beheer Lees replica's vanuit Azure CLI, REST API

In dit artikel leert u hoe u in Azure Database for PostgreSQL Lees replica's maakt en beheert met behulp van de Azure CLI en REST API. Zie het [overzicht](concepts-read-replicas.md)voor meer informatie over het lezen van replica's.

## <a name="azure-replication-support"></a>Ondersteuning voor Azure-replicatie
Het [lezen van replica's](concepts-read-replicas.md) en [logische decodering](concepts-logical.md) is beide afhankelijk van het post gres-logboek (write-Ahead) (Wal) voor informatie. Deze twee functies hebben verschillende niveaus van logboek registratie nodig van post gres. Voor logische decodering is een hoger niveau van logboek registratie vereist dan bij het lezen van replica's.

Als u het juiste niveau van logboek registratie wilt configureren, gebruikt u de Azure Replication support-para meter. Ondersteuning voor Azure-replicatie heeft drie instellings opties:

* **Uit** : Hiermee wordt de minste informatie in de wal geplaatst. Deze instelling is niet beschikbaar op de meeste Azure Database for PostgreSQL-servers.  
* **Replica** : uitgebreidere dan **uit**. Dit is het minimale registratie niveau dat nodig is voor het werken met [replica's](concepts-read-replicas.md) . Deze instelling is de standaard waarde op de meeste servers.
* **Logisch** -uitgebreidere dan de **replica**. Dit is het minimale registratie niveau voor het werken met logische code ring. Het lezen van replica's werkt ook bij deze instelling.

De server moet opnieuw worden opgestart na het wijzigen van deze para meter. Intern worden met deze para meter de post gres-para meters `wal_level` , `max_replication_slots` en ingesteld `max_wal_senders` .

## <a name="azure-cli"></a>Azure CLI
U kunt met behulp van de Azure CLI Lees replica's maken en beheren.

### <a name="prerequisites"></a>Vereisten

- [Installeer Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Een [Azure database for postgresql-server](quickstart-create-server-up-azure-cli.md) als de hoofd server.


### <a name="prepare-the-master-server"></a>De hoofd server voorbereiden

1. Controleer de waarde van de hoofd server `azure.replication_support` . Dit moet ten minste een REPLICA zijn voor het werken met replica's.

   ```azurecli-interactive
   az postgres server configuration show --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support
   ```

2. Als `azure.replication_support` niet ten minste een replica is, stelt u deze in. 

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

3. Start de server opnieuw op om de wijziging toe te passen.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

### <a name="create-a-read-replica"></a>Een lees replica maken

De opdracht [AZ post gres Server replica Create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) vereist de volgende para meters:

| Instelling | Voorbeeldwaarde | Beschrijving  |
| --- | --- | --- |
| resource-group | myResourceGroup |  De resource groep waar de replica-server wordt gemaakt.  |
| naam | mydemoserver-replica | De naam van de nieuwe replica server die wordt gemaakt. |
| source-server | mydemoserver | De naam of bron-ID van de bestaande hoofd server waaruit moet worden gerepliceerd. Gebruik de resource-ID als u wilt dat de replica en de resource groepen van de hoofd groep verschillend zijn. |

In het CLI-voor beeld hieronder wordt de replica gemaakt in dezelfde regio als de Master.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Gebruik de para meter om een lees replica te maken `--location` . In het CLI-voor beeld hieronder wordt de replica in VS West gemaakt.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Ga naar het [artikel concepten van replica's lezen](concepts-read-replicas.md)voor meer informatie over de regio's die u kunt maken in de replica. 

Als u de `azure.replication_support` para meter niet op **replica** hebt ingesteld op een master server met algemeen of geoptimaliseerd voor geheugen en de server opnieuw hebt opgestart, wordt een fout bericht weer gegeven. Voer de volgende twee stappen uit voordat u een replica maakt.

> [!IMPORTANT]
> Raadpleeg de [sectie overwegingen in het overzicht van het lezen van replica's](concepts-read-replicas.md#considerations).
>
> Werk de replica-instelling bij naar een gelijke of grotere waarde voordat een master server-instelling wordt bijgewerkt naar een nieuwe waarde. Met deze actie wordt de replica zo aangepast dat er wijzigingen in de master worden aangebracht.

### <a name="list-replicas"></a>Replica's weer geven
U kunt de lijst met replica's van een hoofd server weer geven met de opdracht [AZ post gres Server replica list](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) .

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

### <a name="stop-replication-to-a-replica-server"></a>Replicatie naar een replica server stoppen
U kunt de replicatie tussen een hoofd server en een lees replica stoppen met de opdracht [AZ post gres Server replica stop](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) .

Nadat u de replicatie naar een hoofd server en een lees replica hebt gestopt, kunt u deze niet meer ongedaan maken. De Lees replica wordt een zelfstandige server die zowel lees-als schrijf bewerkingen ondersteunt. De zelfstandige server kan niet opnieuw in een replica worden gemaakt.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

### <a name="delete-a-master-or-replica-server"></a>Een Master-of replica server verwijderen
Als u een Master-of replica server wilt verwijderen, gebruikt u de opdracht [AZ post gres server delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) .

Wanneer u een master-server verwijdert, wordt de replicatie naar alle Lees replica's gestopt. De Lees replica's worden zelfstandige servers die nu zowel lees-als schrijf bewerkingen ondersteunen.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="rest-api"></a>REST-API
U kunt met behulp van de [Azure rest API](/rest/api/azure/)Lees replica's maken en beheren.

### <a name="prepare-the-master-server"></a>De hoofd server voorbereiden

1. Controleer de waarde van de hoofd server `azure.replication_support` . Dit moet ten minste een REPLICA zijn voor het werken met replica's.

   ```http
   GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/configurations/azure.replication_support?api-version=2017-12-01
   ```

2. Als `azure.replication_support` niet ten minste een replica is, stelt u deze in.

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

2. [Start de server opnieuw](/rest/api/postgresql/servers/restart) op om de wijziging toe te passen.

   ```http
   POST https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/restart?api-version=2017-12-01
   ```

### <a name="create-a-read-replica"></a>Een lees replica maken
U kunt een lees replica maken met behulp van de [API Create](/rest/api/postgresql/servers/create):

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
> Ga naar het [artikel concepten van replica's lezen](concepts-read-replicas.md)voor meer informatie over de regio's die u kunt maken in de replica. 

Als u de `azure.replication_support` para meter niet op **replica** hebt ingesteld op een master server met algemeen of geoptimaliseerd voor geheugen en de server opnieuw hebt opgestart, wordt een fout bericht weer gegeven. Voer de volgende twee stappen uit voordat u een replica maakt.

Een replica wordt gemaakt met behulp van dezelfde berekenings-en opslag instellingen als de hoofd server. Nadat een replica is gemaakt, kunnen verschillende instellingen onafhankelijk van de hoofd server worden gewijzigd: generatie van compute, vCores, opslag en back-up van Bewaar periode. De prijs categorie kan ook onafhankelijk worden gewijzigd, met uitzonde ring van of van de Basic-laag.


> [!IMPORTANT]
> Werk de replica-instelling bij naar een gelijke of grotere waarde voordat een master server-instelling wordt bijgewerkt naar een nieuwe waarde. Met deze actie wordt de replica zo aangepast dat er wijzigingen in de master worden aangebracht.

### <a name="list-replicas"></a>Replica's weer geven
U kunt de lijst met replica's van een hoofd server weer geven met behulp van de [replica lijst-API](/rest/api/postgresql/replicas/listbyserver):

```http
GET https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{masterServerName}/Replicas?api-version=2017-12-01
```

### <a name="stop-replication-to-a-replica-server"></a>Replicatie naar een replica server stoppen
U kunt de replicatie tussen een hoofd server en een lees replica stoppen met de [Update-API](/rest/api/postgresql/servers/update).

Nadat u de replicatie naar een hoofd server en een lees replica hebt gestopt, kunt u deze niet meer ongedaan maken. De Lees replica wordt een zelfstandige server die zowel lees-als schrijf bewerkingen ondersteunt. De zelfstandige server kan niet opnieuw in een replica worden gemaakt.

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

### <a name="delete-a-master-or-replica-server"></a>Een Master-of replica server verwijderen
Als u een Master-of replica server wilt verwijderen, gebruikt u de [API verwijderen](/rest/api/postgresql/servers/delete):

Wanneer u een master-server verwijdert, wordt de replicatie naar alle Lees replica's gestopt. De Lees replica's worden zelfstandige servers die nu zowel lees-als schrijf bewerkingen ondersteunen.

```http
DELETE https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.DBforPostgreSQL/servers/{serverName}?api-version=2017-12-01
```

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [lezen van replica's in azure database for PostgreSQL](concepts-read-replicas.md).
* Meer informatie over [het maken en beheren van Lees replica's in de Azure Portal](howto-read-replicas-portal.md).
