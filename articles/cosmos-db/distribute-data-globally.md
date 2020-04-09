---
title: Gegevens wereldwijd distribueren met Azure Cosmos DB
description: Meer informatie over georeplicatie op planeetschaal, multi-master, failover en gegevensherstel met behulp van wereldwijde databases van Azure Cosmos DB, een wereldwijd gedistribueerde databaseservice met meerdere modellen.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: ef511c203caa11aad4dea1047c982158810c4038
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982120"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>Globale gegevensdistributie met Azure Cosmos DB - overzicht

De toepassingen van vandaag zijn vereist om zeer responsief en altijd online te zijn. Voor het bereiken van lage latentie en hoge beschikbaarheid moeten de instanties van deze toepassingen worden geïmplementeerd in datacenters die zich dicht bij de gebruikers ervan bevinden. Deze toepassingen worden meestal geïmplementeerd in meerdere datacenters en worden wereldwijd gedistribueerd genoemd. Globaal gedistribueerde toepassingen hebben een globaal gedistribueerde database nodig die overal ter wereld transparant gegevens kan repliceren om de toepassingen in staat te stellen te werken met een kopie van de gegevens die zich dicht bij de gebruikers bevindt. 

Azure Cosmos DB is een wereldwijd gedistribueerde databaseservice die is ontworpen om lage latentie, elastische schaalbaarheid van doorvoer, goed gedefinieerde semantiek voor gegevensconsistentie en hoge beschikbaarheid te bieden. Kortom, als uw toepassing overal ter wereld gegarandeerde snelle responstijd nodig heeft, als deze altijd online moet zijn en onbeperkte en elastische schaalbaarheid van doorvoer en opslag nodig heeft, moet u uw toepassing bouwen op Azure Cosmos DB.

U instellen dat uw databases wereldwijd worden gedistribueerd en beschikbaar zijn in een van de Azure-regio's. Als u de latentie wilt verlagen, plaatst u de gegevens dicht bij de plaats waar uw gebruikers zich bevinden. Het kiezen van de vereiste regio's is afhankelijk van het wereldwijde bereik van uw toepassing en waar uw gebruikers zich bevinden. Cosmos DB repliceert de gegevens op transparante wijze naar alle regio's die zijn gekoppeld aan uw Cosmos-account. Het biedt één systeemafbeelding van uw wereldwijd gedistribueerde Azure Cosmos-database en containers die uw toepassing lokaal kan lezen en schrijven. 

Met Azure Cosmos DB u de regio's die aan uw account zijn gekoppeld, op elk gewenst moment toevoegen of verwijderen. Uw toepassing hoeft niet te worden onderbroken of opnieuw te worden geïmplementeerd om een regio toe te voegen of te verwijderen. Het blijft zeer beschikbaar de hele tijd als gevolg van de multi-homing mogelijkheden die de dienst native biedt.

![Hoogst beschikbare implementatietopologie](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>Belangrijkste voordelen van wereldwijde distributie

**Bouw wereldwijde actieve apps.** Met zijn nieuwe multi-master replicatieprotocol ondersteunt elke regio zowel schrijft als leest. De multi-master mogelijkheid maakt het ook mogelijk:

- Onbeperkt elastisch schrijven en lezen schaalbaarheid. 
- 99,999% lezen en schrijven beschikbaarheid over de hele wereld.
- Gegarandeerd leest en schrijft geserveerd in minder dan 10 milliseconden op de 99e percentiel.

Door gebruik te maken van de Azure Cosmos DB multi-homing API's is uw toepassing op de hoogte van de dichtstbijzijnde regio en kan het aanvragen naar die regio verzenden. Het dichtstbijzijnde gebied wordt geïdentificeerd zonder configuratiewijzigingen. Als u regio's toevoegt en verwijdert van en naar uw Azure Cosmos-account, hoeft uw toepassing niet opnieuw te worden geïmplementeerd of onderbroken, maar blijft deze te allen tijde zeer beschikbaar.

**Bouw zeer responsieve apps.** Uw toepassing kan bijna realtime lezen en schrijven uitvoeren tegen alle regio's die u voor uw database hebt gekozen. Azure Cosmos DB verwerkt intern de gegevensreplicatie tussen regio's met consistentieniveaugaranties van het geselecteerde niveau.

**Bouw zeer beschikbare apps.** Het uitvoeren van een database in meerdere regio's wereldwijd verhoogt de beschikbaarheid van een database. Als één regio niet beschikbaar is, verwerken andere regio's automatisch toepassingsaanvragen. Azure Cosmos DB biedt 99,999% lees- en schrijfbeschikbaarheid voor databases met meerdere regio's.

**Zorg voor continuïteit van het bedrijf tijdens regionale uitval.** Azure Cosmos DB ondersteunt [automatische failover](how-to-manage-database-account.md#automatic-failover) tijdens een regionale storing. Tijdens een regionale storing blijft Azure Cosmos DB zijn latentie, beschikbaarheid, consistentie en doorvoerSLA's behouden. Om ervoor te zorgen dat uw hele toepassing zeer beschikbaar is, biedt Cosmos DB een handmatige failover-API om een regionale storing te simuleren. Door gebruik te maken van deze API u regelmatig bedrijfscontinuïteitsoefeningen uitvoeren.

**Schaal lees- en schrijfdoorvoer wereldwijd.** U elke regio in staat stellen om schrijfbaar en elastisch schaal leest en schrijft over de hele wereld. De doorvoer die uw toepassing configureert op een Azure Cosmos-database of een container, wordt gegarandeerd geleverd in alle regio's die zijn gekoppeld aan uw Azure Cosmos-account. De ingerichte doorvoer wordt gegarandeerd door [financieel gesteunde SLA 's](https://azure.microsoft.com/support/legal/sla/cosmos-db/v1_3/).

**Kies uit verschillende goed gedefinieerde consistentiemodellen.** Het Azure Cosmos DB-replicatieprotocol biedt vijf goed gedefinieerde, praktische en intuïtieve consistentiemodellen. Elk model heeft een afweging tussen consistentie en prestaties. Gebruik deze consistentiemodellen om eenvoudig wereldwijd gedistribueerde toepassingen te bouwen.

## <a name="next-steps"></a><a id="Next Steps"></a>Volgende stappen

Lees meer over wereldwijde distributie in de volgende artikelen:

* [Wereldwijde distributie - achter de schermen](global-dist-under-the-hood.md)
* [Multimaster configureren in uw toepassingen](how-to-multi-master.md)
* [Clients configureren voor multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Regio's toevoegen of verwijderen uit uw Azure Cosmos DB-account](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Een aangepast conflictoplossingsbeleid voor SQL API-accounts maken](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Programmeerbare consistentiemodellen in Cosmos DB](consistency-levels.md)
* [Kies het juiste consistentieniveau voor uw toepassing](consistency-levels-choosing.md)
* [Consistentieniveaus in Azure Cosmos DB API's](consistency-levels-across-apis.md)
* [Beschikbaarheid en prestatieafwegingen voor verschillende consistentieniveaus](consistency-levels-tradeoffs.md)

