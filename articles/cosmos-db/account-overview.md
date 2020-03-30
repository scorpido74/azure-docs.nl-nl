---
title: Werken met Azure Cosmos DB-accounts
description: In dit artikel wordt beschreven hoe Azure Cosmos-accounts worden gemaakt en gebruikt. Het toont ook de hiërarchie van elementen in een Azure Cosmos-account
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.openlocfilehash: d29ed68b2945b2473b33aa88176e6f5d832a0fba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79246901"
---
# <a name="work-with-azure-cosmos-account"></a>Werken met een Azure Cosmos-account

Azure Cosmos DB is een volledig beheerde platform-as-a-service (PaaS). Als u Azure Cosmos DB wilt gaan gebruiken, moet u in eerste instantie een Azure Cosmos-account maken in uw Azure-abonnement. Uw Azure Cosmos-account bevat een unieke DNS-naam en u een account beheren met Azure-portal, Azure CLI of met verschillende taalspecifieke SDK's. Zie voor meer informatie [hoe u uw Azure Cosmos-account beheert](how-to-manage-database-account.md).

Het Azure Cosmos-account is de fundamentele eenheid van wereldwijde distributie en hoge beschikbaarheid. Voor het wereldwijd distribueren van uw gegevens en doorvoer over meerdere Azure-regio's, u azure-regio's op elk gewenst moment toevoegen en verwijderen aan uw Azure Cosmos-account. U uw Azure Cosmos-account zo configureren dat er één of meerdere schrijfregio's zijn. Zie voor meer informatie [hoe u Azure-regio's toevoegt en verwijdert aan uw Azure Cosmos-account.](how-to-manage-database-account.md) U het [standaardconsistentieniveau](consistency-levels.md) configureren op azure cosmos-account. Azure Cosmos DB biedt uitgebreide SLA's met doorvoer, latentie bij het 99e percentiel, consistentie en hoge beschikbaarheid. Zie [Azure Cosmos D-SLA's voor](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)meer informatie .

Als u de toegang tot alle gegevens in uw Azure Cosmos-account veilig wilt beheren, u de [hoofdsleutels](secure-access-to-data.md) gebruiken die aan uw account zijn gekoppeld. Als u de toegang tot uw gegevens verder wilt beveiligen, u een [VNET-serviceeindpunt](vnet-service-endpoint.md) en [IP-firewall](firewall-support.md) configureren op uw Azure Cosmos-account. 

## <a name="elements-in-an-azure-cosmos-account"></a>Elementen in een Azure Cosmos-account

Azure Cosmos-container is de fundamentele eenheid van schaalbaarheid. U vrijwel een onbeperkte ingerichte doorvoer (RU/s) en opslag op een container hebben. Azure Cosmos DB partitiet uw container op transparante wijze met behulp van de logische partitiesleutel die u opgeeft om uw ingerichte doorvoer en opslag elastisch te schalen. Zie [Werken met Azure Cosmos-containers en -items](databases-containers-items.md)voor meer informatie.

Momenteel u maximaal 100 Azure Cosmos-accounts maken onder een Azure-abonnement. Eén Azure Cosmos-account kan vrijwel onbeperkte hoeveelheid gegevens en ingerichte doorvoer beheren. Als u uw gegevens en ingerichte doorvoer wilt beheren, u een of meer Azure Cosmos-databases maken onder uw account en binnen die database u een of meer containers maken. In de volgende afbeelding wordt de hiërarchie van elementen in een Azure Cosmos-account weergegeven:

![Hiërarchie van een Azure Cosmos-account](./media/account-overview/hierarchy.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het beheren van uw Azure Cosmos-account en andere concepten:

* [Uw Azure Cosmos-account beheren](how-to-manage-database-account.md)
* [Wereldwijde distributie](distribute-data-globally.md)
* [Consistentieniveaus](consistency-levels.md)
* [Werken met Azure Cosmos-containers en -items](databases-containers-items.md)
* [VNET-serviceeindpunt voor uw Azure Cosmos-account](vnet-service-endpoint.md)
* [IP-firewall voor uw Azure Cosmos-account](firewall-support.md)
* [Azure-regio's toevoegen en verwijderen aan uw Azure Cosmos-account](how-to-manage-database-account.md)
* [Azure Cosmos DB SLA's](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
