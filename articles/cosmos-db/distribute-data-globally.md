---
title: Gegevens globaal distribueren met Azure Cosmos DB
description: Meer informatie over het schalen van geo-replicatie, multi-master, failover en herstel van gegevens met behulp van globale data bases van Azure Cosmos DB, een wereld wijd gedistribueerde, multi-model database service.
author: markjbrown
ms.author: mjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.openlocfilehash: 84cd201cd758293082a61a87528332c7d8c58811
ms.sourcegitcommit: 2a2af81e79a47510e7dea2efb9a8efb616da41f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2020
ms.locfileid: "76264302"
---
# <a name="global-data-distribution-with-azure-cosmos-db---overview"></a>Distributie van globale gegevens met Azure Cosmos DB-overzicht

Van toepassingen wordt tegenwoordig vereist dat ze zeer responsief en altijd online zijn. Voor het bereiken van lage latentie en hoge beschikbaarheid moeten de instanties van deze toepassingen worden geïmplementeerd in datacenters die zich dicht bij de gebruikers ervan bevinden. Deze toepassingen worden doorgaans geïmplementeerd in meerdere data centers en worden wereld wijd gedistribueerd genoemd. Wereld wijd gedistribueerde toepassingen hebben een wereld wijd gedistribueerde data base nodig die de gegevens overal ter wereld transparant kan repliceren, zodat de toepassingen kunnen werken op een kopie van de gegevens die zich dicht bij de gebruikers bevinden. 

Azure Cosmos DB is een wereld wijd gedistribueerde database service die is ontworpen om te voorzien in een lage latentie, elastische schaal baarheid van door Voer, goed gedefinieerde semantiek voor consistentie van gegevens en hoge Beschik baarheid. Kortom, als uw toepassing een snelle reactie tijd van de wereld nodig heeft, als deze altijd online moet zijn, en u een onbeperkte en elastische schaal baarheid van door Voer en opslag nodig hebt, moet u uw toepassing bouwen op Azure Cosmos DB.

U kunt uw data bases zo configureren dat deze globaal worden gedistribueerd en beschikbaar zijn in een van de Azure-regio's. Als u de latentie wilt verlagen, plaatst u de gegevens dichtbij waar uw gebruikers zich bevinden. Het kiezen van de vereiste regio's is afhankelijk van het wereld wijde bereik van uw toepassing en waar uw gebruikers zich bevinden. Cosmos DB worden de gegevens op transparante wijze gerepliceerd naar alle regio's die zijn gekoppeld aan uw Cosmos-account. Het biedt één systeem kopie van uw wereld wijd gedistribueerde Azure Cosmos-data base en containers die uw toepassing lokaal kan lezen en ernaar kan schrijven. 

Met Azure Cosmos DB kunt u op elk gewenst moment de regio's toevoegen of verwijderen die aan uw account zijn gekoppeld. Uw toepassing hoeft niet te worden onderbroken of opnieuw te worden geïnstalleerd als u een regio wilt toevoegen of verwijderen. Het blijft altijd Maxi maal beschikbaar als gevolg van de mogelijkheden voor multi-multihoming die de service systeem eigen heeft.

![Implementatie topologie met hoge Beschik baarheid](./media/distribute-data-globally/deployment-topology.png)

## <a name="key-benefits-of-global-distribution"></a>Belangrijkste voor delen van wereld wijde distributie

**Wereld wijd actieve apps bouwen.** Met het nieuwe multi-master replicatie protocol ondersteunt elke regio zowel schrijf bewerkingen als Lees bewerkingen. De functionaliteit voor meerdere masters biedt ook de volgende mogelijkheden:

- Onbeperkte elastische schrijf-en lees schaal baarheid. 
- 99,999% Beschik baarheid voor lezen en schrijven over de hele wereld.
- Gegarandeerde lees-en schrijf bewerkingen in minder dan 10 milliseconden in het 99e percentiel.

