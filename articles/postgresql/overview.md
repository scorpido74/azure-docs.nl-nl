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
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "74481655"
---
# <a name="what-is-azure-database-for-postgresql"></a>Wat is Azure Database for PostgreSQL?
Azure Database for PostgreSQL is een relationele database service in de micro soft Cloud die is gebouwd voor ontwikkel aars. Het is gebaseerd op de Community-versie van de open-source [postgresql](https://www.postgresql.org/) data base-engine en is beschikbaar in twee implementatie opties: één server en grootschalige (Citus).

## <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - één server
De implementatie optie voor één server levert het volgende:

- Ingebouwde [hoge Beschik baarheid](concepts-high-availability.md) zonder extra kosten (99,99% Sla)
- Voorspelbare prestaties, tegen all-inclusive prijzen op basis van betalen per gebruik
- [Verticale schaal indien nodig](concepts-pricing-tiers.md) binnen enkele seconden
- [Bewaking en waarschuwingen](concepts-monitoring.md) voor het beoordelen van uw server
- Beveiliging en naleving van enterprise-kwaliteit
- [Beveiligd om](concepts-security.md) gevoelige gegevens op rest en in beweging te beschermen
- [Automatische back-ups en herstel tijdstippen](concepts-business-continuity.md) tot 35 dagen


Alle deze mogelijkheden vereisen nauwelijks beheer, en alles wordt zonder extra kosten geleverd. Zo kunt u zich richten op Rapid Application Development en uw tijd op de markt versnellen, in plaats van kost bare tijd en resources voor het beheren van virtuele machines en infra structuur. U kunt door gaan met het ontwikkelen van uw toepassing met de open-source hulpprogram ma's en het platform van uw keuze, zonder dat u nieuwe vaardig heden hoeft te leren.

De implementatie optie voor één server biedt drie prijs Categorieën: Basic, Algemeen en geoptimaliseerd voor geheugen. Elke categorie biedt verschillende resources ter ondersteuning van de workloads van uw databases. U kunt uw eerste app op een kleine database bouwen voor een paar euro met maand en vervolgens de schaal ervan aanpassen om aan de vereisten van uw oplossing te voldoen. Doordat de schaalbaarheid dynamisch is, kan uw database op een transparante manier reageren op snel veranderende resourcevereisten. U betaalt alleen voor de resources die u nodig hebt op het moment dat u ze nodig hebt. Zie  [Prijscategorieën](concepts-pricing-tiers.md) voor meer informatie.

## <a name="azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL-grootschalige (Citus)
De grootschalige (Citus)-optie schaalt query's op meerdere computers horizon taal met behulp van sharding. De query engine parallelizes binnenkomende SQL-query's op deze servers voor snellere reacties op grote gegevens sets. Het is geschikt voor toepassingen die meer schaal en prestaties vereisen, in het algemeen werk belastingen die het nader benaderen of al groter dan 100 GB aan gegevens.

De implementatie optie grootschalige (Citus) levert het volgende:

- Horizon taal schalen op meerdere computers met behulp van sharding
- Parallel Lise ring op deze servers vragen voor snellere reacties op grote gegevens sets
- Uitstekende ondersteuning voor toepassingen met meerdere tenants, realtime operationele analyses en transactionele workloads voor hoge door Voer

Toepassingen die zijn gebouwd voor PostgreSQL kunnen gedistribueerde query's uitvoeren op grootschalige (Citus) met standaard [verbindings bibliotheken](./concepts-connection-libraries.md) en minimale wijzigingen.

## <a name="contacts"></a>Contactpersonen
Voor vragen of suggesties over het werken met Azure database for PostgreSQL stuurt u een e-mail naar het Azure database for PostgreSQL team ([ @Ask Azure DB voor postgresql](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Dit adres is voor algemene vragen in plaats van voor ondersteunings tickets.

Houd ook rekening met de volgende punten van contact persoon:
- Als u contact wilt opnemen met de ondersteuning van Azure of als u een probleem met uw account wilt oplossen, moet u [een ticket van de Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Als u feedback wilt geven of een nieuwe functie wilt aanvragen, maakt u een vermelding via [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Volgende stappen
- Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/postgresql/) voor kostprijs vergelijkingen en reken machines.
- Ga aan de slag door uw eerste Azure Database for PostgreSQL [één server](./quickstart-create-server-database-portal.md) of grootschalige te maken [(Citus)](./quickstart-create-hyperscale-portal.md)
- Bouw uw eerste app in Python, PHP, Ruby, C\#, Java, Node.js: [verbindingsbibliotheken](./concepts-connection-libraries.md)
