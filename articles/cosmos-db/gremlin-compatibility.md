---
title: Azure Cosmos DB compatibiliteit met TinkerPop-functies Gremlin
description: Naslag informatie over compatibiliteits problemen met de documentatie-engine
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 989a033a843b861c34dc9dbdbced50399f8e5cd7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81449881"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Gremlin-compatibiliteit Azure Cosmos DB
Azure Cosmos DB Graph engine voldoet aan de specificaties van [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) traversal, maar er zijn verschillen.

## <a name="behavior-differences"></a>Gedrags verschillen

* Azure Cosmos DB Graph-engine voert een ***breedte-eerste-*** navigatie uit terwijl TinkerPop Gremlin de diepte-First heeft. Dit gedrag behaalt betere prestaties in horizon taal schaalbaar systeem zoals Cosmos DB. 

## <a name="unsupported-features"></a>Niet-ondersteunde functies

* ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** is een programmeertaal-agnostische specificatie voor het doorkruisen van grafieken. Cosmos DB Graph ondersteunt dit nog niet. Gebruik `GremlinClient.SubmitAsync()` en laat door gaan als een teken reeks.

* ***`property(set, 'xyz', 1)`*** de kardinaliteit instellen wordt vandaag niet ondersteund. Gebruik in plaats daarvan `property(list, 'xyz', 1)`. Zie [vertex eigenschappen met TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties)voor meer informatie.

* ***`atch()`*** Hiermee kunt u query's uitvoeren op grafieken met declaratieve patroon vergelijking. Deze mogelijkheid is niet beschikbaar.

* ***Objecten als eigenschappen*** op hoek punten of randen worden niet ondersteund. Eigenschappen kunnen alleen primitieve typen of matrices zijn.

* ***Sorteren op matrix eigenschappen*** `order().by(<array property>)` wordt niet ondersteund. Alleen sorteren op primitieve typen wordt ondersteund.

* ***Niet-PRIMITIEVE JSON-typen*** worden niet ondersteund. Gebruik `string`, `number` `true` / of `false` typen. `null`waarden worden niet ondersteund. 

* De ***GraphSONv3*** -serialisatiefunctie wordt momenteel niet ondersteund. Gebruik `GraphSONv2` de klassen serializer, Reader en writer in de configuratie van de verbinding. De resultaten die door de Azure Cosmos DB Gremlin API worden geretourneerd, hebben niet dezelfde indeling als de GraphSON-indeling. 

* **Lambda-expressies en-functies** worden momenteel niet ondersteund. Dit omvat de `.map{<expression>}`, de `.by{<expression>}`en de `.filter{<expression>}` functies. Raadpleeg voor meer informatie en meer informatie over hoe u deze herschrijft met behulp van Gremlin stappen [een opmerking over lambdas](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas).

* ***Trans acties*** worden niet ondersteund vanwege gedistribueerde aard van het systeem.  Configureer het juiste consistentie model op het Gremlin-account om uw eigen schrijf bewerkingen te lezen en gebruik optimistische gelijktijdigheid om conflicterende schrijf bewerkingen op te lossen.

## <a name="next-steps"></a>Volgende stappen
* Ga naar [Cosmos DB gebruikers spraak](https://feedback.azure.com/forums/263030-azure-cosmos-db) pagina om feedback te delen en de focus te geven aan functies die voor u belang rijk zijn.
