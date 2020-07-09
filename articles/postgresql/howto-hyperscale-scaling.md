---
title: Scale-Server groep-grootschalige (Citus)-Azure Database for PostgreSQL
description: De Server groep geheugen, schijf en CPU-bronnen aanpassen om te voorzien in een grotere belasting
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 3/16/2020
ms.openlocfilehash: 1173defa8bbe66cbeaaf6bd5264b0730160a197b
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86116825"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Een Citus-Server groep (grootschalige) schalen

Azure Database for PostgreSQL-grootschalige (Citus) biedt self-service schaling voor een hogere belasting. Met de Azure Portal kunt u eenvoudig nieuwe werk knooppunten toevoegen en de vCores van bestaande knoop punten verg Roten.

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

Met de `rebalance_table_shards` functie worden alle tabellen in de groep co- [locatie](concepts-hyperscale-colocation.md) van de tabel met de naam in het argument opnieuw gebalanceerd. U hoeft de functie dus niet aan te roepen voor elke gedistribueerde tabel, maar u kunt deze ook aanroepen in een representatieve tabel vanuit elke groep voor co-locaties.

## <a name="increase-or-decrease-vcores-on-nodes"></a>VCores op knoop punten verg Roten of verkleinen

> [!NOTE]
> Deze functie is momenteel in preview. [Neem contact op met de ondersteuning van Azure](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)als u een wijziging wilt aanvragen in vCores voor knoop punten in uw server groep.

Naast het toevoegen van nieuwe knoop punten, kunt u de mogelijkheden van bestaande knoop punten verg Roten. Het aanpassen van de reken capaciteit omhoog en omlaag kan nuttig zijn bij het uitvoeren van prestatie experimenten en op korte of lange termijn wijzigingen in verkeers vereisten.

Als u de vCores voor alle worker-knoop punten wilt wijzigen, past u de schuif regelaar **vCores** aan onder **configuratie (per worker-knoop punt)**. De vCores van het coördinator knooppunt kan onafhankelijk worden aangepast. Klik op de koppeling **configuratie wijzigen** onder **coördinator knooppunt**. Er wordt een dialoog venster weer gegeven met schuif regelaars voor de vCores en opslag capaciteit van de coördinator. Wijzig de schuif regelaars naar wens en selecteer **OK**.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over de opties voor de [prestaties](concepts-hyperscale-configuration-options.md)van de Server groep.