Met de Azure Cosmos DB multi-multihoming-Api's is uw toepassing op de hoogte van de dichtstbijzijnde regio en kan hij aanvragen verzenden naar deze regio. De dichtstbijzijnde regio wordt geïdentificeerd zonder configuratie wijzigingen. Wanneer u regio's toevoegt aan en verwijdert uit uw Azure Cosmos-account, hoeft uw toepassing niet opnieuw te worden geïmplementeerd of gepauzeerd. deze is altijd Maxi maal beschikbaar.

**Bouw zeer snel reagerende apps.** Uw toepassing kan bijna realtime Lees-en schrijf bewerkingen uitvoeren op alle regio's die u voor uw data base hebt gekozen. Azure Cosmos DB beheert intern de gegevens replicatie tussen regio's met consistentie niveau van het niveau dat u hebt geselecteerd.

**Bouw Maxi maal beschik bare apps.** Het uitvoeren van een data base in meerdere regio's over de hele wereld verhoogt de beschik baarheid van een Data Base. Als een regio niet beschikbaar is, worden door andere regio's automatisch toepassings aanvragen afgehandeld. Azure Cosmos DB biedt een lees-en schrijf Beschik baarheid van 99,999% voor data bases met meerdere regio's.

**Onderhoud van bedrijfs continuïteit tijdens regionale storingen.** Azure Cosmos DB ondersteunt [automatische failover](how-to-manage-database-account.md#automatic-failover) tijdens een regionale storing. Tijdens een regionale onderbreking blijft Azure Cosmos DB de latentie, Beschik baarheid, consistentie en de door Voer van de voor uitgang blijven behouden. Om ervoor te zorgen dat uw volledige toepassing Maxi maal beschikbaar is, biedt Cosmos DB hand matige failover-API voor het simuleren van een regionale storing. Met deze API kunt u normale bedrijfs continuïteits oefeningen uitvoeren.

**Lees-en schrijf doorvoer wereld wijd schalen.** U kunt elke regio zo instellen dat deze kan worden beschrijfd en op elastische schaal worden gelezen en overal ter wereld wordt geschreven. De door Voer die door uw toepassing wordt geconfigureerd op een Azure Cosmos-data base of een container, wordt gegarandeerd in alle regio's die zijn gekoppeld aan uw Azure Cosmos-account. De ingerichte door Voer wordt gegarandeerd door [financieel ondersteunde sla's](https://aka.ms/acdbsla).

**U kunt kiezen uit verschillende goed gedefinieerde consistentie modellen.** Het Azure Cosmos DB-replicatie protocol biedt vijf goed gedefinieerde, praktische en intuïtieve consistentie modellen. Elk model heeft een verhouding tussen consistentie en prestaties. Gebruik deze consistentie modellen voor het gemak om wereld wijd gedistribueerde toepassingen te bouwen.

## <a id="Next Steps"></a>Volgende stappen

Meer informatie over globale distributie vindt u in de volgende artikelen:

* [Wereld wijde distributie: onder de motorkap](global-dist-under-the-hood.md)
* [Multi-Master configureren in uw toepassingen](how-to-multi-master.md)
* [Clients configureren voor multihoming](how-to-manage-database-account.md#configure-multiple-write-regions)
* [Regio's toevoegen aan of verwijderen uit uw Azure Cosmos DB-account](how-to-manage-database-account.md#addremove-regions-from-your-database-account)
* [Een aangepast beleid voor conflict oplossing maken voor SQL-API-accounts](how-to-manage-conflicts.md#create-a-custom-conflict-resolution-policy)
* [Programmeer bare consistentie modellen in Cosmos DB](consistency-levels.md)
* [Kies de juiste consistentieniveau voor uw toepassing](consistency-levels-choosing.md)
* [Consistentieniveaus in Azure Cosmos DB-API 's](consistency-levels-across-apis.md)
* [Beschikbaarheid en prestaties van optimalisatie voor verschillende consistentieniveaus](consistency-levels-tradeoffs.md)

