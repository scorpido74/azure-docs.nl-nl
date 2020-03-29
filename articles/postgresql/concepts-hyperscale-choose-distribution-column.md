---
title: Distributiekolommen kiezen – Hyperscale (Citus) - Azure Database voor PostgreSQL
description: Meer informatie over het kiezen van distributiekolommen in veelvoorkomende hyperscale-scenario's in Azure Database voor PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: 8ced9767d81affceef851820ee587f4f3dd24deb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975666"
---
# <a name="choose-distribution-columns-in-azure-database-for-postgresql--hyperscale-citus"></a>Distributiekolommen kiezen in Azure Database voor PostgreSQL – Hyperscale (Citus)

Het kiezen van de distributiekolom van elke tabel is een van de belangrijkste modelleringsbeslissingen die u zult nemen. Azure Database voor PostgreSQL – Hyperscale (Citus) slaat rijen op in shards op basis van de waarde van de distributiekolom van de rijen.

De juiste keuze groepeert gerelateerde gegevens samen op dezelfde fysieke knooppunten, waardoor query's snel worden en ondersteuning wordt toegevoegd voor alle SQL-functies. Een onjuiste keuze zorgt ervoor dat het systeem langzaam wordt uitgevoerd en ondersteunt niet alle SQL-functies op verschillende knooppunten.

Dit artikel geeft distributiekolomtips voor de twee meest voorkomende Hyperscale -scenario's (Citus).

### <a name="multi-tenant-apps"></a>Apps met meerdere tenants

De multi-tenantarchitectuur maakt gebruik van een vorm van hiërarchische databasemodellering om query's te distribueren over knooppunten in de servergroep. De bovenkant van de gegevenshiërarchie staat bekend als de *tenant-id* en moet worden opgeslagen in een kolom in elke tabel.

Hyperscale (Citus) inspecteert query's om te zien welke tenant-id ze inhouden en vindt de overeenkomende tabelshard. De query wordt doorgestoofd naar één werkknooppunt dat de shard bevat. Het uitvoeren van een query met alle relevante gegevens die op hetzelfde knooppunt worden geplaatst, wordt colocatie genoemd.

Het volgende diagram illustreert colocatie in het multi-tenant gegevensmodel. Het bevat twee tabellen, accounts en `account_id`campagnes, elk verdeeld door . De gearceerde dozen vertegenwoordigen scherven. Groene scherven worden samen opgeslagen op één werkknooppunt en blauwe scherven worden opgeslagen op een ander werknemersknooppunt. Merk op hoe een join query tussen accounts en campagnes alle benodigde gegevens samen\_op één knooppunt heeft wanneer beide tabellen zijn beperkt tot dezelfde account-id.

![Colocatie met meerdere huurders](media/concepts-hyperscale-choosing-distribution-column/multi-tenant-colocation.png)

Als u dit ontwerp wilt toepassen in uw eigen schema, u bepalen wat een tenant in uw toepassing is. Veelvoorkomende instanties zijn bedrijf, account, organisatie of klant. De kolomnaam zal `company_id` iets `customer_id`zijn als of . Onderzoek elk van uw vragen en vraag jezelf af, zou het werken als het had extra WAAR clausules om alle betrokken tabellen te beperken tot rijen met dezelfde tenant-ID?
Query's in het multitenantmodel worden aan een tenant toebedeeld. Query's over verkopen of voorraad worden bijvoorbeeld binnen een bepaalde winkel ondergebracht.

#### <a name="best-practices"></a>Aanbevolen procedures

-   **Partitie gedistribueerde tabellen door\_een gemeenschappelijke tenant-id-kolom.** Bijvoorbeeld, in een SaaS applicatie waar huurders\_zijn bedrijven, de\_huurder id is waarschijnlijk de bedrijfs-id.
-   **Kleine cross-tenanttabellen converteren naar referentietabellen.** Wanneer meerdere tenants een kleine tabel met informatie delen, distribueert u deze als referentietabel.
-   **Filter alle toepassingsquery's\_beperken op tenant-id.** Elke query moet informatie opvragen voor één tenant tegelijk.

Lees de [multi-tenant tutorial](./tutorial-design-database-hyperscale-multi-tenant.md) voor een voorbeeld van hoe je dit soort toepassingen te bouwen.

### <a name="real-time-apps"></a>Realtime-apps

