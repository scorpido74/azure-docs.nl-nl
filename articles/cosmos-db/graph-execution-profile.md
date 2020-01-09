---
title: Het uitvoerings profiel gebruiken om query's te evalueren in Azure Cosmos DB Gremlin-API
description: Meer informatie over het oplossen van uw Gremlin-query's met behulp van de stap uitvoerings profiel.
services: cosmos-db
author: luisbosquez
manager: kfile
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 03/27/2019
ms.author: lbosq
ms.openlocfilehash: 5705ef4fb6aa895009d554617c968543cc3fcd63
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75441853"
---
# <a name="how-to-use-the-execution-profile-step-to-evaluate-your-gremlin-queries"></a>De stap uitvoerings profiel gebruiken om uw Gremlin-query's te evalueren

Dit artikel bevat een overzicht van het gebruik van de stap uitvoerings profiel voor Azure Cosmos DB Gremlin API Graph-data bases. Deze stap bevat relevante informatie voor het oplossen van problemen en het uitvoeren van query optimalisaties, en is compatibel met elke Gremlin-query die kan worden uitgevoerd met een Cosmos DB Gremlin-API-account.

Als u deze stap wilt gebruiken, voegt u de `executionProfile()` functie aanroep toe aan het einde van uw Gremlin-query. **Uw Gremlin-query wordt uitgevoerd** en het resultaat van de bewerking retourneert een JSON-antwoord object met het uitvoerings profiel voor de query.

Bijvoorbeeld:

```java
    // Basic traversal
    g.V('mary').out()

    // Basic traversal with execution profile call
    g.V('mary').out().executionProfile()
```

Na het aanroepen van de `executionProfile()` stap is het antwoord een JSON-object dat de uitgevoerde Gremlin-stap bevat, de totale tijd die nodig was en een matrix van de Opera tors van de Cosmos DB runtime die de instructie heeft opgeleverd.

> [!NOTE]
> Deze implementatie voor het uitvoerings profiel is niet gedefinieerd in de Apache Tinkerpop-specificatie. Het is specifiek voor de implementatie van de Gremlin-API van Azure Cosmos DB.


## <a name="response-example"></a>Antwoord voorbeeld

Hieronder ziet u een voor beeld van de uitvoer die wordt geretourneerd:

> [!NOTE]
> Dit voor beeld is voorzien van opmerkingen die de algemene structuur van het antwoord verklaren. Een echt executionProfile-antwoord bevat geen opmerkingen.

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
> In de stap executionProfile wordt de Gremlin-query uitgevoerd. Dit omvat de `addV`-of `addE`-stappen, wat resulteert in het maken en de wijzigingen doorvoert die zijn opgegeven in de query. Als gevolg hiervan worden er ook kosten in rekening gebracht voor de aanvraag eenheden die zijn gegenereerd door de Gremlin-query.

## <a name="execution-profile-response-objects"></a>Reactie objecten uitvoerings profiel

Het antwoord van een executionProfile ()-functie levert een hiërarchie van JSON-objecten met de volgende structuur:
  - **Gremlin-bewerkings object**: vertegenwoordigt de gehele Gremlin-bewerking die is uitgevoerd. Bevat de volgende eigenschappen.
    - `gremlin`: de expliciete Gremlin-instructie die is uitgevoerd.
    - `totalTime`: de tijd, in milliseconden, die de uitvoering van de stap is gemaakt in. 
    - `metrics`: een matrix die alle Cosmos DB runtime-Opera tors bevat die zijn uitgevoerd om te voldoen aan de query. Deze lijst wordt in volg orde van uitvoering gesorteerd.
    
  - **Cosmos DB runtime-Opera tors**: vertegenwoordigt elk van de onderdelen van de hele Gremlin-bewerking. Deze lijst wordt in volg orde van uitvoering gesorteerd. Elk object bevat de volgende eigenschappen:
    - `name`: de naam van de operator. Dit is het type fase dat is geëvalueerd en uitgevoerd. Meer informatie vindt u in de onderstaande tabel.
    - `time`: de hoeveelheid tijd, in milliseconden, dat een bepaalde operator heeft geduurd.
    - `annotations`: bevat aanvullende informatie die specifiek is voor de operator die is uitgevoerd.
    - `annotations.percentTime`: percentage van de totale tijd die nodig is om de specifieke operator uit te voeren.
    - `counts`: het aantal objecten dat door deze operator van de opslaglaag is geretourneerd. Dit is opgenomen in de `counts.resultCount` scalaire waarde binnen.
    - `storeOps`: vertegenwoordigt een opslag bewerking die een of meer partities kan omvatten.
    - `storeOps.fanoutFactor`: geeft het aantal partities aan waarvoor deze specifieke opslag bewerking is geopend.
    - `storeOps.count`: geeft het aantal resultaten dat deze opslag bewerking heeft geretourneerd.
    - `storeOps.size`: vertegenwoordigt de grootte in bytes van het resultaat van een bepaalde opslag bewerking.

