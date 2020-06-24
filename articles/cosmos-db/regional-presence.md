---
title: Regionale aanwezigheid met Azure Cosmos DB
description: In dit artikel wordt uitgelegd wat de regionale aanwezigheid van Azure Cosmos DB en verschillende Cloud omgevingen zijn.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.custom: seodec18
ms.openlocfilehash: 78f02c07932c6a058ba4fa27289640b7efd56169
ms.sourcegitcommit: 23604d54077318f34062099ed1128d447989eea8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/20/2020
ms.locfileid: "85113667"
---
# <a name="regional-presence-with-azure-cosmos-db"></a>Regionale aanwezigheid met Azure Cosmos DB

Azure Cosmos DB is een Foundational service in Azure en is standaard altijd beschikbaar in alle regio's, waar Azure beschikbaar is. Azure is momenteel beschikbaar in [54 regio's](https://azure.microsoft.com/global-infrastructure/regions/) wereld wijd. 

:::image type="content" source="./media/regional-presence/regional-presence.png" alt-text="Regio's waar Azure Cosmos DB beschikbaar is" lightbox="./media/regional-presence/regional-presence.png" border="false":::

Cosmos DB is beschikbaar in alle vijf verschillende Azure-Cloud omgevingen die beschikbaar zijn voor klanten:

* **Open bare Azure** -Cloud, die wereld wijd beschikbaar is.

* **Azure China 21vianet** is beschikbaar via een unieke samen werking tussen micro soft en 21vianet, een van de grootste Internet providers van het land in China.

* **Azure Duitsland** biedt services onder een model van de gegevens beheerder, wat ervoor zorgt dat klant gegevens in Duitsland blijven, onder controle van T-Systems International GmbH, een dochter onderneming van Deutsche Telecom, die fungeert als de Duitse gegevens beheerder.

* **Azure Government** is beschikbaar in vier regio's van de Verenigde Staten naar overheids instanties van de Verenigde Staten en hun partners. 

* **Azure Government voor ministerie van defensie (DoD)** is beschikbaar in twee regio's in de Verenigde Staten van het Amerikaanse ministerie van defensie.

## <a name="regional-presence-with-global-distribution"></a>Regionale aanwezigheid met wereld wijde distributie

Alle Api's die worden weer gegeven door Azure Cosmos DB (inclusief SQL, MongoDB, Cassandra, Gremlin en Table) zijn standaard beschikbaar in alle Azure-regio's. U kunt bijvoorbeeld MongoDB-en Cassandra-Api's weer gegeven door Azure Cosmos DB niet alleen in alle wereld wijde Azure-regio's, maar ook in soevereine Clouds, zoals China, Duitsland, overheids instellingen en DoD-regio's (Ministerie van defensie).

Azure Cosmos DB is een [wereld wijd gedistribueerde](distribute-data-globally.md) database service. U kunt een wille keurig aantal Azure-regio's koppelen aan uw Azure Cosmos-account en uw gegevens worden automatisch en transparant gerepliceerd. U kunt op elk gewenst moment een regio toevoegen aan of verwijderen uit uw Azure Cosmos-account. Met de kant-en-klare wereld wijde distributie en het replicatie protocol met meerdere masters biedt Azure Cosmos DB minder dan 10 MS Lees-en schrijf latenties voor het 99e-percentiel, 99,999 Lees-en schrijf beschikbaarheid en de mogelijkheid om de ingerichte door Voer voor lees-en schrijf bewerkingen op een elastische schaal te schalen voor alle regio's die zijn gekoppeld aan uw Azure Cosmos-account. Azure Cosmos DB biedt ook vijf goed gedefinieerde consistentie modellen en u kunt ervoor kiezen om een specifiek consistentie model toe te passen op uw gegevens. Ten slotte is Azure Cosmos DB de enige database service in de branche met een uitgebreide [Service Level Agreement (Sla)](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/) die voorziet in de ingerichte door Voer, latentie bij het 99e percentiel, hoge Beschik baarheid en consistentie. De bovenstaande mogelijkheden zijn beschikbaar in alle Azure-Clouds.

## <a name="next-steps"></a>Volgende stappen

U kunt nu meer informatie over de basis concepten van Azure Cosmos DB met de volgende artikelen:

* [Distributie van globale gegevens](distribute-data-globally.md)
* [Een Azure Cosmos DB-account beheren](manage-account.md)
* [Door Voer inrichten voor Azure Cosmos-containers en-data bases](set-throughput.md)
* [SLA voor Azure Cosmos DB](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_2/)
