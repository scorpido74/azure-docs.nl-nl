---
title: Azure Cosmos DB compatibiliteit met TinkerPop-functies Gremlin
description: Naslag informatie over compatibiliteits problemen met de documentatie-engine
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 1db7937cb574ce62986f25e0bfa688dc54b5c606
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84700596"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Gremlin-compatibiliteit Azure Cosmos DB
Azure Cosmos DB Graph engine voldoet aan de specificaties van [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) traversal, maar er zijn verschillen in de implementatie die specifiek zijn voor Azure Cosmos db. Zie het artikel [GREMLIN API wire protocol support](gremlin-support.md) voor meer informatie over de lijst met ondersteunde Gremlin-stappen.

## <a name="behavior-differences"></a>Gedrags verschillen

* Azure Cosmos DB Graph-engine voert een ***breedte-eerste-*** navigatie uit terwijl TinkerPop Gremlin de diepte-First heeft. Dit gedrag behaalt betere prestaties in horizon taal schaalbaar systeem zoals Cosmos DB. 

## <a name="unsupported-features"></a>Niet-ondersteunde functies

* ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** is een programmeertaal-agnostische specificatie voor het doorkruisen van grafieken. Cosmos DB Graph ondersteunt dit nog niet. Gebruik `GremlinClient.SubmitAsync()` en laat door gaan als een teken reeks.

* ***`property(set, 'xyz', 1)`*** de kardinaliteit instellen wordt vandaag niet ondersteund. Gebruik in plaats daarvan `property(list, 'xyz', 1)`. Zie [vertex eigenschappen met TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties)voor meer informatie.

* De *** `match()` stap*** is momenteel niet beschikbaar. Deze stap biedt declaratieve query mogelijkheden.

* ***Objecten als eigenschappen*** op hoek punten of randen worden niet ondersteund. Eigenschappen kunnen alleen primitieve typen of matrices zijn.

* ***Sorteren op matrix eigenschappen*** `order().by(<array property>)` wordt niet ondersteund. Alleen sorteren op primitieve typen wordt ondersteund.

* ***Niet-PRIMITIEVE JSON-typen*** worden niet ondersteund. Gebruik `string` , `number` of `true` / `false` typen. `null`waarden worden niet ondersteund. 

* De ***GraphSONv3*** -serialisatiefunctie wordt momenteel niet ondersteund. Gebruik `GraphSONv2` de klassen serializer, Reader en writer in de configuratie van de verbinding. De resultaten die door de Azure Cosmos DB Gremlin API worden geretourneerd, hebben niet dezelfde indeling als de GraphSON-indeling. 

* **Lambda-expressies en-functies** worden momenteel niet ondersteund. Dit omvat de `.map{<expression>}` , de en de `.by{<expression>}` `.filter{<expression>}` functies. Raadpleeg voor meer informatie en meer informatie over hoe u deze herschrijft met behulp van Gremlin stappen [een opmerking over lambdas](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* ***Trans acties*** worden niet ondersteund vanwege gedistribueerde aard van het systeem.  Configureer het juiste consistentie model op het Gremlin-account om uw eigen schrijf bewerkingen te lezen en gebruik optimistische gelijktijdigheid om conflicterende schrijf bewerkingen op te lossen.

## <a name="known-limitations"></a>Bekende beperkingen

* **Index gebruik voor Gremlin-query's met `.V()` Stapsgewijze stappen**: op dit moment wordt alleen de eerste `.V()` aanroep van een passage gebruikt voor het omzetten van alle filters of predikaten die eraan zijn gekoppeld. Bij volgende aanroepen wordt de index niet geraadpleegd, waardoor de latentie en de kosten van de query kunnen worden verhoogd.
    
    Uitgaande van de standaard indexering, een typische Lees Gremlin-query die begint met de `.V()` stap, worden para meters in de bijgevoegde filter stappen gebruikt, zoals `.has()` of `.where()` om de kosten en prestaties van de query te optimaliseren. Bijvoorbeeld:

    ```java
    g.V().has('category', 'A')
    ```

    Als er echter meer dan één `.V()` stap is opgenomen in de Gremlin-query, is de oplossing van de gegevens voor de query mogelijk niet optimaal. Voer de volgende query uit als voor beeld:

    ```java
    g.V().has('category', 'A').as('a').V().has('category', 'B').as('b').select('a', 'b')
    ```

    Met deze query worden twee groepen hoek punten geretourneerd op basis van de eigenschap met de naam `category` . In dit geval wordt alleen de eerste aanroep `g.V().has('category', 'A')` gebruikt om de index te gebruiken voor het omzetten van de hoek punten op basis van de waarden van hun eigenschappen.

    Een tijdelijke oplossing voor deze query is het gebruik van subnavigatie stappen zoals `.map()` en `union()` . Hieronder ziet u geïllustreerd:

    ```java
    // Query workaround using .map()
    g.V().has('category', 'A').as('a').map(__.V().has('category', 'B')).as('b').select('a','b')

    // Query workaround using .union()
    g.V().has('category', 'A').fold().union(unfold(), __.V().has('category', 'B'))
    ```

    U kunt de prestaties van de query's controleren met behulp van de [Gremlin `executionProfile()` stap] (Graph-Execution-profile.MD.

## <a name="next-steps"></a>Volgende stappen
* Ga naar [Cosmos DB gebruikers spraak](https://feedback.azure.com/forums/263030-azure-cosmos-db) pagina om feedback te delen en de focus te geven aan functies die voor u belang rijk zijn.
