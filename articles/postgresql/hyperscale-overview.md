---
title: Overzicht van Azure Database for PostgreSQL - Hyperscale (Citus)
description: Geeft een overzicht van de implementatieoptie Hyperscale (Citus)
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 09/01/2020
ms.openlocfilehash: e62d1bf0e9db5e80193cb0615d0a9d31e3041d63
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/22/2020
ms.locfileid: "90943828"
---
# <a name="what-is-azure-database-for-postgresql---hyperscale-citus"></a>Wat is Azure Database for PostgreSQL - Hyperscale (Citus)?

Azure Database for PostgreSQL is een relationele databaseservice in de Microsoft Cloud die is gebouwd voor ontwikkelaars. Deze is gebaseerd op de communityversie van de opensource-[PostgreSQL](https://www.postgresql.org/)-database-engine.

Hyperscale (Citus) is een implementatieoptie waarmee query’s horizontaal over meerdere machines worden geschaald door middel van sharding. De query-engine zorgt voor query-parallellisatie van inkomende SQL-query’s over deze servers voor een snellere respons bij grote gegevenssets. Het is geschikt voor toepassingen die een grotere schaal en betere prestaties vereisen dan andere implementatieopties: meestal workloads die de 100 GB aan gegevens benaderen of overschrijden.

Hyperscale (Citus) levert:

- Horizontaal schalen over meerdere machines door middel van sharding
- Query-parallellisatie over deze servers voor een snellere respons bij grote gegevenssets
- Uitstekende ondersteuning voor multi-tenant toepassingen, realtime operationele analyse en transactionele workloads met hoge doorvoer

Toepassingen die zijn gebouwd voor PostgreSQL kunnen gedistribueerde query's uitvoeren op Hyperscale (Citus) met standaard [verbindingsbibliotheken](./concepts-connection-libraries.md) en minimale wijzigingen.

## <a name="next-steps"></a>Volgende stappen

- Ga aan de slag met het [maken van uw eerste](./quickstart-create-hyperscale-portal.md) servergroep met Azure Database for PostgreSQL - Hyperscale (Citus).
- Zie de [pagina met prijzen](https://azure.microsoft.com/pricing/details/postgresql/) voor kostenvergelijkingen en calculators. Hyperscale (Citus) biedt ook kortingen voor vooraf betaalde gereserveerde instanties. Zie [Hyperscale RI-prijzen](concepts-hyperscale-reserved-pricing.md) voor meer informatie.
- Bepaal de beste [begingrootte](howto-hyperscale-scaling.md#picking-initial-size) voor uw servergroep
