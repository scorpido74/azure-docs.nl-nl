---
title: Werken met de change feed support in Azure Cosmos DB
description: Gebruik Azure Cosmos DB change feed support bijhouden van wijzigingen in documenten en uitvoeren op basis van gebeurtenissen verwerken, zoals triggers en caches en analysefuncties systemen up-to-date te houden.
author: rimman
ms.author: rimman
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 07/23/2019
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 20ca823f60ced4260c2e61ca15ae04e158b7d952
ms.sourcegitcommit: e42c778d38fd623f2ff8850bb6b1718cdb37309f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69615672"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Feed wijzigen in Azure Cosmos DB-overzicht

De ondersteuning voor feeds in Azure Cosmos DB kan worden gewijzigd door naar een Azure Cosmos-container te Luis teren. Het voert vervolgens de gesorteerde lijst met documenten die zijn gewijzigd in de volgorde waarin ze zijn gewijzigd. De wijzigingen zijn persistent, kunnen asynchroon en incrementeel worden verwerkt, en de uitvoer kan naar een of meer consumenten worden gedistribueerd voor parallelle verwerking. 

Azure Cosmos DB is zeer geschikt voor IoT, games, detailhandel, en operationele logboekregistratie toepassingen. Een algemene ontwerppatroon in deze toepassingen is het gebruik van wijzigingen in de gegevens kunt u aanvullende acties activeren. Voorbeelden van aanvullende acties zijn:

* Een melding of een aanroep naar een API wordt geactiveerd wanneer een item wordt ingevoegd of bijgewerkt.
* Realtime-verwerking voor IoT of analyses in realtime verwerking van operationele gegevens.
* De verplaatsing van de aanvullende gegevens te synchroniseren met een cache of een zoekmachine of een datawarehouse, of archiveren van gegevens naar koude opslag.

De in Azure Cosmos DB-wijzigingenfeed kunt u efficiënt en schaalbare oplossingen bouwen voor elk van deze patronen, zoals wordt weergegeven in de volgende afbeelding:

