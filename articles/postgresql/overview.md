---
title: Overzicht van de relationele databaseservice Azure Database for PostgreSQL
description: Dit biedt een overzicht van de relationele databaseservice Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 11/25/2019
ms.openlocfilehash: deb720638e0921762eb9c5800d218c3cfccb7f55
ms.sourcegitcommit: 6fd28c1e5cf6872fb28691c7dd307a5e4bc71228
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/23/2020
ms.locfileid: "85213598"
---
# <a name="what-is-azure-database-for-postgresql"></a>Wat is Azure Database for PostgreSQL?
Azure Database for PostgreSQL is een relationele databaseservice in de Microsoft Cloud die is gebouwd voor ontwikkelaars. Deze is gebaseerd op de open-source [PostgreSQL](https://www.postgresql.org/)-database-engine en is beschikbaar in twee implementatieopties: Individuele server en Hyperscale (Citus).

## <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL - één server
De implementatieoptie Individuele server levert het volgende:

- Ingebouwde [hoge beschikbaarheid](concepts-high-availability.md) zonder extra kosten (99.99% SLA)
- Voorspelbare prestaties, tegen all-inclusive prijzen op basis van betalen per gebruik
- [Verticaal schalen naar behoefte](concepts-pricing-tiers.md), binnen enkele seconden
- [Bewaken en waarschuwen](concepts-monitoring.md) voor het evalueren van uw server
- Beveiliging en naleving van enterprise-kwaliteit
- [Beveiliging ter bescherming](concepts-security.md) van gevoelige niet-actieve en actieve gegevens
- [Automatische back-ups en herstel](concepts-business-continuity.md) naar een tijdstip tot 35 dagen geleden


Alle deze mogelijkheden vereisen nauwelijks beheer, en alles wordt zonder extra kosten geleverd. Dankzij deze mogelijkheden kunt u zich richten op het sneller ontwikkelen en op de markt brengen van toepassingen, in plaats van kostbare tijd en middelen te besteden aan het beheer van virtuele machines en infrastructuur. U kunt de opensourceprogramma's en het platform van uw keuze blijven gebruiken om uw toepassing verder te ontwikkelen zonder dat u nieuwe vaardigheden hoeft te leren.

De implementatieoptie Individuele server biedt de volgende drie prijscategorieën: Basic, Algemeen gebruik en Geoptimaliseerd voor geheugen. Elke categorie biedt verschillende resources ter ondersteuning van de workloads van uw databases. U kunt uw eerste app op een kleine database bouwen voor een paar euro met maand en vervolgens de schaal ervan aanpassen om aan de vereisten van uw oplossing te voldoen. Doordat de schaalbaarheid dynamisch is, kan uw database op een transparante manier reageren op snel veranderende resourcevereisten. U betaalt alleen voor de resources die u nodig hebt op het moment dat u ze nodig hebt. Zie  [Prijscategorieën](concepts-pricing-tiers.md) voor meer informatie.

## <a name="azure-database-for-postgresql---hyperscale-citus"></a>Azure Database for PostgreSQL - Hyperscale (Citus)
De opties Hyperscale (Citus) schaalt query’s horizontaal over meerdere machines door middel van sharding. De query-engine zorgt voor query-parallellisatie van inkomende SQL-query’s over deze servers voor een snellere respons bij grote gegevenssets. Het is geschikt voor toepassingen die een grotere schaal en betere prestaties vereisen, meestal workloads die de 100 GB benaderen of overschrijden.

De implementatieoptie Hyperscale (Citus) levert het volgende:

- Horizontaal schalen over meerdere machines door middel van sharding
- Query-parallellisatie over deze servers voor een snellere respons bij grote gegevenssets
- Uitstekende ondersteuning voor multi-tenant toepassingen, realtime operationele analyse en transactionele workloads met hoge doorvoer

Toepassingen die zijn gebouwd voor PostgreSQL kunnen gedistribueerde query's uitvoeren op Hyperscale (Citus) met standaard [verbindingsbibliotheken](./concepts-connection-libraries.md) en minimale wijzigingen.

## <a name="contacts"></a>Contactpersonen
Voor eventuele vragen of suggesties over het werken met Azure Database for PostgreSQL stuurt u een e-mailbericht naar het Azure Database for PostgreSQL-team ([@AskAzure DB voor PostgreSQL](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Dit adres is voor algemene vragen, niet voor ondersteuningstickets.

Overweeg ook deze aanspreekpunten, indien van toepassing:
- Als u contact wilt opnemen met Azure-ondersteuning of een probleem met uw account wilt oplossen, kunt u een [ticket indienen vanuit Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Als u feedback wilt geven of een nieuwe functie wilt aanvragen, maakt u een vermelding via [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Volgende stappen
- Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/postgresql/) voor kostenvergelijkingen en calculators. De implementatieopties Individuele server en Hyperscale (Citus) bieden allebei kortingen voor vooraf betaalde gereserveerde instanties. Zie de pagina’s [Prijzen voor gereserveerde instanties van Individuele server](concept-reserved-pricing.md) en [Prijzen voor gereserveerde instanties van Hyperscale](concepts-hyperscale-reserved-pricing.md) voor meer informatie.
- Ga aan de slag met het maken van uw eerste Azure Database for PostgreSQL [Individuele server](./quickstart-create-server-database-portal.md) of [Hyperscale (Citus)](./quickstart-create-hyperscale-portal.md)
- Bouw uw eerste app in Python, PHP, Ruby, C\#, Java, Node.js: [verbindingsbibliotheken](./concepts-connection-libraries.md)
