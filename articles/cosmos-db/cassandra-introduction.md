---
title: Inleiding tot de Azure Cosmos DB Cassandra-API
description: Meer informatie over hoe u Azure Cosmos DB kunt gebruiken om bestaande toepassingen te ' til en verschuiven ' en nieuwe toepassingen te bouwen met behulp van de Cassandra-Stuur Programma's en CQL
author: kanshiG
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: overview
ms.date: 05/21/2019
ms.author: govindk
ms.reviewer: sngun
ms.openlocfilehash: cc6b1a2516a454c843f176e947cc3a56186b0b47
ms.sourcegitcommit: b5106424cd7531c7084a4ac6657c4d67a05f7068
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/14/2020
ms.locfileid: "75939947"
---
# <a name="introduction-to-the-azure-cosmos-db-cassandra-api"></a>Inleiding tot de Azure Cosmos DB Cassandra-API

Azure Cosmos DB Cassandra-API kan worden gebruikt als gegevensopslag voor apps die zijn geschreven voor [Apache Cassandra](http://cassandra.apache.org). Dit betekent dat uw bestaande Cassandra-toepassing nu kan communiceren met de Azure Cosmos DB Cassandra-API, door gebruik te maken van bestaande [Apache-apparaatstuurprogramma’s](http://cassandra.apache.org/doc/latest/getting_started/drivers.html?highlight=driver) die compatibel zijn met CQLv4. In veel gevallen kunt u overschakelen van Apache Cassandra naar de Cassandra-API van Azure Cosmos DB door alleen een verbindingsreeks te wijzigen. 

De Cassandra-API stelt u in staat om gegevens te gebruiken die zijn opgeslagen in Azure Cosmos DB. Dit doet u via de Cassandra-querytaal (CQL), hulpprogramma’s op basis van de Cassandra-querytaal (bijvoorbeeld cqlsh) en Cassandra-clientstuurprogramma’s waarmee u al vertrouwd bent.

## <a name="what-is-the-benefit-of-using-apache-cassandra-api-for-azure-cosmos-db"></a>Wat is het voordeel van het gebruik van de Apache Cassandra-API voor Azure Cosmos DB?

**Geen operationeel management**: Azure Cosmos DB Cassandra-API is een volledig beheerde cloudservice, waardoor beheerders zich niet bezig hoeven te houden met het beheren en controleren van talloze instellingen in OS-, JVM- en yaml-bestanden en de bijbehorende wisselwerkingen. Azure Cosmos DB biedt controle van doorvoer, latentie, opslag, beschikbaarheid en waarschuwingen die kunnen worden geconfigureerd.

**Prestatiebeheer**: Azure Cosmos DB biedt gegarandeerde lage latentie van lees- en schrijfbewerkingen voor een percentiel van 99%, ondersteund met een SLA. Gebruikers hoeven zich geen zorgen te maken over operationele overhead voor hoge prestaties en lage latentie van lees- en schrijfbewerkingen. Dit betekent dat gebruikers zich niet bezig hoeven te houden met geplande compressie, het beheren van testblokken, het handmatig instellen van bloomfilters en replica’s. Azure Cosmos DB verwijdert de overhead om deze zaken te beheren, waardoor u zich beter kunt focussen op de logica van de toepassing.

**Mogelijkheid om bestaande code en hulpprogramma’s te gebruiken**: Azure Cosmos DB biedt compatibiliteit van wire-protocolniveau’s met bestaande Cassandra-SDK’s en hulpprogramma’s. Door deze compatibiliteit kunt u bestaande codebase gebruiken met de Azure Cosmos DB Cassandra-API, met slechts een klein aantal wijzigingen.

**Elasticiteit van doorvoer en opslag**: Azure Cosmos DB biedt elasticiteit van gegarandeerde doorvoer naar alle regio’s en kan de geboden doorvoer opschalen met Azure Portal, PowerShell of de CLI. U kunt naar behoefte flexibel de schaal van de opslag en doorvoer aanpassen met voorspelbare prestaties.

**Wereldwijde distributie en beschikbaarheid**: Azure Cosmos DB heeft de mogelijkheid om gegevens wereldwijd te distribueren over alle Azure-regio’s en de gegevens lokaal aanbieden terwijl lage latentie gegevenstoegang en hoge beschikbaarheid gegarandeerd blijft. Azure Cosmos DB biedt 99,99% hoge beschikbaarheid binnen een regio, en 99,999% beschikbaarheid voor lezen en schrijven in meerdere regio’s, zonder operationele overhead. Lees het artikel [Gegevens wereldwijd distribueren](distribute-data-globally.md) voor meer informatie. 

**Keuze in consistentie**: Azure Cosmos DB biedt de keuze uit vijf goed gedefinieerde consistentieniveaus voor een optimale balans tussen consistentie en prestaties. Deze consistentieniveaus zijn: sterk, gebonden-verouderd, sessie, consistent voorvoegsel en mogelijk. Op basis van deze goed gedefinieerde, praktische en intuïtieve consistentieniveaus kunnen ontwikkelaars een nauwkeurige balans vinden tussen de consistentie, beschikbaarheid en latentie. Lees het artikel [consistentieniveaus](consistency-levels.md) voor meer informatie. 

**Zakelijk niveau**: Azure Cosmos DB biedt [nalevingscertificeringen](https://www.microsoft.com/trustcenter) om ervoor te zorgen dat het platform veilig kan worden gebruikt. Azure Cosmos DB biedt ook versleuteling van niet-actieve en actieve gegevens, een IP-firewall en auditlogboeken voor beheeractiviteiten.

## <a name="next-steps"></a>Volgende stappen

* U kunt snel aan de slag met het bouwen van de volgende taalspecifieke toepassingen om Cassandra-API-gegevens te maken en beheren:
  - [Node.js-app](create-cassandra-nodejs.md)
  - [.NET app](create-cassandra-dotnet.md)
  - [Python app](create-cassandra-python.md)

* Ga aan de slag met het [maken van een Cassandra-API-account, -database en een tabel](create-cassandra-api-account-java.md) met behulp van een Java-toepassing.

* [Laad voorbeeldgegevens in de Cassandra-API-tabel](cassandra-api-load-data.md) met behulp van een Java-toepassing.

* [Zoek in gegevens in de Cassandra-API-account](cassandra-api-query-data.md) met behulp van een Java-toepassing.

* Lees het artikel [Cassandra-ondersteuning](cassandra-support.md) voor meer informatie over Apache Cassandra-functies ondersteund door Azure Cosmos DB Cassandra API.

* Lees de [veelgestelde vragen (FAQ)](faq.md#cassandra).
