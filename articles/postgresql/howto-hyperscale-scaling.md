---
title: Een Citus-Server groep (Azure Database for PostgreSQL-grootschalige) schalen
description: De Server groep geheugen, schijf en CPU-bronnen aanpassen om te voorzien in een grotere belasting
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/17/2019
ms.openlocfilehash: a69b4988a5ee835381de986edaa5899b09aa9e4e
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71262490"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Een Citus-Server groep (grootschalige) schalen

Azure Database for PostgreSQL-grootschalige (Citus) biedt self-service schaling voor een hogere belasting. Met de Azure Portal kunt u eenvoudig nieuwe worker-knoop punten toevoegen.

Als u dit wilt doen, gaat u naar het tabblad **configureren** in de Server groep grootschalige (Citus).
Sleep de schuif regelaar voor het **aantal worker-knoop punten** om de waarde te wijzigen.

![Resource schuif regelaars](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Klik op de knop Opslaan om de gewijzigde waarde van kracht te laten worden.

> [!NOTE]
> Zodra het aantal worker-knoop punten is verhoogd en opgeslagen, kan de schuif regelaar niet meer worden gebruikt.
>
> Daarnaast kunnen vCores en opslag nog niet worden aangepast aan de coördinator of werk nemers met deze gebruikers interface. Open een ondersteunings ticket als u de reken kracht op de coördinator of worker-knoop punten wilt schalen.

Als u wilt profiteren van nieuwe knoop punten, moet u de gedistribueerde tabel [Shards](concepts-hyperscale-distributed-data.md#shards)opnieuw verdelen. Dit betekent dat u een aantal Shards van bestaande knoop punten naar de nieuwe hebt verplaatst. Als u de Shard-herbalancer wilt starten, maakt u verbinding met het cluster coördinator knooppunt met psql en voert u de volgende handelingen uit:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

Met `rebalance_table_shards` de functie worden alle tabellen in de groep co- [locatie](concepts-hyperscale-colocation.md) van de tabel met de naam in het argument opnieuw gebalanceerd. U hoeft de functie dus niet aan te roepen voor elke gedistribueerde tabel, maar u kunt deze ook aanroepen in een representatieve tabel vanuit elke groep voor co-locaties.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de opties voor de [prestaties](concepts-hyperscale-configuration-options.md)van de Server groep.
