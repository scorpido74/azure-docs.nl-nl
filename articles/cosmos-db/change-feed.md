---
title: Werken met de ondersteuning voor wijzigingsfeed in Azure Cosmos DB
description: Gebruik Azure Cosmos DB-feedondersteuning om wijzigingen in documenten, op gebeurtenissen gebaseerde verwerking zoals triggers bij te houden en caches en analytische systemen up-to-date te houden
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: 898dfe7a619981b93af98effa942fdecbeb42dde
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79368125"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Wijzigingenfeed in Azure Cosmos DB - overzicht

Ondersteuning voor de wijzigingenfeed in Azure Cosmos DB gebeurt door te luisteren naar een Azure Cosmos DB-container voor wijzigingen. Als output verschijnt er vervolgens een gesorteerde lijst met gewijzigde documenten op volgorde van wijziging. De wijzigingen zijn persistent, kunnen asynchroon en incrementeel worden verwerkt en de uitvoer kan naar een of meer consumenten worden gedistribueerd voor parallelle verwerking. 

Azure Cosmos DB is zeer geschikt voor IoT-, gaming-, retail- en operationele registratietoepassingen. Een gemeenschappelijk ontwerppatroon in deze toepassingen is het gebruik van wijzigingen in de gegevens om extra acties te activeren. Voorbeelden van aanvullende acties zijn:

* Een melding of een aanroep naar een API activeren wanneer een item wordt ingevoegd of bijgewerkt.
* Real-time stream processing voor IoT of real-time analytics processing op operationele data.
* Extra gegevensverplaatsing door te synchroniseren met een cache of een zoekmachine of een datawarehouse of door gegevens te archiveren naar koude opslag.

Met de wijzigingsfeed in Azure Cosmos DB u voor elk van deze patronen efficiënte en schaalbare oplossingen bouwen, zoals in de volgende afbeelding wordt weergegeven:

