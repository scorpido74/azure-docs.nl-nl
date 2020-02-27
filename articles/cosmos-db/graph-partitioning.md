---
title: Partitioneren van gegevens in Azure Cosmos DB Gremlin-API
description: Lees hoe u een gepartitioneerde grafiek kunt gebruiken in Azure Cosmos DB. Dit artikel beschrijft ook de vereisten en best practices voor een gepartitioneerde grafiek.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 44d3b7c2b9e23b90f696162747d9728b18fb7d3f
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623379"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Met behulp van een gepartitioneerde grafiek in Azure Cosmos DB

Een van de belangrijkste functies van de Gremlin-API in Azure Cosmos DB is de mogelijkheid om grootschalige grafieken via horizontaal schalen. De containers kunnen onafhankelijk worden geschaald in termen van opslag en doorvoer. U kunt containers maken in Azure Cosmos DB die automatisch kunnen worden geschaald voor het opslaan van een graph-gegevens. De gegevens worden automatisch gebalanceerd op basis van de opgegeven **partitie sleutel**.

**Partitioneren is vereist** als de container wordt verwacht meer dan 20 GB op te slaan of als u meer dan 10.000 aanvraag eenheden per seconde (RUs) wilt toewijzen. Dezelfde algemene principes van het [Azure Cosmos DB partitioneren-mechanisme](partition-data.md) zijn van toepassing met een aantal grafische specifieke optimalisaties die hieronder worden beschreven.

![Graph-partitionering.](./media/graph-partitioning/graph-partitioning.png)

## <a name="graph-partitioning-mechanism"></a>Mechanisme voor het partitioneren van grafieken

De volgende richt lijnen beschrijven hoe de strategie voor partitioneren in Azure Cosmos DB werkt:

- **Beide hoek punten en randen worden opgeslagen als JSON-documenten**.

- Voor **hoek punten is een partitie sleutel vereist**. Deze sleutel wordt bepaald welke partitie het hoekpunt worden opgeslagen via een hash-algoritme. De naam van de partitie sleutel eigenschap wordt gedefinieerd bij het maken van een nieuwe container en heeft de volgende indeling: `/partitioning-key-name`.

- **Randen worden opgeslagen met hun bron hoekpunt**. Met andere woorden, voor elk hoekpunt dat de partitiesleutel wordt gedefinieerd waarin ze zijn opgeslagen, samen met de uitgaande randen. Deze optimalisatie wordt uitgevoerd om cross-Partition query's te voor komen bij het gebruik van de `out()` kardinaliteit in Graph-query's.

- **Randen bevatten verwijzingen naar de hoek punten waarnaar ze verwijzen**. Alle randen worden opgeslagen met de partitie sleutels en de Id's van de hoek punten waarnaar ze verwijzen. Deze berekening zorgt ervoor dat alle `out()` richtings query's altijd een gepartitioneerde en niet-sitegebonden query zijn. 

- **Graph-query's moeten een partitie sleutel opgeven**. Als u wilt profiteren van de horizontale partitionering in Azure Cosmos DB, moet de partitiesleutel worden opgegeven wanneer een hoekpunt is geselecteerd, indien mogelijk. De volgende zijn query's voor het selecteren van een of meerdere hoekpunten in een gepartitioneerde grafiek:

    - `/id` en `/label` worden niet ondersteund als partitie sleutels voor een container in de Gremlin-API.


    - Selecteer een hoek punt op ID en **gebruik vervolgens de `.has()` stap om de eigenschap van de partitie sleutel op te geven**: 
    
        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Een hoek punt selecteren door **een tuple op te geven, inclusief de partitie sleutel waarde en de id**: 
    
        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Het opgeven **van een matrix met Tuples van partitie sleutel waarden en-id's**:
    
        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Een set hoek punten selecteren met hun Id's en **een lijst met partitie sleutel waarden opgeven**: 
    
        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - Het gebruik van de **partitie strategie** aan het begin van een query en het opgeven van een partitie voor het bereik van de rest van de Gremlin-query: 
    
        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Aanbevolen procedures bij het gebruik van een gepartitioneerde grafiek

Gebruik de volgende richtlijnen voor optimale prestaties en schaalbaarheid bij het gebruik van gepartitioneerde grafieken met onbeperkte containers:

- **Geef altijd de partitie sleutel waarde op bij het uitvoeren van een query op een hoek punt**. Hoekpunt ophalen uit een bekende partitie is een manier prestaties bereiken. Alle volgende bewerkings belendings zijn altijd binnen het bereik van een partitie, aangezien randen de verwijzings-ID en partitie sleutel naar hun doel hoekpunten bevatten.

- **Gebruik de uitgaande richting bij het uitvoeren van een query op randen wanneer dat mogelijk is**. Zoals eerder vermeld, worden de randen opgeslagen met hun bron hoekpunten in de uitgaande richting. De kans op sorteren op query's over meerdere partities zijn dus geminimaliseerd wanneer de gegevens en query's met dit patroon waarmee u rekening moet worden ontworpen. In tegens telling is de `in()` query altijd een dure ventilator-out-query.

- **Kies een partitie sleutel waarmee gegevens gelijkmatig over de partities kunnen worden verdeeld**. Dit besluit is sterk afhankelijk van het gegevensmodel van de oplossing. Lees meer over het maken van een juiste partitie sleutel in [partitioneren en schalen in azure Cosmos DB](partition-data.md).

- **Optimaliseer query's voor het verkrijgen van gegevens binnen de grenzen van een partitie**. Een strategie voor het partitioneren van optimale zou worden uitgelijnd met de patronen in een query uitvoeren op. Query's die gegevens worden verkregen uit één partitie bevatten de best mogelijke prestaties.

## <a name="next-steps"></a>Volgende stappen

Vervolgens kunt u doorgaan met het lezen van de volgende artikelen:

* Meer informatie over [partitioneren en schalen in azure Cosmos DB](partition-data.md).
* Meer informatie over de [Gremlin-ondersteuning in de Gremlin-API](gremlin-support.md).
* Meer informatie over [Introduction to GREMLIN API](graph-introduction.md).
