---
title: Regionale aanwezigheid met Azure Cosmos DB
description: In dit artikel wordt uitgelegd over de regionale aanwezigheid van Azure Cosmos DB en verschillende cloudomgevingen.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.custom: seodec18
ms.openlocfilehash: 95b9a9804fe1a9473d226912f178284f701a1547
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72753233"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Regionale aanwezigheid met Azure Cosmos DB

Azure Cosmos DB is een basisservice in Azure en is standaard altijd beschikbaar in alle regio's waar Azure beschikbaar is. Momenteel is Azure beschikbaar in [54 regio's](https://azure.microsoft.com/global-infrastructure/regions/) wereldwijd. 

[![Regio's waar Azure Cosmos DB beschikbaar is](./media/regional-presence/regional-presence.png)](./media/regional-presence/regional-presence.png#lightbox)

Cosmos DB is beschikbaar in alle vijf verschillende Azure-cloudomgevingen die beschikbaar zijn voor klanten:

* **Azure public** cloud, die wereldwijd beschikbaar is.

* **Azure China 21Vianet** is beschikbaar via een unieke samenwerking tussen Microsoft en 21Vianet, een van de grootste internetproviders van het land in China.

* **Azure Germany** levert diensten onder een data trustee-model, dat ervoor zorgt dat klantgegevens in Duitsland onder de controle blijven van T-Systems International GmbH, een dochteronderneming van Deutsche Telecom, die optreedt als de Duitse datatrustee.

* **Azure Government** is beschikbaar in vier regio's in de Verenigde Staten voor Amerikaanse overheidsinstanties en hun partners. 

* **Azure Government for Department of Defense (DoD)** is beschikbaar in twee regio's in de Verenigde Staten voor het Amerikaanse ministerie van Defensie.

## <a name="regional-presence-with-global-distribution"></a>Regionale aanwezigheid met wereldwijde distributie

Alle API's die worden blootgesteld door Azure Cosmos DB (inclusief SQL, MongoDB, Cassandra, Gremlin en Tabel) zijn standaard beschikbaar in alle Azure-regio's. U bijvoorbeeld MongoDB en Cassandra API's laten zien door Azure Cosmos DB, niet alleen in alle wereldwijde Azure-regio's, maar ook in soevereine clouds zoals China, Duitsland, de overheid en het Ministerie van Defensie (DoD).

Azure Cosmos DB is een [wereldwijd gedistribueerde](distribute-data-globally.md) databaseservice. U een willekeurig aantal Azure-regio's koppelen aan uw Azure Cosmos-account en uw gegevens worden automatisch en transparant gerepliceerd. U op elk gewenst moment een regio toevoegen of verwijderen aan uw Azure Cosmos-account. Met het kant-en-klare wereldwijde distributiemogelijkheid en het replicatieprotocol met meerdere mastered biedt Azure Cosmos DB minder dan 10 ms lees- en schrijflatencies op het 99e percentiel, de beschikbaarheid van 99.999 lees- en schrijfgegevens en de mogelijkheid om de ingerichte doorvoer voor lees- en schrijfbewerkingen in alle regio's die zijn gekoppeld aan uw Azure Cosmos-account. Azure Cosmos DB biedt ook vijf goed gedefinieerde consistentiemodellen en u ervoor kiezen om een specifiek consistentiemodel toe te passen op uw gegevens. Ten slotte is Azure Cosmos DB de enige databaseservice in de branche die een uitgebreide [Service Level Agreement (SLA)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) biedt die een ingerichte doorvoer, latentie op het 99e percentiel, hoge beschikbaarheid en consistentie omvat. De bovenstaande mogelijkheden zijn beschikbaar in alle Azure-clouds.

## <a name="next-steps"></a>Volgende stappen

U nu meer te weten komen over de kernconcepten van Azure Cosmos DB met de volgende artikelen:

* [Wereldwijde gegevensdistributie](distribute-data-globally.md)
* [Een Azure Cosmos DB-account beheren](manage-account.md)
* [Doorvoer voorziening voor Azure Cosmos-containers en -databases](set-throughput.md)
* [Azure Cosmos DB SLA](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
