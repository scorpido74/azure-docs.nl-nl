---
title: Scale-Server groep-grootschalige (Citus)-Azure Database for PostgreSQL
description: De Server groep geheugen, schijf en CPU-bronnen aanpassen om te voorzien in een grotere belasting
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: how-to
ms.date: 9/18/2020
ms.openlocfilehash: fef873d5122fefb48c85281f71e206f95f3fbe48
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90986730"
---
# <a name="server-group-size"></a>Grootte van servergroep

De implementatie optie grootschalige (Citus) maakt gebruik van data base-servers voor samen werking om query's uit te voeren en meer gegevens op te slaan. De Server groep ' size ' verwijst naar het aantal servers en de hardwarebronnen van elk.

## <a name="picking-initial-size"></a>Begin grootte van opname

De grootte van een server groep, in termen van het aantal knoop punten en de hardware-capaciteit, is eenvoudig te wijzigen ([Zie hieronder](#scale-a-hyperscale-citus-server-group)). U moet echter wel een begin grootte kiezen voor een nieuwe server groep. Hier volgen enkele tips voor een redelijke keuze.

### <a name="multi-tenant-saas-use-case"></a>Multi tenant SaaS-gebruik-Case

Voor de migratie naar grootschalige (Citus) van een bestaand PostgreSQL data base-exemplaar met één knoop punt, kunt u het beste een cluster kiezen waarvan het aantal worker vCores en het totale RAM-geheugen gelijk is aan dat van het oorspronkelijke exemplaar. In dergelijke scenario's hebben we twee tot drie verbeteringen in prestaties gezien, omdat sharding het gebruik van bronnen verbetert, waardoor kleinere indexen, enzovoort.

Het aantal vCores dat vereist is voor het coördinator knooppunt is afhankelijk van uw bestaande workload (schrijf-en lees doorvoer). Het coördinator knooppunt vereist niet zoveel RAM-geheugen als worker-knoop punten, maar RAM-toewijzing wordt bepaald op basis van het aantal vCore (zoals beschreven in de [grootschalige-configuratie opties](concepts-hyperscale-configuration-options.md)), zodat het vCore aantal in feite de echte beslissing is.

### <a name="real-time-analytics-use-case"></a>Real-time analyse-gebruik-Case

Totaal aantal vCores: wanneer werk gegevens in het RAM-geheugen passen, kunt u een lineaire prestatie verbetering verwachten op grootschalige (Citus) in verhouding tot het aantal worker-kernen. Als u het juiste aantal vCores voor uw behoeften wilt bepalen, moet u rekening houden met de huidige latentie voor query's in uw data base met één knoop punt en de vereiste latentie in grootschalige (Citus). Deel de huidige latentie door de gewenste latentie en rond het resultaat af.

RAM voor werkrollen: de beste manier is om voldoende geheugen beschikbaar te stellen zodat het merendeel van de werksets in het geheugen past. Het type query's dat door uw toepassing wordt gebruikt, is van invloed op de geheugen vereisten. U kunt uitleg analyseren uitvoeren op een query om te bepalen hoeveel geheugen nodig is. Houd er rekening mee dat vCores en RAM samen worden geschaald, zoals beschreven in het artikel [configuratie opties voor grootschalige](concepts-hyperscale-configuration-options.md) .

## <a name="scale-a-hyperscale-citus-server-group"></a>Een Citus-Server groep (grootschalige) schalen

Azure Database for PostgreSQL-grootschalige (Citus) biedt self-service schaling voor een hogere belasting. Met de Azure Portal kunt u eenvoudig nieuwe werk knooppunten toevoegen en de vCores van bestaande knoop punten verg Roten. Het toevoegen van knoop punten leidt tot uitval tijd en zelfs het verplaatsen van Shards naar de nieuwe knoop punten ( [Shard-herverdeling](#rebalance-shards)) gebeurt zonder query's te onderbreken.

### <a name="add-worker-nodes"></a>Worker-knoop punten toevoegen

Als u knoop punten wilt toevoegen, gaat u naar het tabblad **Compute + Storage** in de Server groep grootschalige (Citus).  Door de schuif regelaar voor het **aantal worker-knoop punten** te slepen, wijzigt u de waarde.

:::image type="content" source="./media/howto-hyperscale-scaling/01-sliders-workers.png" alt-text="Resource schuif regelaars":::

Klik op de knop **Opslaan** om de gewijzigde waarde van kracht te laten worden.

> [!NOTE]
> Zodra het aantal worker-knoop punten is verhoogd en opgeslagen, kan de schuif regelaar niet meer worden gebruikt.

#### <a name="rebalance-shards"></a>Shards opnieuw verdelen

Als u wilt profiteren van nieuwe knoop punten, moet u de gedistribueerde tabel [Shards](concepts-hyperscale-distributed-data.md#shards)opnieuw verdelen. Dit betekent dat u een aantal Shards van bestaande knoop punten naar de nieuwe hebt verplaatst. Controleer eerst of de inrichting van de nieuwe werk rollen is voltooid. Start vervolgens de Shard-herbalancer door verbinding te maken met het knoop punt van de cluster coördinator met psql en wordt uitgevoerd:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

Met de `rebalance_table_shards` functie worden alle tabellen in de groep co- [locatie](concepts-hyperscale-colocation.md) van de tabel met de naam in het argument opnieuw gebalanceerd. U hoeft de functie dus niet aan te roepen voor elke gedistribueerde tabel, maar u kunt deze ook aanroepen in een representatieve tabel vanuit elke groep voor co-locaties.

### <a name="increase-or-decrease-vcores-on-nodes"></a>VCores op knoop punten verg Roten of verkleinen

Naast het toevoegen van nieuwe knoop punten, kunt u de mogelijkheden van bestaande knoop punten verg Roten. Het aanpassen van de reken capaciteit omhoog en omlaag kan nuttig zijn bij het uitvoeren van prestatie experimenten en op korte of lange termijn wijzigingen in verkeers vereisten.

Als u de vCores voor alle worker-knoop punten wilt wijzigen, past u de schuif regelaar **vCores** aan onder **configuratie (per worker-knoop punt)**. De vCores van het coördinator knooppunt kan onafhankelijk worden aangepast. Pas de schuif regelaar **vCores** aan onder  **configuratie (coördinator knooppunt)**.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de opties voor de [prestaties](concepts-hyperscale-configuration-options.md)van de Server groep.
