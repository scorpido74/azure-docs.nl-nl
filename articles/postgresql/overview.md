---
title: Overzicht van de relationele databaseservice Azure Database for PostgreSQL
description: Dit biedt een overzicht van de relationele databaseservice Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 11/25/2019
ms.openlocfilehash: 9ea0610811f6906526afe55d577e04a8decd5f49
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "74481655"
---
# <a name="what-is-azure-database-for-postgresql"></a>Wat is Azure Database for PostgreSQL?
Azure Database voor PostgreSQL is een relationele databaseservice in de Microsoft-cloud die is gebouwd voor ontwikkelaars. Het is gebaseerd op de communityversie van de open-source [PostgreSQL-databaseengine](https://www.postgresql.org/) en is beschikbaar in twee implementatieopties: Single Server en Hyperscale (Citus).

## <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - één server
De optie Implementatie van één server wordt geleverd:

- Ingebouwde [hoge beschikbaarheid](concepts-high-availability.md) zonder extra kosten (99,99% SLA)
- Voorspelbare prestaties, tegen all-inclusive prijzen op basis van betalen per gebruik
- [Verticale schaal indien nodig](concepts-pricing-tiers.md) binnen enkele seconden
- [Bewaken en waarschuwen](concepts-monitoring.md) om uw server te beoordelen
- Beveiliging en naleving van enterprise-kwaliteit
- [Beveiligd om](concepts-security.md) gevoelige gegevens in rust en in beweging te beschermen
- [Automatische back-ups en point-in-time-restore](concepts-business-continuity.md) voor maximaal 35 dagen


Alle deze mogelijkheden vereisen nauwelijks beheer, en alles wordt zonder extra kosten geleverd. Hiermee u zich richten op snelle applicatieontwikkeling en het versnellen van uw time-to-market, in plaats van kostbare tijd en middelen te besteden aan het beheer van virtuele machines en infrastructuur. U uw applicatie blijven ontwikkelen met de open-source tools en het platform van uw keuze, zonder nieuwe vaardigheden te hoeven leren.

De optie Implementatie van één server biedt drie prijsniveaus: Basic, General Purpose en Memory Optimized. Elke categorie biedt verschillende resources ter ondersteuning van de workloads van uw databases. U kunt uw eerste app op een kleine database bouwen voor een paar euro met maand en vervolgens de schaal ervan aanpassen om aan de vereisten van uw oplossing te voldoen. Doordat de schaalbaarheid dynamisch is, kan uw database op een transparante manier reageren op snel veranderende resourcevereisten. U betaalt alleen voor de resources die u nodig hebt op het moment dat u ze nodig hebt. Zie  [Prijscategorieën](concepts-pricing-tiers.md) voor meer informatie.

## <a name="azure-database-for-postgresql---hyperscale-citus"></a>Azure Database voor PostgreSQL - Hyperscale (Citus)
De optie Hyperscale (Citus) schaalt query's horizontaal over meerdere machines met sharding. De queryengine paralleleert binnenkomende SQL-query's over deze servers voor snellere antwoorden op grote gegevenssets. Het bedient toepassingen die grotere schaal en prestaties vereisen, over het algemeen workloads die naderen - of al meer dan - 100 GB aan gegevens.

De optie Hyperscale (Citus) implementatie levert:

- Horizontaal schalen over meerdere machines met sharding
- Queryparallelisatie over deze servers voor snellere antwoorden op grote gegevenssets
- Uitstekende ondersteuning voor multi-tenant toepassingen, realtime operationele analyses en transactionele workloads met hoge doorvoer

Toepassingen die voor PostgreSQL zijn gemaakt, kunnen gedistribueerde query's uitvoeren op Hyperscale (Citus) met [standaardverbindingsbibliotheken](./concepts-connection-libraries.md) en minimale wijzigingen.

## <a name="contacts"></a>Contactpersonen
Stuur voor vragen of suggesties over het werken met Azure Database voor PostgreSQL een e-mail naar de Azure Database voor PostgreSQL Team[ @Ask (Azure DB for PostgreSQL).](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) Dit adres is voor algemene vragen in plaats van support tickets.

Bovendien, acht deze contactpunten zo nodig:
- Als u contact wilt opnemen met Azure Support of een probleem met uw account wilt oplossen, [dient u een ticket in vanaf de Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade)
- Als u feedback wilt geven of een nieuwe functie wilt aanvragen, maakt u een vermelding via [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Volgende stappen
- Zie de [prijspagina](https://azure.microsoft.com/pricing/details/postgresql/) voor kostenvergelijkingen en rekenmachines.
- Aan de slag met het maken van uw eerste Azure-database voor PostgreSQL [Single Server](./quickstart-create-server-database-portal.md) of [Hyperscale (Citus)](./quickstart-create-hyperscale-portal.md)
- Bouw uw eerste app in Python, PHP, Ruby, C\#, Java, Node.js: [verbindingsbibliotheken](./concepts-connection-libraries.md)
