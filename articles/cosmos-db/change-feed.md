---
title: Werken met de ondersteuning voor wijzigings invoer in Azure Cosmos DB
description: Gebruik Azure Cosmos DB Change feed-ondersteuning om wijzigingen in documenten bij te houden en op gebeurtenis gebaseerde verwerking uit te voeren zoals triggers en caches en analyse systemen up-to-date te houden.
author: TheovanKraay
ms.author: thvankra
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/25/2019
ms.reviewer: sngun
ms.custom: seodec18
ms.openlocfilehash: eef950c4e8c4a880d331022ed60477bebce65b5d
ms.sourcegitcommit: 48b7a50fc2d19c7382916cb2f591507b1c784ee5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2019
ms.locfileid: "74689090"
---
# <a name="change-feed-in-azure-cosmos-db---overview"></a>Feed wijzigen in Azure Cosmos DB-overzicht

De ondersteuning voor feeds in Azure Cosmos DB kan worden gewijzigd door naar een Azure Cosmos-container te Luis teren. Als output verschijnt er vervolgens een gesorteerde lijst met gewijzigde documenten op volgorde van wijziging. De wijzigingen zijn persistent, kunnen asynchroon en incrementeel worden verwerkt, en de uitvoer kan naar een of meer consumenten worden gedistribueerd voor parallelle verwerking. 

Azure Cosmos DB is goed geschikt voor IoT-, gaming-, retail-en operationele logboek toepassingen. Een gemeen schappelijk ontwerp patroon in deze toepassingen is het gebruik van wijzigingen in de gegevens om aanvullende acties te activeren. Voor beelden van aanvullende acties zijn:

* Het activeren van een melding of een aanroep van een API wanneer een item wordt ingevoegd of bijgewerkt.
* Realtime-verwerking van streams voor IoT of realtime analyse verwerking op operationele gegevens.
* Aanvullende gegevens verplaatsing door ofwel te synchroniseren met een cache of een zoek machine of een Data Warehouse of het archiveren van gegevens naar koude opslag.

Met de wijzigings feed in Azure Cosmos DB kunt u efficiënte en schaal bare oplossingen voor elk van deze patronen bouwen, zoals wordt weer gegeven in de volgende afbeelding:

