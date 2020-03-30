---
title: Het uitvoeringsprofiel gebruiken om query's in Azure Cosmos DB Gremlin API te evalueren
description: Meer informatie over het oplossen en verbeteren van uw Gremlin-query's met de uitvoeringsprofielstap.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 5705ef4fb6aa895009d554617c968543cc3fcd63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75441853"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>De stap met het uitvoeringsprofiel gebruiken om de Gremlin-query's te evalueren

Dit artikel bevat een overzicht van het gebruik van de stap met het uitvoeringsprofiel voor grafiekdatabases van Azure Cosmos DB Gremlin API. Deze stap biedt relevante informatie voor het oplossen van problemen en het optimaliseren van query's, en is compatibel met Gremlin-query's die kunnen worden uitgevoerd voor een Cosmos DB Gremlin API-account.

Als u deze stap wilt `executionProfile()` gebruiken, sluit u de functieaanroep toe aan het einde van uw Gremlin-query. **Uw Gremlin-query wordt uitgevoerd** en het resultaat van de bewerking retourneert een JSON-antwoordobject met het queryuitvoeringsprofiel.

Bijvoorbeeld:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

Na het `executionProfile()` aanroepen van de stap is het antwoord een JSON-object dat de uitgevoerde Gremlin-stap, de totale tijd die het heeft opgelopen en een array van de Cosmos DB-runtime-operators waarde instructie in heeft geleid, bevat.

> [!NOTE]
> Deze implementatie voor Execution Profile is niet gedefinieerd in de Apache Tinkerpop specificatie. Het is specifiek voor de implementatie van Azure Cosmos DB Gremlin API.


## <a name="response-example"></a>Voorbeeld van reactie

Het volgende is een geannoteerd voorbeeld van de uitvoer die wordt geretourneerd:

> [!NOTE]
> Dit voorbeeld wordt geannoteerd met opmerkingen die de algemene structuur van het antwoord verklaren. Een daadwerkelijke uitvoeringProfielreactie bevat geen opmerkingen.

