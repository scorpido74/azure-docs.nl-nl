---
title: Overzicht van de relationele databaseservice Azure Database for PostgreSQL
description: Dit biedt een overzicht van de relationele databaseservice Azure Database for PostgreSQL.
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.topic: overview
ms.date: 05/06/2019
ms.openlocfilehash: 5194cf51fd7f1debeba76edb48e8377919ae448a
ms.sourcegitcommit: 42748f80351b336b7a5b6335786096da49febf6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2019
ms.locfileid: "72177951"
---
# <a name="what-is-azure-database-for-postgresql"></a>Wat is Azure Database for PostgreSQL?
Azure Database for PostgreSQL is een relationele database service in de micro soft Cloud die is gebouwd voor ontwikkel aars. Het is gebaseerd op de Community-versie van de open-source [postgresql](https://www.postgresql.org/) data base-engine en is beschikbaar in twee implementatie opties: Eén server en grootschalige (Citus) (preview-versie).

## <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL-één server
De implementatie optie voor één server levert het volgende:

- Ingebouwde hoge Beschik baarheid zonder extra kosten (99,99% SLA)
- Voorspelbare prestaties, tegen all-inclusive prijzen op basis van betalen per gebruik
- Verticale schaal indien nodig binnen enkele seconden
- Bewaking en waarschuwingen om snel de gevolgen van schalen te beoordelen
- Beveiliging ter bescherming van gevoelige niet-actieve en actieve gegevens
- Automatische back-ups en herstel naar een tijdstip tot 35 dagen geleden
- Beveiliging en naleving van enterprise-kwaliteit

Alle deze mogelijkheden vereisen nauwelijks beheer, en alles wordt zonder extra kosten geleverd. Zo kunt u zich richten op Rapid Application Development en uw tijd op de markt versnellen, in plaats van kost bare tijd en resources voor het beheren van virtuele machines en infra structuur. U kunt door gaan met het ontwikkelen van uw toepassing met de open-source hulpprogram ma's en het platform van uw keuze, zonder dat u nieuwe vaardig heden hoeft te leren.

De implementatie optie voor één server biedt drie prijs Categorieën: Basic, Algemeen gebruik en Geoptimaliseerd voor geheugen. Elke categorie biedt verschillende resources ter ondersteuning van de workloads van uw databases. U kunt uw eerste app op een kleine database bouwen voor een paar euro met maand en vervolgens de schaal ervan aanpassen om aan de vereisten van uw oplossing te voldoen. Doordat de schaalbaarheid dynamisch is, kan uw database op een transparante manier reageren op snel veranderende resourcevereisten. U betaalt alleen voor de resources die u nodig hebt op het moment dat u ze nodig hebt. Zie  [Prijscategorieën](concepts-pricing-tiers.md) voor meer informatie.

## <a name="azure-database-for-postgresql---hyperscale-citus-preview"></a>Azure Database for PostgreSQL-grootschalige (Citus) (preview-versie)
De grootschalige (Citus)-optie schaalt query's op meerdere computers horizon taal met behulp van sharding. De query engine parallelizes binnenkomende SQL-query's op deze servers voor snellere reacties op grote gegevens sets. Het is geschikt voor toepassingen die meer schaal en prestaties vereisen, in het algemeen werk belastingen die het nader benaderen of al groter dan 100 GB aan gegevens.

De implementatie optie grootschalige (Citus) levert het volgende:

- Horizon taal schalen op meerdere computers met behulp van sharding
- Parallel Lise ring op deze servers vragen voor snellere reacties op grote gegevens sets
- Uitstekende ondersteuning voor toepassingen met meerdere tenants, realtime operationele analyses en transactionele workloads voor hoge door Voer

Toepassingen die zijn gebouwd voor PostgreSQL kunnen gedistribueerde query's uitvoeren op grootschalige (Citus) met standaard [verbindings bibliotheken](./concepts-connection-libraries.md) en minimale wijzigingen.

Houd er rekening mee dat grootschalige (Citus) zich in de open bare preview bevindt en dat er nog geen SLA wordt aangeboden.

## <a name="data-security"></a>Gegevensbeveiliging
Azure Database for PostgreSQL de traditie van gegevens beveiliging van Azure Data Base Services worden opgeslagen. Het bevat functies die de toegang beperken, gegevens op rest en in beweging beveiligen en u helpen bij het bewaken van activiteiten. Bezoek het [Vertrouwenscentrum van Azure](https://azure.microsoft.com/overview/trusted-cloud/) voor informatie over de beveiliging van het Azure-platform.

De Azure Database for PostgreSQL-service gebruikt de door FIPS 140-2 gevalideerde cryptografische module voor opslag versleuteling van gegevens in rust. Gegevens, met inbegrip van back-ups, worden op schijf versleuteld, met uitzonde ring van tijdelijke bestanden die worden gemaakt tijdens het uitvoeren van query's. De service maakt gebruik van de AES 256-bits code ring opgenomen in azure Storage-versleuteling en de sleutels worden beheerd door het systeem. Opslagversleuteling is altijd actief en kan niet worden uitgeschakeld. De Azure Database for PostgreSQL-service vereist standaard beveiligde verbindingen voor gegevens in-motion in zowel het netwerk als tussen de data base en de client toepassing.

## <a name="contacts"></a>Contactpersonen
Voor vragen of suggesties over het werken met Azure Database for PostgreSQL stuurt u een e-mail bericht naar het Azure Database for PostgreSQL team ([@Ask Azure DB voor postgresql](mailto:AskAzureDBforPostgreSQL@service.microsoft.com)). Dit adres is voor algemene vragen in plaats van voor ondersteunings tickets.

Houd ook rekening met de volgende punten van contact persoon:
- Als u contact wilt opnemen met de ondersteuning van Azure of als u een probleem met uw account wilt oplossen, moet u [een ticket van de Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).
- Als u feedback wilt geven of een nieuwe functie wilt aanvragen, maakt u een vermelding via [UserVoice](https://feedback.azure.com/forums/597976-azure-database-for-postgresql).

## <a name="next-steps"></a>Volgende stappen
- Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/postgresql/) voor kostenvergelijkingen en calculators.
- Ga aan de slag door uw eerste Azure Database for PostgreSQL [één server](./quickstart-create-server-database-portal.md) of [grootschalige (Citus) te maken (preview)](./quickstart-create-hyperscale-portal.md)
- Bouw uw eerste app in Python, PHP, Ruby, C\#, Java, Node.js: [verbindingsbibliotheken](./concepts-connection-libraries.md)