Cosmos DB-operator Gremlin runtime|Beschrijving
---|---
`GetVertices`| In deze stap wordt een gegroepeerde set objecten opgehaald van de laag persistentie. 
`GetEdges`| Met deze stap worden de randen opgehaald die grenzen aan een set hoek punten. Deze stap kan leiden tot een of meer opslag bewerkingen.
`GetNeighborVertices`| Met deze stap worden de hoek punten opgehaald die zijn verbonden met een reeks randen. De randen bevatten de partitie sleutels en de ID van de bron-en doel hoekpunten.
`Coalesce`| Dit geldt voor de evaluatie van twee bewerkingen telkens wanneer de `coalesce()` Gremlin-stap wordt uitgevoerd.
`CartesianProductOperator`| Deze stap berekent een Cartesisch product tussen twee gegevens sets. Wordt gewoonlijk uitgevoerd wanneer de predikaten `to()` of `from()` worden gebruikt.
`ConstantSourceOperator`| In deze stap wordt een expressie berekend om een constante waarde te produceren als resultaat.
`ProjectOperator`| In deze stap wordt een reactie voor bereid en geserialiseerd met het resultaat van voor gaande bewerkingen.
`ProjectAggregation`| In deze stap wordt een reactie voor een geaggregeerde bewerking voor bereid en geserialiseerd.

> [!NOTE]
> Deze lijst wordt nog steeds bijgewerkt als nieuwe Opera tors worden toegevoegd.

## <a name="examples-on-how-to-analyze-an-execution-profile-response"></a>Voor beelden van het analyseren van een uitvoerings profiel reactie

Hier volgen enkele voor beelden van algemene optimalisaties die kunnen worden Spotted met behulp van de reactie van het uitvoerings profiel:
  - Blinde ventilator-out-query.
  - Niet-gefilterde query.

### <a name="blind-fan-out-query-patterns"></a>Blinde ventilatoren query patronen

Stel dat de volgende uitvoerings profiel reactie van een **gepartitioneerde grafiek**:

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

De volgende conclusies kunnen worden gemaakt:
- De query is een enkelvoudige ID-zoek opdracht, omdat de Gremlin-instructie het patroon volgt `g.V('id')`.
- Beoordelings van de `time` metriek lijkt de latentie van deze query hoog te zijn, omdat het [meer is dan 10 MS voor één punt-Lees bewerking](https://docs.microsoft.com/azure/cosmos-db/introduction#guaranteed-low-latency-at-99th-percentile-worldwide).
- Als we het `storeOps`-object bekijken, kunnen we zien dat de `fanoutFactor` `5`is. Dit betekent dat er [vijf partities](https://docs.microsoft.com/azure/cosmos-db/partition-data) zijn geopend door deze bewerking.

Als gevolg van deze analyse kunnen we bepalen dat de eerste query toegang krijgt tot meer partities dan nodig is. Dit kan worden verholpen door de partitie sleutel in de query als een predikaat op te geven. Dit leidt tot minder latentie en minder kosten per query. Meer informatie over [Graph-partitionering](graph-partitioning.md). Er wordt een optimale query `g.V('tt0093640').has('partitionKey', 't1001')`.

### <a name="unfiltered-query-patterns"></a>Niet-gefilterde query patronen

Vergelijk de volgende twee uitvoerings profiel reacties. Voor de eenvoud gebruiken deze voor beelden één gepartitioneerde grafiek.

Met deze eerste query worden alle hoek punten opgehaald met het label `tweet` en worden vervolgens de naburige hoek punten opgehaald:

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

Let op het profiel van dezelfde query, maar nu met een extra filter, `has('lang', 'en')`, voordat u de aangrenzende hoek punten kunt verkennen:

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

Deze twee query's bereiken hetzelfde resultaat, maar in het eerste geval zijn er meer aanvraag eenheden nodig, omdat er een grotere initiële gegevensset moet worden herhaald voordat de aangrenzende items kunnen worden opgevraagd. Er kunnen indica toren van dit gedrag worden weer geven bij het vergelijken van de volgende para meters van beide antwoorden:
- De waarde van `metrics[0].time` is hoger in het eerste antwoord, wat aangeeft dat deze enkele stap langer duurde om op te lossen.
- De `metrics[0].counts.resultsCount` waarde is ook hoger in het eerste antwoord, wat aangeeft dat de oorspronkelijke werk gegevensset groter is.

## <a name="next-steps"></a>Volgende stappen
* Meer informatie over de [ondersteunde Gremlin-functies](gremlin-support.md) in azure Cosmos db. 
* Meer informatie over de [Gremlin-API vindt u in azure Cosmos DB](graph-introduction.md).