```json
[
  {
    // The Gremlin statement that was executed.
    "gremlin": "g.V('mary').out().executionProfile()",

    // Amount of time in milliseconds that the entire operation took.
    "totalTime": 28,

    // An array containing metrics for each of the steps that were executed. 
    // Each Gremlin step will translate to one or more of these steps.
    // This list is sorted in order of execution.
    "metrics": [
      {
        // This operation obtains a set of Vertex objects.
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetVertices",
        "time": 24,
        "annotations": {
          "percentTime": 85.71
        },
        "counts": {
          "resultCount": 2
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 2,
            "size": 696,
            "time": 0.4
          }
        ]
      },
      {
        // This operation obtains a set of Edge objects. 
        // Depending on the query, these might be directly adjacent to a set of vertices, 
        // or separate, in the case of an E() query.
        //
        // The metrics include: time, percentTime of total execution time, resultCount, 
        // fanoutFactor, count, size (in bytes) and time.
        "name": "GetEdges",
        "time": 4,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 1,
            "size": 419,
            "time": 0.67
          }
        ]
      },
      {
        // This operation obtains the vertices that a set of edges point at.
        // The metrics include: time, percentTime of total execution time and resultCount.
        "name": "GetNeighborVertices",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      },
      {
        // This operation represents the serialization and preparation for a result from 
        // the preceding graph operations. The metrics include: time, percentTime of total 
        // execution time and resultCount.
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

> [!NOTE]
> De stap executionProfile voert de Gremlin-query uit. Dit omvat `addV` `addE`de of stappen, die resulteren in de creatie en zal de wijzigingen die in de query. Als gevolg hiervan worden ook de aanvraageenheden die door de Gremlin-query worden gegenereerd, in rekening gebracht.

## <a name="execution-profile-response-objects"></a>Antwoordobjecten voor het uitvoeringsprofiel

De respons van een functie executionProfile() geeft een hiërarchie van JSON-objecten met de volgende structuur:
  - **Gremlin-bewerkingsobject**: vertegenwoordigt de gehele Gremlin-bewerking die is uitgevoerd. Bevat de volgende eigenschappen.
    - `gremlin`: De expliciete Gremlin-instructie die is uitgevoerd.
    - `totalTime`: De tijd, in milliseconden, dat de uitvoering van de stap gemaakt in. 
    - `metrics`: Een array die elk van de runtime-operatoren van Cosmos DB bevat die zijn uitgevoerd om de query te vervullen. Deze lijst wordt gesorteerd in volgorde van uitvoering.
    
  - **Cosmos DB runtime operators**: Vertegenwoordigt elk van de componenten van de gehele Gremlin operatie. Deze lijst wordt gesorteerd in volgorde van uitvoering. Elk object bevat de volgende eigenschappen:
    - `name`: Naam van de operator. Dit is het type stap dat is geëvalueerd en uitgevoerd. Lees meer in onderstaande tabel.
    - `time`: Hoeveelheid tijd, in milliseconden, die een bepaalde operator heeft ingenomen.
    - `annotations`: Bevat aanvullende informatie, specifiek voor de operator die is uitgevoerd.
    - `annotations.percentTime`: Percentage van de totale tijd die nodig was om de specifieke operator uit te voeren.
    - `counts`: Aantal objecten dat door deze operator uit de opslaglaag is geretourneerd. Dit is opgenomen `counts.resultCount` in de scalaire waarde binnen.
    - `storeOps`: Vertegenwoordigt een opslagbewerking die één of meerdere partities kan omvatten.
    - `storeOps.fanoutFactor`: Geeft het aantal partities weer waartoe deze specifieke opslagbewerking toegang heeft.
    - `storeOps.count`: Geeft het aantal resultaten weer dat deze opslagbewerking heeft geretourneerd.
    - `storeOps.size`: Geeft de grootte in bytes weer van het resultaat van een bepaalde opslagbewerking.

Cosmos DB Gremlin Runtime Operator|Beschrijving
---|---
`GetVertices`| Deze stap verkrijgt een gebaseerde set objecten uit de persistentielaag. 
`GetEdges`| Deze stap verkrijgt de randen die grenzen aan een set van vertices. Deze stap kan resulteren in een of meerdere opslagbewerkingen.
`GetNeighborVertices`| Deze stap verkrijgt de vertices die zijn aangesloten op een set randen. De randen bevatten de partitiesleutels en ID's van zowel hun bron als doelvertices.
`Coalesce`| Deze stap is verantwoordelijk voor de `coalesce()` evaluatie van twee bewerkingen wanneer de Gremlin-stap wordt uitgevoerd.
`CartesianProductOperator`| Deze stap berekent een cartesiaans product tussen twee gegevenssets. Meestal uitgevoerd wanneer de `to()` predicaten of `from()` worden gebruikt.
`ConstantSourceOperator`| Met deze stap wordt een expressie berekend om een constante waarde te produceren.
`ProjectOperator`| Met deze stap wordt een reactie voorbereid en geserialt met behulp van het resultaat van eerdere bewerkingen.
`ProjectAggregation`| Met deze stap wordt een antwoord voorbereid en geserialt voor een geaggregeerde bewerking.

> [!NOTE]
> Deze lijst wordt verder bijgewerkt zodra nieuwe operators worden toegevoegd.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Voorbeelden voor het analyseren van een reactie van een uitvoeringsprofiel

Hieronder volgen voorbeelden van veelvoorkomende optimalisaties die kunnen worden gespot met de reactie van het uitvoeringsprofiel:
  - Blinde fan-out query.
  - Ongefilterde query.

### <a name="blind-fan-out-query-patterns"></a>Blinde fan-out querypatronen

Ga uit van de volgende reactie van het uitvoeringsprofiel uit een **verdeelde grafiek:**

```json
[
  {
    "gremlin": "g.V('tt0093640').executionProfile()",
    "totalTime": 46,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 46,
        "annotations": {
          "percentTime": 100
        },
        "counts": {
          "resultCount": 1
        },
        "storeOps": [
          {
            "fanoutFactor": 5,
            "count": 1,
            "size": 589,
            "time": 75.61
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 1
        }
      }
    ]
  }
]
```

Daaruit kunnen de volgende conclusies worden getrokken:
- De query is een enkele ID-lookup, omdat `g.V('id')`de gremlin-instructie het patroon volgt.
- Afgaande `time` op de metrische, de latentie van deze query lijkt hoog te zijn, omdat het [meer dan 10 ms voor een enkele point-read operatie](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide).
- Als we kijken `storeOps` naar het object, `fanoutFactor` `5`kunnen we zien dat de is , wat betekent dat [5 partities](https://docs.microsoft.com/azure/cosmos-db/partition-data) werden benaderd door deze bewerking.

Als conclusie van deze analyse kunnen we vaststellen dat de eerste query toegang heeft tot meer partities dan nodig is. Dit kan worden aangepakt door de partitiesleutel in de query op te geven als een predicaat. Dit zal leiden tot minder latentie en minder kosten per query. Meer informatie over [grafiekpartitionering](graph-partitioning.md). Een meer optimale `g.V('tt0093640').has('partitionKey', 't1001')`query zou zijn .

### <a name="unfiltered-query-patterns"></a>Ongefilterde querypatronen

Vergelijk de volgende twee antwoorden op het uitvoeringsprofiel. Voor de eenvoud gebruiken deze voorbeelden één partitiegrafiek.

Deze eerste query haalt alle vertices met het label `tweet` en vervolgens verkrijgt hun naburige vertices:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').out().executionProfile()",
    "totalTime": 42,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 31,
        "annotations": {
          "percentTime": 73.81
        },
        "counts": {
          "resultCount": 30
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 13,
            "size": 6819,
            "time": 1.02
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 6,
        "annotations": {
          "percentTime": 14.29
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 20,
            "size": 7950,
            "time": 1.98
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 11.9
        },
        "counts": {
          "resultCount": 20
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.19
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 20
        }
      }
    ]
  }
]
```

