---
title: Lees replica's beheren voor Azure Database for PostgreSQL-één server uit de Azure CLI
description: Meer informatie over het beheren van Lees replica's in Azure Database for PostgreSQL-één-server vanuit de Azure CLI.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/21/2019
ms.openlocfilehash: 63a8acad3c393a4c4d9c6a3b6750f1f934dad43d
ms.sourcegitcommit: beb34addde46583b6d30c2872478872552af30a1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2019
ms.locfileid: "69907425"
---
# <a name="create-and-manage-read-replicas-from-the-azure-cli"></a>Lees replica's maken en beheren vanuit de Azure CLI

In dit artikel leert u hoe u met de Azure CLI Lees replica's maakt en beheert in Azure Database for PostgreSQL. Zie het [overzicht](concepts-read-replicas.md)voor meer informatie over het lezen van replica's.

> [!IMPORTANT]
> U kunt een lees replica maken in dezelfde regio als uw hoofd server of in andere Azure-regio's van uw keuze. Replicatie tussen regio's is momenteel beschikbaar als open bare preview.

## <a name="prerequisites"></a>Vereisten
- Een [Azure database for postgresql-server](quickstart-create-server-up-azure-cli.md) als de hoofd server.

[!INCLUDE [cloud-shell-try-it](../../includes/cloud-shell-try-it.md)]

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor dit artikel gebruikmaken van Azure CLI versie 2.0 of hoger. Voer de opdracht `az --version` uit om de geïnstalleerde versie te zien. Zie [Azure CLI installeren]( /cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren. 


## <a name="prepare-the-master-server"></a>De hoofd server voorbereiden
Deze stappen moeten worden gebruikt om een hoofd server voor te bereiden in de lagen Algemeen of geoptimaliseerd voor geheugen.

De `azure.replication_support` para meter moet worden ingesteld op **replica** op de hoofd server. Wanneer deze statische para meter wordt gewijzigd, moet de server opnieuw worden opgestart om de wijziging van kracht te laten worden.

1. Stel `azure.replication_support` in op replica.

   ```azurecli-interactive
   az postgres server configuration set --resource-group myresourcegroup --server-name mydemoserver --name azure.replication_support --value REPLICA
   ```

2. Opnieuw opstarten om de wijziging toe te passen op de server.

   ```azurecli-interactive
   az postgres server restart --name mydemoserver --resource-group myresourcegroup
   ```

## <a name="create-a-read-replica"></a>Maken van een replica lezen

De opdracht [AZ post gres Server replica Create](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-create) vereist de volgende para meters:

| Instelling | Voorbeeldwaarde | Description  |
| --- | --- | --- |
| resource-group | myResourceGroup |  De resource groep waar de replica-server wordt gemaakt.  |
| name | mydemoserver-replica | De naam van de nieuwe replicaserver die is gemaakt. |
| source-server | mydemoserver | De naam of bron-ID van de bestaande hoofd server waaruit moet worden gerepliceerd. |

In het CLI-voor beeld hieronder wordt de replica gemaakt in dezelfde regio als de Master.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup
```

Gebruik de `--location` para meter om een lees replica te maken. In het CLI-voor beeld hieronder wordt de replica in VS West gemaakt.

```azurecli-interactive
az postgres server replica create --name mydemoserver-replica --source-server mydemoserver --resource-group myresourcegroup --location westus
```

> [!NOTE]
> Ga naar het [artikel concepten van replica's lezen](concepts-read-replicas.md)voor meer informatie over de regio's die u kunt maken in de replica. 

Als u de `azure.replication_support` para meter niet op **replica** hebt ingesteld op een master server met algemeen of geoptimaliseerd voor geheugen en de server opnieuw hebt opgestart, wordt een fout bericht weer gegeven. Voer de volgende twee stappen uit voordat u een replica maakt.

Een replica wordt gemaakt met behulp van dezelfde berekenings-en opslag instellingen als de hoofd server. Nadat een replica is gemaakt, kunnen verschillende instellingen onafhankelijk van de hoofd server worden gewijzigd: generatie van compute, vCores, opslag en back-up van Bewaar periode. De prijs categorie kan ook onafhankelijk worden gewijzigd, met uitzonde ring van of van de Basic-laag.

> [!IMPORTANT]
> Werk de replica-instelling bij naar een gelijke of grotere waarde voordat een master server-instelling wordt bijgewerkt naar een nieuwe waarde. Met deze actie wordt de replica zo aangepast dat er wijzigingen in de master worden aangebracht.

## <a name="list-replicas"></a>Replica's weer geven
U kunt de lijst met replica's van een hoofd server weer geven met de opdracht [AZ post gres Server replica list](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-list) .

```azurecli-interactive
az postgres server replica list --server-name mydemoserver --resource-group myresourcegroup 
```

## <a name="stop-replication-to-a-replica-server"></a>Replicatie naar een replica-server stoppen
U kunt de replicatie tussen een hoofd server en een lees replica stoppen met de opdracht [AZ post gres Server replica stop](/cli/azure/postgres/server/replica?view=azure-cli-latest#az-postgres-server-replica-stop) .

Nadat u de replicatie naar een hoofd server en een lees replica hebt gestopt, kunt u deze niet meer ongedaan maken. De Lees replica wordt een zelfstandige server die zowel lees-als schrijf bewerkingen ondersteunt. De zelfstandige server kan niet opnieuw in een replica worden gemaakt.

```azurecli-interactive
az postgres server replica stop --name mydemoserver-replica --resource-group myresourcegroup 
```

## <a name="delete-a-master-or-replica-server"></a>Een Master-of replica server verwijderen
Als u een Master-of replica server wilt verwijderen, gebruikt u de opdracht [AZ post gres server delete](/cli/azure/postgres/server?view=azure-cli-latest#az-postgres-server-delete) .

Wanneer u een master-server verwijdert, wordt de replicatie naar alle Lees replica's gestopt. De Lees replica's worden zelfstandige servers die nu zowel lees-als schrijf bewerkingen ondersteunen.

```azurecli-interactive
az postgres server delete --name myserver --resource-group myresourcegroup
```

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over het [lezen van replica's in azure database for PostgreSQL](concepts-read-replicas.md).
* Meer informatie over [het maken en beheren van Lees replica's in de Azure Portal](howto-read-replicas-portal.md).