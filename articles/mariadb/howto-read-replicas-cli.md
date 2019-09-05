---
title: Lees replica's maken en beheren in Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u in Azure Database for MariaDB Lees replica's instelt en beheert met behulp van de Azure CLI.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 07/26/2019
ms.openlocfilehash: ba0f3bd002b2675c33ea5106ce64c7957c9989d0
ms.sourcegitcommit: f176e5bb926476ec8f9e2a2829bda48d510fbed7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2019
ms.locfileid: "70309127"
---
# <a name="how-to-create-and-manage-read-replicas-in-azure-database-for-mariadb-using-the-azure-cli"></a>Lees replica's maken en beheren in Azure Database for MariaDB met behulp van de Azure CLI

In dit artikel leert u hoe u met de Azure CLI Lees replica's maakt en beheert in dezelfde Azure-regio als de Master in de Azure Database for MariaDB-service.

## <a name="prerequisites"></a>Vereisten

- [Installeer Azure CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest)
- Een [Azure database for MariaDB-server](quickstart-create-mariadb-server-database-using-azure-portal.md) die wordt gebruikt als de hoofd server. 

> [!IMPORTANT]
> De functie voor het lezen van replica's is alleen beschikbaar voor Azure Database for MariaDB-servers in de prijs Categorieën Algemeen of geoptimaliseerd voor geheugen. Controleer of de hoofd-server in een van deze Prijscategorieën.

## <a name="create-a-read-replica"></a>Maken van een replica lezen

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

## <a name="stop-replication-to-a-replica-server"></a>Replicatie naar een replica-server stoppen

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

## <a name="delete-a-replica-server"></a>Een replica-server verwijderen

Het verwijderen van een lees replica-server kan worden uitgevoerd door de opdracht **[AZ mariadb server delete](/cli/azure/mariadb/server)** uit te voeren.

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoreplicaserver
```

## <a name="delete-a-master-server"></a>Een hoofd-server verwijderen

> [!IMPORTANT]
> Verwijderen van een hoofd-server-replicatie naar alle replicaservers stopt en Hiermee verwijdert u de hoofd-server zelf. Replica-servers worden zelfstandige servers die bieden nu ondersteuning voor zowel lees- en schrijfbewerkingen.

Als u een master server wilt verwijderen, kunt u de opdracht **[AZ mariadb server delete](/cli/azure/mariadb/server)** uitvoeren.

```azurecli-interactive
az mariadb server delete --resource-group myresourcegroup --name mydemoserver
```

## <a name="list-replicas-for-a-master-server"></a>Lijst met replica's voor een hoofd-server

Als u wilt weergeven van alle replica's voor een bepaalde hoofd-server, moet u de volgende opdracht uitvoeren: 

```azurecli-interactive
az mariadb server replica list --server-name mydemoserver --resource-group myresourcegroup
```

De `az mariadb server replica list` opdracht moet de volgende parameters:

| Instelling | Voorbeeldwaarde | Description  |
| --- | --- | --- |
| resource-group |  myResourceGroup |  De resourcegroep waar de replica-server om te worden gemaakt.  |
| servernaam | mydemoserver | De naam of ID van de hoofd-server. |

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [lezen replica's](concepts-read-replicas.md)