![Met behulp van Azure Cosmos DB change feed power realtime analyses en gebeurtenissen gebaseerde computing-scenario 's](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>Ondersteunde API's en client-SDK 's

Deze functie wordt momenteel ondersteund door de volgende Azure Cosmos DB-API's en client-SDK's.

| **-Clientstuurprogramma 's** | **Azure-CLI** | **SQL-API** | **Cassandra-API** | **API van Azure Cosmos DB voor MongoDB** | **Gremlin-API**|**Tabel-API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | N.v.t. | Ja | Nee | Nee | Ja | Nee |
|Java|N.v.t.|Ja|Nee|Nee|Ja|Nee|
|Python|N.v.t.|Ja|Nee|Nee|Ja|Nee|
|Knooppunt/JS|N.v.t.|Ja|Nee|Nee|Ja|Nee|

## <a name="change-feed-and-different-operations"></a>Feed wijzigen en verschillende bewerkingen

Vandaag, ziet u alle bewerkingen in de feed wijzigen. De functionaliteit van waar u kunt bepalen wijzigen feed voor bepaalde bewerkingen, zoals alleen updates en niet voegt nog niet beschikbaar is. U kunt een 'soft 'markering toevoegen op het item voor updates en filteren op basis van die bij het verwerken van items in de feed wijzigen. Op dit moment Meld niet worden verwijderd u wijzigingenfeed. Net als bij het vorige voorbeeld, kunt u een voorlopig markering toevoegen op de items die worden verwijderd, bijvoorbeeld, u kunt een kenmerk toevoegen in het item met de naam 'verwijderd' en stel deze in op 'true' en een TTL-waarde ingesteld op het item, zodat deze kan automatisch worden verwijderd. U kunt lezen om dat de wijziging feed voor historische items, bijvoorbeeld items die vijf jaar geleden zijn toegevoegd. Als het item is niet verwijderd. u kunt de wijziging lezen feed zo de oorsprong van de container.

### <a name="sort-order-of-items-in-change-feed"></a>Sorteervolgorde van de items in de feed wijzigen

Wijziging feeditems komen in de volgorde van hun tijd van wijziging. Deze sorteervolgorde wordt gegarandeerd per sleutel van de logische partitie.

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Feed in meerdere regio's Azure Cosmos-accounts wijzigen

In een Azure Cosmos-account van meerdere regio's als een regio voor schrijven failover optreedt, wijzigingenfeed werkt voor de handmatige failover-bewerking en dient aaneengesloten.

### <a name="change-feed-and-time-to-live-ttl"></a>Feed wijzigen en Time to Live (TTL)

Als een TTL (Time to Live)-eigenschap is ingesteld op een item op-1, wordt altijd wijzigingenfeed behouden. Als de gegevens worden niet verwijderd, blijft deze in de feed wijzigen.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Feed wijzigen en _etag, _lsn of _ts

De indeling _etag is intern en nemen niet afhankelijkheid, omdat deze op elk gewenst moment kunt wijzigen. _ts is een wijziging of een tijdstempel van maken. U kunt _ts gebruiken voor chronologische vergelijking. _lsn is een batch-ID die alleen is toegevoegd voor wijzigings invoer. het vertegenwoordigt de trans actie-ID. Aantal items mogelijk dezelfde _lsn. ETag op FeedResponse wijkt af van de _etag u op het item ziet. _etag is een interne id en wordt gebruikt voor gelijktijdigheid besturingselement vertelt over de versie van het item, terwijl ETag wordt gebruikt voor het sequentiëren van de feed.

## <a name="change-feed-use-cases-and-scenarios"></a>Feed use cases en scenario's wijzigen

Change feed maakt efficiënte verwerking van grote gegevenssets met een groot aantal schrijfbewerkingen. Wijzigingenfeed biedt ook een alternatief voor het uitvoeren van query's een volledige gegevensset om te bepalen wat er is gewijzigd.

### <a name="use-cases"></a>Gebruiksvoorbeelden

Bijvoorbeeld, met wijzigingenfeed kunt u de volgende taken uitvoeren efficiënt:

* Bijwerken van een cache, een search-index bijwerken of bijwerken van een datawarehouse met gegevens die zijn opgeslagen in Azure Cosmos DB.

* Implementeren van een gegevensbron op toepassingsniveau cloudlagen en archiveren van gegevens, bijvoorbeeld "gegevens" opslaan in Azure Cosmos DB en leeftijd van 'koude gegevens' naar andere opslagsystemen, bijvoorbeeld [Azure Blob Storage](../storage/common/storage-introduction.md).

* Nul uitval migraties naar een ander Azure-Cosmos-account of een andere Azure-Cosmos-container uitvoeren met een andere logische partitie-sleutel.

* Implementeer [lambda-architectuur](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) met Azure Cosmos DB, waarbij Azure Cosmos DB biedt ondersteuning voor zowel realtime, batch en query voor de lagen, waardoor u van lambda-architectuur met lage totale Eigendomskosten.

* Ontvangen en gebeurtenisgegevens van apparaten, sensoren, infrastructuur en toepassingen opslaan en verwerken van deze gebeurtenissen in realtime, bijvoorbeeld met behulp van [Spark](../hdinsight/spark/apache-spark-overview.md).  De volgende afbeelding ziet u hoe u met behulp van Azure Cosmos DB via wijzigingenfeed lambda-architectuur kunt implementeren:

![Azure Cosmos DB op basis van lambda-pijplijn voor gegevensopname en query's uitvoeren](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Scenario's

Hier volgen enkele van de scenario's die u eenvoudig met een wijzigingsfeed implementeren kunt:

* Binnen uw [serverloze](https://azure.microsoft.com/solutions/serverless/) web- of mobiele apps, kunt u gebeurtenissen, zoals alle wijzigingen bijhouden van uw klant profiel, voorkeuren of de locatie en activeren van bepaalde acties, bijvoorbeeld het verzenden van pushmeldingen te verzenden naar hun apparaten met behulp van [Azure Functions](change-feed-functions.md).

* Als u gebruikmaakt van Azure Cosmos DB een game ontwikkelt, kunt u, bijvoorbeeld, gebruik wijzigingenfeed voor het implementeren van realtime scoreborden op basis van scores van voltooide games.


## <a name="working-with-change-feed"></a>Werken met change feed

U kunt werken met een wijzigingsfeed met de volgende opties:

* [Met behulp van de change feed met Azure Functions](change-feed-functions.md)
* [Change feed gebruiken met Change feed-processor](change-feed-processor.md) 

Wijzigingenfeed is beschikbaar voor elke sleutel logische partitie in de container en deze kan worden verdeeld over een of meer consumenten voor parallelle verwerking zoals wordt weergegeven in de onderstaande afbeelding.

![Gedistribueerde verwerking van Azure Cosmos DB-wijzigingenfeed](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Functies van de wijzigingenfeed

* Wijzigingenfeed is standaard ingeschakeld voor alle Azure-Cosmos-accounts.

* U kunt uw [ingerichte doorvoer](request-units.md) om te lezen uit de feed wijzigen, net als elke andere Azure Cosmos DB-bewerking in een van de regio's die zijn gekoppeld aan uw Azure Cosmos-database.

* De wijzigingenfeed bevat INSERT en update-bewerkingen die zijn aangebracht in de items in de container. U kunt hiermee vastleggen door een vlag 'voorlopig verwijderen' in uw items (bijvoorbeeld documenten) in plaats van verwijdert. U kunt ook een beperkte verloopperiode voor uw artikelen met instellen de [TTL mogelijkheid](time-to-live.md). Bijvoorbeeld: 24 uur en gebruik de waarde van deze eigenschap om vast te leggen worden verwijderd. Met deze oplossing hebt u voor het verwerken van de wijzigingen binnen een kortere periode dan de verloopperiode TTL-waarde. 

* Elke wijziging aan een item wordt exact één keer weergegeven in de feed wijzigen en de clients moeten de logica voor het plaatsen van controlepunten beheren. Als u de complexiteit van het beheer van controle punten wilt voor komen, biedt de Change feed-processor automatische controle punten en ' ten minste eenmaal ' semantiek. Zie [Change feed gebruiken met Change feed processor](change-feed-processor.md).

* Alleen de meest recente wijziging voor een bepaald item is opgenomen in het logboekbestand. Tussentijdse wijzigingen zijn mogelijk niet beschikbaar.

* De wijzigingenfeed is gesorteerd door de volgorde van wijzigingen in de waarde voor elke logische partitie-sleutel. Er is geen gegarandeerde volgorde tussen de partitiesleutelwaarden die zijn.

* Wijzigingen kunnen worden gesynchroniseerd vanuit een point-in-time, die er is geen vaste Gegevensretentieperiode waarvoor wijzigingen beschikbaar zijn.

* Wijzigingen zijn beschikbaar in parallelle voor alle sleutels van de logische partitie van een Azure Cosmos-container. Op deze manier kunt wijzigingen in grote containers moeten parallel worden verwerkt door meerdere gebruikers.

* Toepassingen kunnen meerdere wijzigings feeds op dezelfde container tegelijk aanvragen. ChangeFeedOptions.StartTime kan worden gebruikt voor een initieel beginpunt. Als u bijvoorbeeld het vervolgtoken dat overeenkomt met een bepaalde clock-tijd vinden. De ContinuationToken, wins indien opgegeven, boven de StartTime en StartFromBeginning waarden. De precisie van de ChangeFeedOptions.StartTime is ongeveer 5 seconden. 

## <a name="next-steps"></a>Volgende stappen

U kunt nu doorgaan naar meer informatie over de wijzigingenfeed in de volgende artikelen:

* [Opties voor het lezen van wijzigingenfeed](read-change-feed.md)
* [Met behulp van de change feed met Azure Functions](change-feed-functions.md)
* [Een Change feed-processor gebruiken](change-feed-processor.md)
