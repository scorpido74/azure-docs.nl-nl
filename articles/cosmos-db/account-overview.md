---
title: Werken met Azure Cosmos DB accounts
description: In dit artikel wordt beschreven hoe u Azure Cosmos DB-accounts maakt en gebruikt
author: rimman
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: conceptual
ms.date: 07/23/2019
ms.author: rimman
ms.reviewer: sngun
ms.openlocfilehash: 833d8533ff3289693e1744db2d77d4bed6a9ea69
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69616999"
---
# <a name="work-with-azure-cosmos-account"></a>Werken met een Azure Cosmos-account

Azure Cosmos DB is een volledig beheerd platform-as-a-Service (PaaS). Als u Azure Cosmos DB wilt gaan gebruiken, moet u in eerste instantie een Azure Cosmos-account maken in uw Azure-abonnement. Uw Azure Cosmos-account bevat een unieke DNS-naam en u kunt een account beheren door gebruik te maken van Azure Portal, Azure CLI of door andere taalspecifieke Sdk's te gebruiken. Zie [How to manage your Azure Cosmos account](how-to-manage-database-account.md)(Engelstalig) voor meer informatie.

Het Azure Cosmos-account is de fundamentele eenheid van wereld wijde distributie en hoge Beschik baarheid. Voor een wereld wijde distributie van uw gegevens en door Voer in meerdere Azure-regio's kunt u op elk gewenst moment Azure-regio's toevoegen aan en verwijderen uit uw Azure Cosmos-account. U kunt uw Azure Cosmos-account configureren om een of meerdere schrijf regio's te hebben. Zie [Azure-regio's toevoegen aan en verwijderen uit uw Azure Cosmos-account](how-to-manage-database-account.md)voor meer informatie. U kunt het [standaard consistentie](consistency-levels.md) niveau configureren voor het Azure Cosmos-account. Azure Cosmos DB biedt uitgebreide Sla's die de door Voer, latentie op het 99e percentiel, consistentie en hoge Beschik baarheid omvatten. Zie [Azure Cosmos DB sla's](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)voor meer informatie.

Als u de toegang tot alle gegevens in uw Azure Cosmos-account veilig wilt beheren, kunt u de [hoofd sleutels](secure-access-to-data.md) gebruiken die aan uw account zijn gekoppeld. Als u de toegang tot uw gegevens verder wilt beveiligen, kunt u een [VNET-service-eind punt](vnet-service-endpoint.md) en [IP-firewall](firewall-support.md) configureren voor uw Azure Cosmos-account. 

## <a name="elements-in-an-azure-cosmos-account"></a>Elementen in een Azure Cosmos-account

Azure Cosmos-container is de fundamentele eenheid voor schaal baarheid. U kunt vrijwel een onbeperkte ingerichte door Voer (RU/s) en opslag in een container hebben. Azure Cosmos DB uw container op transparante wijze partitioneren met behulp van de logische partitie sleutel die u opgeeft om uw ingerichte door Voer en opslag elastisch te schalen. Zie [werken met Azure Cosmos-containers en-items](databases-containers-items.md)voor meer informatie.

Op dit moment kunt u een maximum van 100 Azure Cosmos-accounts maken onder een Azure-abonnement. Met één Azure Cosmos-account kunt u een onbeperkte hoeveelheid gegevens en ingerichte door Voer beheren. Als u uw gegevens en de ingerichte door voer wilt beheren, kunt u een of meer Azure Cosmos-data bases maken onder uw account en binnen die data base, kunt u een of meer containers maken. In de volgende afbeelding ziet u de hiërarchie van elementen in een Azure Cosmos-account:

![Hiërarchie van een Azure Cosmos-account](./media/account-overview/hierarchy.png)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het beheren van uw Azure Cosmos-account en andere concepten:

* [Uw Azure Cosmos-account beheren](how-to-manage-database-account.md)
* [Wereldwijde distributie](distribute-data-globally.md)
* [Consistentie niveaus](consistency-levels.md)
* [Werken met Azure Cosmos-containers en-items](databases-containers-items.md)
* [VNET-service-eind punt voor uw Azure Cosmos-account](vnet-service-endpoint.md)
* [IP-Firewall voor uw Azure Cosmos-account](firewall-support.md)
* [Azure-regio's toevoegen aan en verwijderen uit uw Azure Cosmos-account](how-to-manage-database-account.md)
* [Azure Cosmos DB Sla's](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
