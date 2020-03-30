---
title: Servergroep schalen - Hyperscale (Citus) - Azure Database voor PostgreSQL
description: Servergroepgeheugen, schijf- en CPU-resources aanpassen om de toegenomen belasting aan te pakken
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 3/16/2020
ms.openlocfilehash: fa48ca287c248155a0271b5134be782d8db1c785
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063100"
---
# <a name="scale-a-hyperscale-citus-server-group"></a>Een Citus-servergroep (Hyperscale) schalen

Azure Database voor PostgreSQL - Hyperscale (Citus) biedt selfserviceschaling om te gaan met verhoogde belasting. De Azure-portal maakt het eenvoudig om nieuwe werknemersknooppunten toe te voegen en de vCores van bestaande knooppunten te verhogen.

## <a name="add-worker-nodes"></a>Worker-knooppunten toevoegen

Als u knooppunten wilt toevoegen, gaat u naar het tabblad **Configureren** in de servergroep Hyperscale (Citus).  Als u de schuifregelaar voor **het aantal werknemersknooppunten** sleept, wordt de waarde gewijzigd.

![Bronschuifregelaars](./media/howto-hyperscale-scaling/01-sliders-workers.png)

Klik **op** de knop Opslaan om de gewijzigde waarde van kracht te laten worden.

> [!NOTE]
> Eenmaal verhoogd en opgeslagen, kan het aantal werknemersknooppunten niet worden verminderd met behulp van de schuifregelaar.

### <a name="rebalance-shards"></a>Shards opnieuw in balans brengen

Om te profiteren van nieuw toegevoegde knooppunten moet u gedistribueerde [tabelscherven](concepts-hyperscale-distributed-data.md#shards)opnieuw in evenwicht brengen, wat betekent dat u sommige scherven van bestaande knooppunten naar de nieuwe moet verplaatsen. Controleer eerst of de nieuwe werknemers de inrichting met succes hebben voltooid. Start vervolgens de shardrebalancer door verbinding te maken met het clustercoördinatorknooppunt met psql en uitvoeren:

```sql
SELECT rebalance_table_shards('distributed_table_name');
```

De `rebalance_table_shards` functie brengt alle tabellen in de [colocatiegroep](concepts-hyperscale-colocation.md) van de tabel met de naam in het argument opnieuw in evenwicht. U hoeft dus niet voor elke gedistribueerde tabel de functie aan te roepen, maar zet deze op een representatieve tabel van elke colocatiegroep.

## <a name="increase-or-decrease-vcores-on-nodes"></a>VCores op knooppunten verhogen of verkleinen

> [!NOTE]
> Deze functie is momenteel beschikbaar als preview-product. Neem contact op [met Azure Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)om een wijziging aan te vragen in vCores voor knooppunten in uw servergroep.

Naast het toevoegen van nieuwe knooppunten, u de mogelijkheden van bestaande knooppunten vergroten. Het aanpassen van de rekencapaciteit op en neer kan nuttig zijn voor prestatie-experimenten en wijzigingen op korte of lange termijn in de verkeerseisen.

Als u de vCores voor alle werknemersknooppunten wilt wijzigen, past u de schuifregelaar **vCores** aan onder **Configuratie (per werkknooppunt).** De vCores van coördinator node kunnen onafhankelijk van elkaar worden aangepast. Klik **op** de koppeling Configuratie wijzigen onder **coördinatorknooppunt**. Er verschijnt een dialoogvenster met schuifregelaars voor de vCores en opslagcapaciteit van de coördinator. Wijzig de schuifregelaars naar wens en selecteer **OK**.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [prestatieopties](concepts-hyperscale-configuration-options.md)voor servergroepen .
