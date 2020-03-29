---
title: Doorvoer schalen in Azure Cosmos DB
description: In dit artikel wordt beschreven hoe Azure Cosmos DB de doorvoer schaalt in verschillende regio's waar het Azure Cosmos-account is ingericht.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 12/02/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 440f23afcd08326261be30432ad1f0ecb16f55fd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74873502"
---
# <a name="globally-scale-provisioned-throughput"></a>Wereldwijd schalen van ingerichte doorvoer 

In Azure Cosmos DB wordt de ingerichte doorvoer weergegeven als aanvraageenheden/seconde (RU/s of het meervoudsformulier RU's). R's meten de kosten van zowel lees- als schrijfbewerkingen ten opzichte van uw Cosmos-container, zoals weergegeven in de volgende afbeelding:

![Aanvraageenheden](./media/scaling-throughput/request-unit-charge-of-read-and-write-operations.png)

U RU's inrichten op een Cosmos-container of een Cosmos-database. DE's die op een container zijn ingericht, zijn uitsluitend beschikbaar voor de bewerkingen die op die container worden uitgevoerd. RALLY's die in een database zijn ingericht, worden gedeeld tussen alle containers in die database (met uitzondering van containers met uitsluitend toegewezen RU's).

Voor elastischschalen van de ingerichte doorvoer u de ingerichte RU/s op elk gewenst moment verhogen of verlagen. Zie Voor meer informatie [How-to-provision throughput](set-throughput.md) en om Cosmos-containers en databases elastisch te schalen. Voor wereldwijde schaaldoorvoer u op elk gewenst moment regio's toevoegen of verwijderen uit uw Cosmos-account. Zie [Regio's toevoegen/verwijderen uit uw databaseaccount voor](how-to-manage-database-account.md#addremove-regions-from-your-database-account)meer informatie . Het koppelen van meerdere regio's aan een Cosmos-account is belangrijk in veel scenario's - om lage latentie en [hoge beschikbaarheid](high-availability.md) over de hele wereld te bereiken.

## <a name="how-provisioned-throughput-is-distributed-across-regions"></a>Hoe de ingerichte doorvoer over regio's wordt verdeeld

Als u *'R'* R's indient op een Cosmos-container (of database), zorgt Cosmos DB ervoor dat *'R'* RU's beschikbaar zijn in *elke* regio die is gekoppeld aan uw Cosmos-account. Elke keer dat u een nieuwe regio aan uw account toevoegt, voorziet Cosmos DB automatisch *'R'* R's in het nieuw toegevoegde gebied. De bewerkingen die worden uitgevoerd met uw Cosmos-container krijgen gegarandeerd *'R'* RU's in elke regio. U geen RU's selectief toewijzen aan een specifieke regio. De R's die zijn ingericht op een Cosmos-container (of database) worden ingericht in alle regio's die zijn gekoppeld aan uw Cosmos-account.

Ervan uitgaande dat een Cosmos-container is geconfigureerd met *'R'* R's en er *'N'-regio's* zijn gekoppeld aan het Cosmos-account, dan:

- Als het Cosmos-account is geconfigureerd met één schrijfgebied, zijn de totale RU's wereldwijd beschikbaar op de container = *R* x *N*.

- Als het Cosmos-account is geconfigureerd met meerdere schrijfregio's, zijn de totale R's wereldwijd beschikbaar op de container = *R* x (*N*+1). De *R* extra R-R's worden automatisch ingericht om updateconflicten en anti-entropieverkeer in de regio's te verwerken.

Uw keuze van [consistentiemodel](consistency-levels.md) heeft ook invloed op de doorvoer. U ongeveer 2x leesdoorvoer krijgen voor de meer ontspannen consistentieniveaus (bijvoorbeeld *sessie,* *consistent voorvoegsel* en *uiteindelijke* consistentie) in vergelijking met sterkere consistentieniveaus (bijvoorbeeld *begrensde staleness* of *sterke* consistentie).

## <a name="next-steps"></a>Volgende stappen

Vervolgens u leren hoe u doorvoer configureert in een container of database:

* [Doorvoer voor containers en databases instellen en instellen](set-throughput.md) 

