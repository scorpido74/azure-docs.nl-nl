---
title: Werken met de ondersteuning voor wijzigingsfeed in Azure Cosmos DB
description: Gebruik Azure Cosmos DB-feedondersteuning om wijzigingen in documenten, op gebeurtenissen gebaseerde verwerking zoals triggers bij te houden en caches en analytische systemen up-to-date te houden
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/08/2020
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: e36e95aeb25c83ccd94f11e25bfe9f1b8f7bfdad
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984858"
---
# <a name="change-feed-in-azure-cosmos-db"></a>Wijzigingenfeed in Azure Cosmos DB

Ondersteuning voor de wijzigingenfeed in Azure Cosmos DB gebeurt door te luisteren naar een Azure Cosmos DB-container voor wijzigingen. Als output verschijnt er vervolgens een gesorteerde lijst met gewijzigde documenten op volgorde van wijziging. De wijzigingen zijn persistent, kunnen asynchroon en incrementeel worden verwerkt en de uitvoer kan naar een of meer consumenten worden gedistribueerd voor parallelle verwerking.

Meer informatie over [ontwerppatronen voor wijzigingen](change-feed-design-patterns.md).

## <a name="supported-apis-and-client-sdks"></a>Ondersteunde API's en client SDKs

Deze functie wordt momenteel ondersteund door de volgende Azure Cosmos DB API's en client-SDK's.

| **Clientstuurprogramma's** | **SQL-API** | **Azure Cosmos DB's API voor Cassandra** | **Azure Cosmos DB-API voor MongoDB** | **Gremlin-API**|**Tabel-API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | Ja | Ja | Ja | Ja | Nee |
|Java|Ja|Ja|Ja|Ja|Nee|
|Python|Ja|Ja|Ja|Ja|Nee|
|Knooppunt/JS|Ja|Ja|Ja|Ja|Nee|

## <a name="change-feed-and-different-operations"></a>Feed en verschillende bewerkingen wijzigen

Vandaag ziet u alle inserts en updates in de wijzigingsfeed. U de wijzigingsfeed niet filteren voor een specifiek type bewerking. Een mogelijk alternatief, is het toevoegen van een "zachte marker" op het item voor updates en filter op basis van dat bij het verwerken van items in de change feed.

Op dit moment wordt de feed niet bijlogboeken geopend. Net als in het vorige voorbeeld u een zachte markering toevoegen aan de items die worden verwijderd. U bijvoorbeeld een kenmerk toevoegen in het item met de naam 'verwijderd' en het instellen op 'true' en een TTL instellen op het item, zodat het automatisch kan worden verwijderd. U de wijzigingsfeed voor historische items lezen (de meest recente wijziging die overeenkomt met het item, de tussentijdse wijzigingen), bijvoorbeeld items die vijf jaar geleden zijn toegevoegd. U de wijzigingsfeed al teruglezen als de oorsprong van uw container, maar als een item wordt verwijderd, wordt het verwijderd uit de wijzigingsfeed.

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

* Toepassingen kunnen meerdere wijzigingsfeeds tegelijk op dezelfde container aanvragen. ChangeFeedOptions.StartTime kan worden gebruikt om een eerste startpunt te bieden. Bijvoorbeeld om het vervolgtoken te vinden dat overeenkomt met een bepaalde kloktijd. De vervolgtoken heeft, indien opgegeven, voorrang op de waarden StartTime en StartFromBeginning. De precisie van ChangeFeedOptions.StartTime is ~5 sec.

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Feed wijzigen in API's voor Cassandra en MongoDB

Feedfunctionaliteit wijzigen wordt opgedoken als wijzigingsstroom in De API van MongoDB en Query met predicaat in Cassandra API. Zie de Change streams in de Azure Cosmos [DB API voor MongoDB voor](mongodb-change-streams.md)meer informatie over de implementatiedetails voor MongoDB.

Native Apache Cassandra biedt change data capture (CDC), een mechanisme om specifieke tabellen voor archivering te markeren en het afwijzen van schrijft naar die tabellen zodra een configureerbare grootte op de schijf voor het CDC-logboek is bereikt. De wijzigingsfeedfunctie in Azure Cosmos DB API voor Cassandra verbetert de mogelijkheid om de wijzigingen met predicaat op te vragen via CQL. Zie [Feed wijzigen in de Azure Cosmos DB API voor Cassandra voor](cassandra-change-feed.md)meer informatie over de implementatiedetails.

## <a name="next-steps"></a>Volgende stappen

U nu verder gaan met meer informatie over de wijzigingsfeed in de volgende artikelen:

* [Opties voor het lezen van wijzigingsfeed](read-change-feed.md)
* [Wijzigingsfeed gebruiken met Azure-functies](change-feed-functions.md)
* [De feedprocessor van change gebruiken](change-feed-processor.md)
