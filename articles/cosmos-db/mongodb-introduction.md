---
title: Inleiding tot de API van Azure Cosmos DB voor MongoDB
description: Meer informatie over hoe u Azure Cosmos DB kunt gebruiken voor het opslaan van enorme hoeveelheden gegevens met de API van Azure Cosmos DB voor MongoDB en er query's op kunt uitvoeren.
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.topic: overview
ms.date: 10/1/2019
author: sivethe
ms.author: sivethe
ms.openlocfilehash: 670020d276b9d4a868f24eb4a3f522581060adca
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/26/2020
ms.locfileid: "72754981"
---
# <a name="azure-cosmos-dbs-api-for-mongodb"></a>Azure Cosmos DB-API voor MongoDB

[Azure Cosmos DB](introduction.md) is de wereldwijd gedistribueerde databaseservice met meerdere modellen van Microsoft voor essentiële toepassingen. Azure Cosmos DB biedt [gebruiksklare wereldwijde distributie](distribute-data-globally.md), [elastisch schalen van doorvoer en opslag](partition-data.md), latentie van slechts enkele milliseconden op het 99e percentiel, en een gegarandeerd hoge beschikbaarheid, ondersteund met [toonaangevende SLA’s](https://azure.microsoft.com/support/legal/sla/cosmos-db/). Met Azure Cosmos DB worden [gegevens automatisch geïndexeerd](https://www.vldb.org/pvldb/vol8/p1668-shukla.pdf), zonder dat u te maken krijgt met schema- en indexbeheer. Het beschikt over meerdere modellen en ondersteunt modellen voor document-, sleutelwaarde-, grafiek- en kolomgegevens. Standaard kunt u via de SQL-API werken met Cosmos DB. Daarnaast worden met de Cosmos DB-service wire-protocollen geïmplementeerd voor veelgebruikte NoSQL-API's zoals Cassandra, MongoDB, Gremlin en Azure Table Storage. Hiermee kunt u uw vertrouwde NoSQL-clientstuurprogramma's en hulpprogramma's gebruiken om te werken met uw Cosmos-database.

## <a name="wire-protocol-compatibility"></a>Compatibiliteit met wire-protocollen

Met Azure Cosmos DB-service worden wire-protocollen geïmplementeerd van veelgebruikte NoSQL-databases, zoals Cassandra, MongoDB, Gremlin en Azure Table Storage. Door direct en effectief binnen Cosmos DB een systeemeigen implementatie van de wire-protocollen uit te voeren, kunnen de bestaande client-SDK's, -stuurprogramma's en -hulpprogramma's van de NoSQL-databases transparant met Cosmos DB werken. Cosmos DB maakt geen gebruik van broncode van de databases om wire-compatibele API's voor een van de NoSQL-databases te leveren.

Nieuwe accounts die zijn gemaakt met de API van Azure Cosmos DB voor MongoDB, zijn standaard compatibel met versie 3.6 van het MongoDB-draadprotocol. Elke MongoDB-clientdriver die deze protocolversie begrijpt, moet native verbinding kunnen maken met Cosmos DB.

![Azure Cosmos DB-API voor MongoDB](./media/mongodb-introduction/cosmosdb-mongodb.png)

## <a name="key-benefits"></a>Belangrijkste voordelen

De belangrijkste voordelen van Cosmos DB als volledig beheerde, wereldwijd gedistribueerde Database as a Service worden [hier](introduction.md) beschreven. Door een systeemeigen implementatie van wire-protocollen van populaire NoSQL-API's uit te voeren, biedt Cosmos DB bovendien de volgende voordelen:

* U kunt uw toepassing eenvoudig migreren naar Cosmos DB met behoud van belangrijke onderdelen van uw toepassingslogica.
* Behoud de overdraagbaarheid van uw toepassing zonder dat u de vrijheid verliest om van cloudleverancier te veranderen.
* Voor de meestgebruikte NoSQL-API's krijgt u toonaangevende SLA's met een solide financiële basis, mogelijk gemaakt door Cosmos DB.
* U kunt de ingerichte doorvoer en opslag voor uw Cosmos-databases elastisch schalen op basis van uw behoeften, en u betaalt alleen voor de doorvoer en opslag die u nodig hebt. Dit leidt tot aanzienlijke kostenbesparingen.
* Kant-en-klare wereldwijde distributie met replicatie voor meerdere masters.

## <a name="cosmos-dbs-api-for-mongodb"></a>API van Cosmos DB voor MongoDB

Volg de quickstarts om een Azure Cosmos-account te maken en migreer uw bestaande MongoDB-toepassing om Azure Cosmos DB te gebruiken of een nieuw account te bouwen:

* [Een bestaande MongoDB Node.js-web-app migreren](create-mongodb-nodejs.md).
* [Met de API van Azure Cosmos DB voor MongoDB en de .NET-SDK een web-app maken](create-mongodb-dotnet.md).
* [Met de API van Azure Cosmos DB voor MongoDB en de Java-SDK een console-app maken](create-mongodb-java.md).

## <a name="next-steps"></a>Volgende stappen

Hier volgen enkele aanwijzingen om aan de slag te gaan:

* Volg de zelfstudie [Een MongoDB-toepassing verbinden met Azure Cosmos DB](connect-mongodb-account.md) voor informatie over het ophalen van verbindingsreeksinformatie voor uw account.
* Volg de zelfstudie [Studio 3T gebruiken met Azure Cosmos DB](mongodb-mongochef.md) om te leren hoe u een verbinding maakt tussen een Cosmos-database en de MongoDB-app in Studio 3T.
* Volg de zelfstudie [MongoDB-gegevens importeren in Azure Cosmos DB](mongodb-migrate.md) om uw gegevens te importeren in een Cosmos-database.
* Verbinding maken met een Cosmos-account met behulp van [Robo 3T](mongodb-robomongo.md).
* Meer informatie over het [configureren van leesvoorkeuren voor wereldwijd gedistribueerde apps](../cosmos-db/tutorial-global-distribution-mongodb.md).

<sup>Opmerking: In dit artikel wordt een functie van Azure Cosmos DB beschreven die compatibiliteit met het protocol voor draad met MongoDB-databases biedt. Microsoft voert geen MongoDB-databases uit om deze service te bieden. Azure Cosmos DB is niet aangesloten bij MongoDB, Inc.</sup>