De multi-tenantarchitectuur introduceert een hiërarchische structuur en gebruikt gegevenscolocatie om query's per tenant te routeren. Real-time architecturen zijn daarentegen afhankelijk van specifieke distributie-eigenschappen van hun gegevens om een zeer parallelle verwerking te bereiken.

We gebruiken 'entity ID' als term voor distributiekolommen in het real-time model. Typische entiteiten zijn gebruikers, hosts of apparaten.

Real-time query's vragen meestal om numerieke aggregaten gegroepeerd op datum of categorie. Hyperscale (Citus) stuurt deze query's naar elke shard voor gedeeltelijke resultaten en assembleert het uiteindelijke antwoord op het coördinatorknooppunt. Query's worden het snelst uitgevoerd wanneer zoveel mogelijk knooppunten bijdragen en wanneer geen enkel knooppunt onevenredig veel werk moet doen.

#### <a name="best-practices"></a>Aanbevolen procedures

-   **Kies een kolom met een hoge kardinaliteit als de distributiekolom.** Ter vergelijking, een statusveld op een ordertabel met waarden Nieuw, Betaald en Verzonden is een slechte keuze aan distributiekolom. Het gaat uit van alleen die paar waarden, die het aantal scherven dat de gegevens kan houden beperkt, en het aantal knooppunten dat het kan verwerken. Onder kolommen met een hoge kardinaliteit is het ook goed om kolommen te kiezen die vaak worden gebruikt in groeps-door-clausules of als joinkeys.
-   **Kies een kolom met gelijkmatige verdeling.** Als u een tabel over een kolom scheef verdeelt, worden gegevens in de tabel meestal verzameld in bepaalde shards. De knooppunten die deze scherven vasthouden, doen uiteindelijk meer werk dan andere knooppunten.
-   **Deel feiten- en dimensietabellen op hun gemeenschappelijke kolommen.**
    Uw feitentabel kan slechts één distributiesleutel hebben. Tabellen die deelnemen aan een andere sleutel worden niet samenmet de feitentabel ondergebracht. Kies een dimensie die u wilt samenvoegen op basis van hoe vaak deze wordt samengevoegd en de grootte van de verbindingsrijen.
-   **Sommige dimensietabellen wijzigen in referentietabellen.** Als een dimensietabel niet kan worden gekoppeld aan de feitentabel, u de queryprestaties verbeteren door kopieën van de dimensietabel te distribueren naar alle knooppunten in de vorm van een referentietabel.

Lees de [real-time dashboard tutorial](./tutorial-design-database-hyperscale-realtime.md) voor een voorbeeld van hoe je dit soort toepassingen te bouwen.

### <a name="time-series-data"></a>Tijdreeksgegevens

In een werkbelasting in een tijdreeks vragen toepassingen recente informatie op terwijl ze oude informatie archiveren.

De meest voorkomende fout bij het modelleren van tijd-serie informatie in Hyperscale (Citus) is het gebruik van de tijdstempel zelf als een distributie kolom. Een hash-distributie op basis van tijd verdeelt tijden schijnbaar willekeurig in verschillende scherven in plaats van het houden van bereiken van de tijd samen in scherven. Query's die betrekking hebben op tijdsperioden, bijvoorbeeld de meest recente gegevens. Dit type hash-distributie leidt tot netwerkoverhead.

#### <a name="best-practices"></a>Aanbevolen procedures

-   **Kies geen tijdstempel als distributiekolom.** Kies een andere distributiekolom. Gebruik in een app met meerdere huurders de tenant-id of gebruik in een realtime-app de entiteits-id.
-   **Gebruik in plaats daarvan PostgreSQL-tabelpartitionering voor tijd.** Gebruik tabelpartitionering om een grote tabel met tijdsgeordende gegevens te breken in meerdere overgenomen tabellen met elke tabel die verschillende tijdsbereiken bevat. Als u een postgres-partitietabel distribueert in Hyperscale (Citus), worden shards voor de overgenomen tabellen uitgevoerd.

Lees de tutorial van de [tijdreeksen](https://aka.ms/hyperscale-tutorial-timeseries) voor een voorbeeld van hoe je dit soort toepassingen bouwen.

## <a name="next-steps"></a>Volgende stappen
- Ontdek hoe [colocatie](concepts-hyperscale-colocation.md) tussen gedistribueerde gegevens query's helpt om snel te werken.