![Azure Cosmos DB Change feed gebruiken om realtime analyse en op gebeurtenissen gebaseerde computer scenario's uit te voeren](./media/change-feed/changefeedoverview.png)

## <a name="supported-apis-and-client-sdks"></a>Ondersteunde Api's en client-Sdk's

Deze functie wordt momenteel ondersteund door de volgende Azure Cosmos DB-Api's en client-Sdk's.

| **Client Stuur Programma's** | **Azure CLI** | **SQL-API** | **API van Azure Cosmos DB voor Cassandra** | **API van Azure Cosmos DB voor MongoDB** | **Gremlin-API**|**Tabel-API** |
| --- | --- | --- | --- | --- | --- | --- |
| .NET | N.V.T. | Ja | Ja | Ja | Ja | Nee |
|Java|N.V.T.|Ja|Ja|Ja|Ja|Nee|
|Python|N.V.T.|Ja|Ja|Ja|Ja|Nee|
|Node/JS|N.V.T.|Ja|Ja|Ja|Ja|Nee|

## <a name="change-feed-and-different-operations"></a>Feed en verschillende bewerkingen wijzigen

Vandaag ziet u alle bewerkingen in de feed voor wijzigingen. De functionaliteit waarmee u de wijzigings feed kunt beheren voor bepaalde bewerkingen, zoals alleen updates en niet wordt ingevoegd, is nog niet beschikbaar. U kunt een ' zachte markering ' toevoegen aan het item voor updates en filteren op basis van de gegevens die worden verwerkt bij het verwerken van items in de wijzigings feed. Wijzigingen in de feed worden niet in het logboek geregistreerd. Net als in het vorige voor beeld kunt u een zachte markering toevoegen voor de items die worden verwijderd. u kunt bijvoorbeeld een kenmerk toevoegen aan het item ' verwijderd ' en instellen op ' True ' en een TTL instellen voor het item, zodat het automatisch kan worden verwijderd. U kunt de wijzigings feed voor historische items lezen (de meest recente wijziging die overeenkomt met het item, het bevat geen tussenliggende wijzigingen), bijvoorbeeld items die vijf jaar geleden zijn toegevoegd. Als het item niet is verwijderd, kunt u de wijzigings feed lezen tot de oorsprong van de container.

### <a name="sort-order-of-items-in-change-feed"></a>Sorteer volgorde van items in een wijzigings feed

De wijzigings items worden in de volg orde van de wijzigings tijd van kracht. Deze sorteer volgorde wordt gegarandeerd per logische partitie sleutel.

### <a name="change-feed-in-multi-region-azure-cosmos-accounts"></a>Feed wijzigen in azure Cosmos-accounts met meerdere regio's

Als er in een Azure Cosmos-account met meerdere regio's een failover wordt uitgevoerd, werkt de wijziging in de bewerking voor hand matige failover en wordt deze aaneengesloten.

### <a name="change-feed-and-time-to-live-ttl"></a>Feed en Time to Live wijzigen (TTL)

Als een TTL-eigenschap (Time to Live) is ingesteld voor een item op-1, blijft de wijzigings feed altijd behouden. Als de gegevens niet worden verwijderd, blijft deze in de wijzigings feed.  

### <a name="change-feed-and-_etag-_lsn-or-_ts"></a>Feed en _etag, _lsn of _ts wijzigen

De indeling van de _etag is intern en u moet er geen afhankelijkheid van maken, omdat deze op elk gewenst moment kan worden gewijzigd. _ts is een wijziging of een tijds tempel voor het maken. U kunt _ts gebruiken voor chronologische vergelijking. _lsn is een batch-ID die alleen is toegevoegd voor wijzigings invoer. het vertegenwoordigt de trans actie-ID. Veel items hebben mogelijk hetzelfde _lsn. ETag op FeedResponse wijkt af van de _etag die u op het item ziet. _etag is een interne id en wordt gebruikt voor gelijktijdigheids beheer en geeft de versie van het item aan, terwijl ETag wordt gebruikt voor het sequentiëren van de feed.

## <a name="change-feed-use-cases-and-scenarios"></a>Gebruiks voorbeelden en scenario's voor feeds wijzigen

Met wijzigings feed kunt u grote gegevens sets met een groot aantal schrijf bewerkingen efficiënt verwerken. Change feed biedt ook een alternatief voor het uitvoeren van een query op een hele gegevensset om te bepalen wat er is gewijzigd.

### <a name="use-cases"></a>Use cases

Met een wijzigings feed kunt u bijvoorbeeld de volgende taken efficiënt uitvoeren:

* Een cache bijwerken, een zoek index bijwerken of een Data Warehouse bijwerken met gegevens die zijn opgeslagen in Azure Cosmos DB.

* Implementeer een gegevenslaagings-en archiverings functie op toepassings niveau, bijvoorbeeld ' warme gegevens ' in Azure Cosmos DB en stel ' koude gegevens ' in voor andere opslag systemen, bijvoorbeeld [Azure Blob Storage](../storage/common/storage-introduction.md).

* U kunt niet meer dan nul migraties uitvoeren naar een ander Azure Cosmos-account of een andere Azure Cosmos-container met een andere logische partitie sleutel.

* Implementeer een [Lambda-architectuur](https://blogs.technet.microsoft.com/msuspartner/2016/01/27/azure-partner-community-big-data-advanced-analytics-and-lambda-architecture/) met behulp van Azure Cosmos DB, waarbij Azure Cosmos DB zowel realtime, batch als query's voor het leveren van lagen ondersteunt, waardoor Lambda-architectuur met lage totale eigendoms kosten kan worden ingeschakeld.

* Ontvang en sla gebeurtenis gegevens op van apparaten, Sens oren, infra structuur en toepassingen, en verwerk deze gebeurtenissen in realtime, bijvoorbeeld met behulp van [Spark](../hdinsight/spark/apache-spark-overview.md).  De volgende afbeelding laat zien hoe u een lambda-architectuur kunt implementeren met behulp van Azure Cosmos DB via een wijzigings feed:

![Lambda-pijp lijn op basis van Azure Cosmos DB voor opname en query](./media/change-feed/lambda.png)

### <a name="scenarios"></a>Scenario's

Hier volgen enkele van de scenario's die u eenvoudig kunt implementeren met een wijzigings feed:

* Binnen uw [serverloze](https://azure.microsoft.com/solutions/serverless/) web-of mobiele apps kunt u gebeurtenissen volgen, zoals alle wijzigingen in het profiel, de voor keuren of hun locatie van uw klant, en worden bepaalde acties geactiveerd, bijvoorbeeld om Push meldingen naar hun apparaten te verzenden met behulp van [Azure functions](change-feed-functions.md).

* Als u Azure Cosmos DB gebruikt voor het bouwen van een spelletje, kunt u bijvoorbeeld Change feed gebruiken om realtime klassementen te implementeren op basis van scores van voltooide games.


## <a name="working-with-change-feed"></a>Werken met wijzigings feed

U kunt met behulp van de volgende opties met een wijzigings feed werken:

* [Change feed gebruiken met Azure Functions](change-feed-functions.md)
* [Change feed gebruiken met Change feed-processor](change-feed-processor.md) 

De wijzigings feed is beschikbaar voor elke logische partitie sleutel in de container en kan worden gedistribueerd voor een of meer consumenten voor parallelle verwerking, zoals wordt weer gegeven in de onderstaande afbeelding.

![Gedistribueerde verwerking van Azure Cosmos DB Change feed](./media/change-feed/changefeedvisual.png)

## <a name="features-of-change-feed"></a>Functies van wijzigings feed

* De wijzigings feed is standaard ingeschakeld voor alle Azure Cosmos-accounts.

* U kunt uw [ingerichte door Voer](request-units.md) gebruiken voor het lezen van de wijzigings feed, net als bij andere Azure Cosmos DB bewerkingen, in een van de regio's die zijn gekoppeld aan uw Azure Cosmos-data base.

* De wijzigings feed bevat invoeg-en update-bewerkingen die zijn aangebracht aan items in de container. U kunt verwijderingen vastleggen door een vlag "zacht verwijderen" in te stellen in uw items (bijvoorbeeld documenten) in plaats van verwijderen. U kunt ook een eindige verval periode instellen voor uw items met de [TTL-mogelijkheid](time-to-live.md). Bijvoorbeeld 24 uur en gebruik de waarde van die eigenschap om verwijderingen vast te leggen. Met deze oplossing moet u de wijzigingen verwerken binnen een korter tijds interval dan de TTL-verval periode. 

* Elke wijziging in een item wordt slechts één keer weer gegeven in de wijzigings feed en de clients moeten de logica voor het controle punt beheren. Als u de complexiteit van het beheer van controle punten wilt voor komen, biedt de Change feed-processor automatische controle punten en ' ten minste eenmaal ' semantiek. Zie [Change feed gebruiken met Change feed processor](change-feed-processor.md).

* Alleen de meest recente wijziging voor een bepaald item is opgenomen in het wijzigings logboek. Tussenliggende wijzigingen zijn mogelijk niet beschikbaar.

* De wijzigings feed wordt gesorteerd op volg orde van wijziging binnen elke logische partitie sleutel waarde. Er is geen gegarandeerde volg orde in de partitie sleutel waarden.

* Wijzigingen kunnen worden gesynchroniseerd vanuit elk tijdstip, dat wil zeggen dat er geen vaste gegevens Bewaar periode is waarvoor wijzigingen beschikbaar zijn.

* Wijzigingen zijn parallel beschikbaar voor alle logische partitie sleutels van een Azure Cosmos-container. Met deze mogelijkheid kunnen wijzigingen van grote containers parallel worden verwerkt door meerdere gebruikers.

* Toepassingen kunnen meerdere wijzigings feeds op dezelfde container tegelijk aanvragen. ChangeFeedOptions. StartTime kan worden gebruikt om een eerste begin punt op te geven. Bijvoorbeeld om het vervolg token te vinden dat overeenkomt met een bepaalde klok tijd. De ContinuationToken, indien opgegeven, WINS via de waarden StartTime en StartFromBeginning. De precisie van ChangeFeedOptions. StartTime is ~ 5 seconden. 

## <a name="change-feed-in-apis-for-cassandra-and-mongodb"></a>Feed wijzigen in Api's voor Cassandra en MongoDB

De functionaliteit voor het wijzigen van de feed wordt geoppereerd als een wijzigings stroom in de MongoDB-API en query met een predikaat in Cassandra-API. Zie voor meer informatie over de implementatie Details voor MongoDB API de [wijzigings stromen in de Azure Cosmos DB-API voor MongoDb](mongodb-change-streams.md).

Systeem eigen Apache Cassandra biedt change data capture (CDC), een mechanisme voor het markeren van specifieke tabellen voor archivering en het afwijzen van schrijf bewerkingen naar die tabellen wanneer een Configureer bare grootte-op-schijf voor het CDC-logboek wordt bereikt. De functie voor het wijzigen van de feed in Azure Cosmos DB-API voor Cassandra verbetert de mogelijkheid om query's uit te voeren op de wijzigingen met een predikaat via CQL. Zie [Change feed in de Azure Cosmos DB-API voor Cassandra voor](cassandra-change-feed.md)meer informatie over de implementatie details.

## <a name="next-steps"></a>Volgende stappen

U kunt nu door gaan met meer informatie over wijzigings invoer in de volgende artikelen:

* [Opties voor het lezen van een wijzigings feed](read-change-feed.md)
* [Change feed gebruiken met Azure Functions](change-feed-functions.md)
* [Een Change feed-processor gebruiken](change-feed-processor.md)
