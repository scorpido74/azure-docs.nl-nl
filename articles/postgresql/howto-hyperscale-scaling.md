---
title: Een Citus-Server groep (Azure Database for PostgreSQL-grootschalige) schalen
description: De Server groep geheugen, schijf en CPU-bronnen aanpassen om te voorzien in een grotere belasting
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/17/2019
ms.openlocfilehash: 6053ba37bf330f6b59e291dade822a5ca9de8c85
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73492310"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Een Citus-Server groep (grootschalige) schalen

Azure Database for PostgreSQL-grootschalige (Citus) biedt self-service schaling voor een hogere belasting. Met de Azure Portal kunt u eenvoudig nieuwe werk knooppunten toevoegen en de capaciteit van bestaande knoop punten verg Roten.

## <a name="add-worker-nodes"></a>Worker-knoop punten toevoegen

Als u knoop punten wilt toevoegen, gaat u naar het tabblad **configureren** in de Server groep grootschalige (Citus).  Door de schuif regelaar voor het **aantal worker-knoop punten** te slepen, wijzigt u de waarde.

![Resource schuif regelaars](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Klik op de knop **Opslaan** om de gewijzigde waarde van kracht te laten worden.

> [!NOTE]
> Zodra het aantal worker-knoop punten is verhoogd en opgeslagen, kan de schuif regelaar niet meer worden gebruikt.

### <a name="rebalance-shards"></a>Shards opnieuw verdelen

Als u wilt profiteren van nieuwe knoop punten, moet u de gedistribueerde tabel [Shards](concepts-hyperscale-distributed-data.md#shards)opnieuw verdelen. Dit betekent dat u een aantal Shards van bestaande knoop punten naar de nieuwe hebt verplaatst. Controleer eerst of de inrichting van de nieuwe werk rollen is voltooid. Start vervolgens de Shard-herbalancer door verbinding te maken met het knoop punt van de cluster coördinator met psql en wordt uitgevoerd:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

Met de functie `rebalance_table_shards` worden alle tabellen in de groep co- [locatie](concepts-hyperscale-colocation.md) van de tabel met de naam in het argument opnieuw gebalanceerd. U hoeft de functie dus niet aan te roepen voor elke gedistribueerde tabel, maar u kunt deze ook aanroepen in een representatieve tabel vanuit elke groep voor co-locaties.

## <a name="increase-vcores-or-storage-space"></a>VCores of opslag ruimte verg Roten

Naast het toevoegen van nieuwe knoop punten, kunt u de mogelijkheden van bestaande knoop punten verg Roten. Ga naar het tabblad **configureren** in de Server groep grootschalige (Citus) en sleep de schuif regelaar voor **vCores** en **opslag** om deze waarden voor alle worker-knoop punten te wijzigen. Klik op **Opslaan** om de wijzigingen toe te passen.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de opties voor de [prestaties](concepts-hyperscale-configuration-options.md)van de Server groep.