![Azure Cosmos DB-wijzigingsfeed gebruiken om realtime analyse- en gebeurtenisgestuurde computerscenario's van stroom te bieden](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>Ondersteunde API's en client SDKs

Deze functie wordt momenteel ondersteund door de volgende Azure Cosmos DB API's en client-SDK's.

| **Clientstuurprogramma's** | **Azure-CLI** | **SQL-API** | **Azure Cosmos DB's API voor Cassandra** | **Azure Cosmos DB-API voor MongoDB** | **Gremlin-API**|**Tabel-API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | N.v.t. | Ja | Ja | Ja | Ja | Nee |
|Java|N.v.t.|Ja|Ja|Ja|Ja|Nee|
|Python|N.v.t.|Ja|Ja|Ja|Ja|Nee|
|Knooppunt/JS|N.v.t.|Ja|Ja|Ja|Ja|Nee|

## <a name="change-feed-and-different-operations"></a>Feed en verschillende bewerkingen wijzigen

Vandaag ziet u alle bewerkingen in de wijzigingsfeed. De functionaliteit waar u de wijzigingsfeed beheren, voor specifieke bewerkingen zoals alleen updates en niet-inserts, is nog niet beschikbaar. U een 'zachte markering' toevoegen aan het item voor updates en filter op basis daarvan bij het verwerken van items in de wijzigingsfeed. Op dit moment wordt de feed niet bijlogboeken geopend. Net als in het vorige voorbeeld u bijvoorbeeld een zachte markering toevoegen aan de items die worden verwijderd, u een kenmerk toevoegen aan het item 'verwijderd' en instellen op 'waar' en een TTL instellen op het item, zodat het automatisch kan worden verwijderd. U de wijzigingsfeed voor historische items lezen (de meest recente wijziging die overeenkomt met het item, de tussentijdse wijzigingen), bijvoorbeeld items die vijf jaar geleden zijn toegevoegd. Als het item niet wordt verwijderd, u de wijzigingsfeed lezen tot aan de oorsprong van uw container.

### <a name="sort-order-of-items-in-change-feed"></a>Volgorde van artikelen sorteren in de feed change

Feeditems wijzigen, zijn in de volgorde van hun wijzigingstijd. Deze sorteervolgorde is gegarandeerd per logische partitiesleutel.

### <a name="consistency-level"></a>Consistentieniveau

Tijdens het consumeren van de wijzigingsfeed in een uiteindelijk consistentieniveau, kunnen er dubbele gebeurtenissen tussen de volgende feedleesbewerkingen voor wijzigingen plaatsvinden (de laatste gebeurtenis van een leesbewerking wordt weergegeven als de eerste van de volgende bewerking).

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Feed wijzigen in Azure Cosmos-accounts met meerdere regio's

Als een schrijfregio mislukt, werkt de wijzigingsfeed in een Azure Cosmos-account met meerdere regio's in de handmatige failover-bewerking en is deze aaneengesloten.

### <a name="change-feed-and-time-to-live-ttl"></a>Feed en Time to Live (TTL) wijzigen

Als een eigenschap TTL (Time to Live) is ingesteld op een item op -1, blijft de feed voor altijd veranderen. Als de gegevens niet worden verwijderd, blijft deze in de wijzigingsfeed.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Voer en _etag, _lsn of _ts wijzigen

De _etag-indeling is intern en u moet er geen afhankelijkheid van nemen, omdat deze op elk gewenst moment kan veranderen. _ts is een wijziging of een creatietijdstempel. U _ts gebruiken voor chronologische vergelijking. _lsn is een batch-id die alleen wordt toegevoegd voor wijzigingsfeed; het vertegenwoordigt de transactie-ID. Veel items kunnen dezelfde _lsn hebben. ETag op FeedResponse is anders dan de _etag die je op het item ziet. _etag is een interne id en wordt gebruikt voor gelijktijdigheidscontrole vertelt over de versie van het item, terwijl ETag wordt gebruikt voor het rangschikken van de feed.

## <a name="change-feed-use-cases-and-scenarios"></a>Voergebruiksaanvragen en -scenario's wijzigen

Change feed maakt een efficiënte verwerking van grote datasets met een groot aantal schrijfbewerkingen mogelijk. Change feed biedt ook een alternatief voor het opvragen van een volledige gegevensset om te bepalen wat er is veranderd.

### <a name="use-cases"></a>Gebruiksvoorbeelden

Met change feed u bijvoorbeeld de volgende taken efficiënt uitvoeren:

* Een cache bijwerken, een zoekindex bijwerken of een gegevensmagazijn bijwerken met gegevens die zijn opgeslagen in Azure Cosmos DB.

* Implementeer bijvoorbeeld een gegevenslagen op toepassingsniveau en archiveren, sla bijvoorbeeld 'hot data' op in Azure Cosmos DB en verouder 'koude gegevens' naar andere opslagsystemen, bijvoorbeeld [Azure Blob Storage.](../storage/common/storage-introduction.md)

* Voer nul downtime-migraties uit naar een ander Azure Cosmos-account of een andere Azure Cosmos-container met een andere logische partitiesleutel.

* Implementeer [de lambda-architectuur](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) met Azure Cosmos DB, waarbij Azure Cosmos DB zowel realtime, batch- als queryserverlagen ondersteunt, waardoor lambda-architectuur met lage TCO mogelijk is.

* Ontvang en bewaar gebeurtenisgegevens van apparaten, sensoren, infrastructuur en toepassingen en verwerk deze gebeurtenissen in realtime, bijvoorbeeld met [Spark.](../hdinsight/spark/apache-spark-overview.md)  In de volgende afbeelding ziet u hoe u de lambda-architectuur implementeren met Azure Cosmos DB via de change feed:

![Azure Cosmos DB-gebaseerde lambda-pijplijn voor inname en query](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Scenario's

Hieronder volgen enkele scenario's die u eenvoudig implementeren met de wijzigingsfeed:

* Binnen uw [serverloze](https://azure.microsoft.com/solutions/serverless/) web- of mobiele apps u gebeurtenissen bijhouden, zoals alle wijzigingen in het profiel, voorkeuren of de locatie van uw klant, en bepaalde acties activeren, bijvoorbeeld het verzenden van pushmeldingen naar hun apparaten met [Azure-functies.](change-feed-functions.md)

* Als u Azure Cosmos DB gebruikt om een game te bouwen, u bijvoorbeeld wijzigingsfeed gebruiken om realtime leaderboards te implementeren op basis van scores uit voltooide games.


## <a name="working-with-change-feed"></a>Werken met change feed

U met de wijzigingsfeed werken met de volgende opties:

* [Wijzigingsfeed gebruiken met Azure-functies](change-feed-functions.md)
* [Change feed gebruiken met change feed processor](change-feed-processor.md) 

Change feed is beschikbaar voor elke logische partitiesleutel in de container en kan worden verdeeld over een of meer consumenten voor parallelle verwerking, zoals in de onderstaande afbeelding wordt weergegeven.

![Gedistribueerde verwerking van Azure Cosmos DB-wijzigingsfeed](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Kenmerken van de wijzigingsfeed

* De feed wijzigen is standaard ingeschakeld voor alle Azure Cosmos-accounts.

* U uw [ingerichte doorvoer](request-units.md) gebruiken om uit de wijzigingsfeed te lezen, net als elke andere Azure Cosmos DB-bewerking, in een van de regio's die zijn gekoppeld aan uw Azure Cosmos-database.

* De wijzigingsfeed bevat inserts en updatebewerkingen die zijn uitgevoerd op items in de container. U deletes vastleggen door een 'soft-delete'-vlag in te stellen in uw items (bijvoorbeeld documenten) in plaats van verwijderingen. U ook een eindige vervaldatum instellen voor uw artikelen met de [TTL-mogelijkheid.](time-to-live.md) Bijvoorbeeld 24 uur en gebruik de waarde van die eigenschap om deletes vast te leggen. Met deze oplossing moet u de wijzigingen binnen een korter tijdsinterval verwerken dan de TTL-vervaldatum. 

* Elke wijziging in een item wordt precies één keer weergegeven in de wijzigingsfeed en de clients moeten de controlepuntlogica beheren. Als u de complexiteit van het beheer van controlepunten wilt vermijden, biedt de wijzigingsfeedprocessor automatische controlepunten en "ten minste één keer" semantiek. Zie [het gebruik van change feed met change feed processor](change-feed-processor.md).

* Alleen de meest recente wijziging voor een bepaald item is opgenomen in het wijzigingslogboek. Tussentijdse wijzigingen zijn mogelijk niet beschikbaar.

* De wijzigingsfeed wordt gesorteerd op de volgorde van wijziging binnen elke logische partitiesleutelwaarde. Er is geen gegarandeerde volgorde in de hoofdwaarden van de partitie.

* Wijzigingen kunnen worden gesynchroniseerd vanaf elk point-in-time, dat wil zeggen dat er geen vaste bewaartermijn voor gegevens is waarvoor wijzigingen beschikbaar zijn.

* Wijzigingen zijn parallel beschikbaar voor alle logische partitiesleutels van een Azure Cosmos-container. Met deze mogelijkheid kunnen wijzigingen van grote containers parallel door meerdere consumenten worden verwerkt.

* Toepassingen kunnen meerdere wijzigingsfeeds tegelijk op dezelfde container aanvragen. ChangeFeedOptions.StartTime kan worden gebruikt om een eerste startpunt te bieden. Bijvoorbeeld om het vervolgtoken te vinden dat overeenkomt met een bepaalde kloktijd. De waarden ContinuationToken wint, indien opgegeven, de waarden StartTime en StartFromBeginning. De precisie van ChangeFeedOptions.StartTime is ~5 sec. 

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Feed wijzigen in API's voor Cassandra en MongoDB

Feedfunctionaliteit wijzigen wordt opgedoken als wijzigingsstroom in De API van MongoDB en Query met predicaat in Cassandra API. Zie de Change streams in de Azure Cosmos [DB API voor MongoDB voor](mongodb-change-streams.md)meer informatie over de implementatiedetails voor MongoDB.

Native Apache Cassandra biedt change data capture (CDC), een mechanisme om specifieke tabellen voor archivering te markeren en het afwijzen van schrijft naar die tabellen zodra een configureerbare grootte op de schijf voor het CDC-logboek is bereikt. De wijzigingsfeedfunctie in Azure Cosmos DB API voor Cassandra verbetert de mogelijkheid om de wijzigingen met predicaat op te vragen via CQL. Zie [Feed wijzigen in de Azure Cosmos DB API voor Cassandra voor](cassandra-change-feed.md)meer informatie over de implementatiedetails.

## <a name="next-steps"></a>Volgende stappen

U nu verder gaan met meer informatie over de wijzigingsfeed in de volgende artikelen:

* [Opties voor het lezen van wijzigingsfeed](read-change-feed.md)
* [Wijzigingsfeed gebruiken met Azure-functies](change-feed-functions.md)
* [De feedprocessor van change gebruiken](change-feed-processor.md)