Let op het profiel van dezelfde query, `has('lang', 'en')`maar nu met een extra filter, voordat u de aangrenzende hoekjes verkent:

```json
[
  {
    "gremlin": "g.V().hasLabel('tweet').has('lang', 'en').out().executionProfile()",
    "totalTime": 14,
    "metrics": [
      {
        "name": "GetVertices",
        "time": 14,
        "annotations": {
          "percentTime": 58.33
        },
        "counts": {
          "resultCount": 11
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 11,
            "size": 4807,
            "time": 1.27
          }
        ]
      },
      {
        "name": "GetEdges",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 18,
            "size": 7159,
            "time": 1.7
          }
        ]
      },
      {
        "name": "GetNeighborVertices",
        "time": 5,
        "annotations": {
          "percentTime": 20.83
        },
        "counts": {
          "resultCount": 18
        },
        "storeOps": [
          {
            "fanoutFactor": 1,
            "count": 4,
            "size": 1070,
            "time": 1.01
          }
        ]
      },
      {
        "name": "ProjectOperator",
        "time": 0,
        "annotations": {
          "percentTime": 0
        },
        "counts": {
          "resultCount": 18
        }
      }
    ]
  }
]
```

Deze twee query's bereikten hetzelfde resultaat, maar de eerste vereist meer aanvraageenheden omdat het een grotere initiële gegevensset moest herhalen voordat de aangrenzende items worden opgevraagd. We kunnen indicatoren van dit gedrag zien wanneer we de volgende parameters van beide antwoorden vergelijken:
- De `metrics[0].time` waarde is hoger in het eerste antwoord, wat aangeeft dat deze enkele stap langer duurde om op te lossen.
- De `metrics[0].counts.resultsCount` waarde is ook hoger in de eerste reactie, wat aangeeft dat de initiële werkgegevensset groter was.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [ondersteunde Gremlin-functies](gremlin-support.md) in Azure Cosmos DB. 
* Meer informatie over de [Gremlin-API in Azure Cosmos DB](graph-introduction.md).
