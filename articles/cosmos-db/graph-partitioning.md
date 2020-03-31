---
title: Gegevenspartitionering in Azure Cosmos DB Gremlin API
description: Meer informatie over hoe u een gepartitioneerde grafiek gebruiken in Azure Cosmos DB. In dit artikel worden ook de vereisten en aanbevolen procedures voor een verdeelde grafiek beschreven.
author: luisbosquez
ms.author: lbosq
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 06/24/2019
ms.custom: seodec18
ms.openlocfilehash: 44d3b7c2b9e23b90f696162747d9728b18fb7d3f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623379"
---
# <a name="using-a-partitioned-graph-in-azure-cosmos-db"></a>Gepartitioneerde graaf gebruiken in Azure Cosmos DB

Een van de belangrijkste kenmerken van de Gremlin API in Azure Cosmos DB is de mogelijkheid om grootschalige grafieken te verwerken via horizontale schaling. De containers kunnen onafhankelijk schalen in termen van opslag en doorvoer. U containers maken in Azure Cosmos DB die automatisch kunnen worden geschaald om een grafiekgegevens op te slaan. De gegevens worden automatisch in evenwicht gebracht op basis van de opgegeven **partitiesleutel**.

**Partitionering is vereist** als de container naar verwachting meer dan 20 GB groot wordt opgeslagen of als u meer dan 10.000 aanvraageenheden per seconde (RU's) wilt toewijzen. Dezelfde algemene principes van het [Azure Cosmos DB-partitioneringsmechanisme](partition-data.md) zijn van toepassing op een paar grafiekspecifieke optimalisaties die hieronder worden beschreven.

![Grafiekpartitionering.](./media/graph-partitioning/graph-partitioning.png)

## <a name="graph-partitioning-mechanism"></a>Het scheidingsmechanisme van de grafiek

In de volgende richtlijnen wordt beschreven hoe de partitioneringsstrategie in Azure Cosmos DB werkt:

- **Zowel vertices als randen worden opgeslagen als JSON-documenten.**

- **Vertices vereisen een partitiesleutel.** Deze sleutel bepaalt in welke partitie het hoekpunt wordt opgeslagen via een hashing-algoritme. De eigenschapsnaam partitiesleutel wordt gedefinieerd bij het maken `/partitioning-key-name`van een nieuwe container en heeft een indeling: .

- **Randen worden opgeslagen met hun bronhoek.** Met andere woorden, voor elke hoekpunt bepaalt de partitiesleutel waar ze samen met de uitgaande randen worden opgeslagen. Deze optimalisatie wordt uitgevoerd om kruispartitiequery's te voorkomen bij het gebruik van de `out()` kardinaliteit in grafiekquery's.

- **Randen bevatten verwijzingen naar de hoekpunten waarnaar ze verwijzen**. Alle randen worden opgeslagen met de partitiesleutels en id's van de hoekpunten waarnaar ze wijzen. Met deze `out()` berekening worden alle richtingsquery's altijd een gepartitioneerde query en geen blinde kruispartitiequery. 

- **Grafiekquery's moeten een partitiesleutel opgeven.** Om optimaal gebruik te maken van de horizontale partitionering in Azure Cosmos DB, moet de partitiesleutel worden opgegeven wanneer een enkel hoekpunt is geselecteerd, wanneer dat mogelijk is. Hieronder volgen query's voor het selecteren van een of meerdere hoekgrepen in een gepartitioneerde grafiek:

    - `/id`en `/label` worden niet ondersteund als partitiesleutels voor een container in Gremlin API.


    - Een hoekpunt selecteren op id en **vervolgens de `.has()` stap gebruiken om de eigenschap partitiesleutel op te geven:** 
    
        ```java
        g.V('vertex_id').has('partitionKey', 'partitionKey_value')
        ```
    
    - Een hoekpunt selecteren door **een tuple op te geven met inbegrip van de waarde van de partitiesleutel en id:** 
    
        ```java
        g.V(['partitionKey_value', 'vertex_id'])
        ```
        
    - Een **array met tuples met partitiesleutelwaarden en -iD's**opgeven:
    
        ```java
        g.V(['partitionKey_value0', 'verted_id0'], ['partitionKey_value1', 'vertex_id1'], ...)
        ```
        
    - Een set hoekpunten selecteren met hun-geïdentificeerde gegevens en **een lijst met partitiesleutelwaarden opgeven:** 
    
        ```java
        g.V('vertex_id0', 'vertex_id1', 'vertex_id2', …).has('partitionKey', within('partitionKey_value0', 'partitionKey_value01', 'partitionKey_value02', …)
        ```

    - De **partitiestrategie** aan het begin van een query gebruiken en een partitie opgeven voor het bereik van de rest van de Gremlin-query: 
    
        ```java
        g.withStrategies(PartitionStrategy.build().partitionKey('partitionKey').readPartitions('partitionKey_value').create()).V()
        ```

## <a name="best-practices-when-using-a-partitioned-graph"></a>Aanbevolen procedures bij het gebruik van een gepartitioneerde grafiek

Gebruik de volgende richtlijnen om prestaties en schaalbaarheid te garanderen bij het gebruik van partitiegrafieken met onbeperkte containers:

- **Geef altijd de waarde van de partitiesleutel op bij het opvragen van een hoekpunt**. Het verkrijgen van hoekpunt van een bekende partitie is een manier om prestaties te bereiken. Alle volgende adjacency-bewerkingen worden altijd toegewezen aan een partitie, omdat randen referentie-id en partitiesleutel bevatten voor hun doelkenmerken.

- **Gebruik de uitgaande richting wanneer het mogelijk is om randen op te vragen.** Zoals hierboven vermeld, worden de randen opgeslagen met hun bronvertices in de uitgaande richting. Dus de kans op toevlucht tot cross-partitie query's worden geminimaliseerd wanneer de gegevens en query's zijn ontworpen met dit patroon in het achterhoofd. Integendeel, de `in()` query zal altijd een dure fan-out query.

- **Kies een partitiesleutel die gegevens gelijkmatig over partities verdeelt.** Deze beslissing hangt sterk af van het datamodel van de oplossing. Lees meer over het maken van een geschikte partitiesleutel in [Partitionering en schalen in Azure Cosmos DB](partition-data.md).

- **Optimaliseer query's om gegevens te verkrijgen binnen de grenzen van een partitie.** Een optimale partitioneringsstrategie zou worden afgestemd op de querypatronen. Query's die gegevens uit één partitie verkrijgen, bieden de best mogelijke prestaties.

## <a name="next-steps"></a>Volgende stappen

Vervolgens u verder gaan met het lezen van de volgende artikelen:

* Meer informatie over [partitie en schaal in Azure Cosmos DB](partition-data.md).
* Meer informatie over de [Gremlin-ondersteuning in Gremlin API](gremlin-support.md).
* Meer informatie over [inleiding tot Gremlin API](graph-introduction.md).
