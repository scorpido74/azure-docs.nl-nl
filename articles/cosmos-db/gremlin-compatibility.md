---
title: Azure Cosmos DB Gremlin compatibiliteit met TinkerPop functies
description: Compatibiliteitsproblemen met de grafiekgrafiek
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 989a033a843b861c34dc9dbdbced50399f8e5cd7
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81449881"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Azure Cosmos DB Gremlin-compatibiliteit
Azure Cosmos DB Graph engine volgt [apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) traversal stappen specificatie, maar er zijn verschillen.

## <a name="behavior-differences"></a>Gedragsverschillen

* Azure Cosmos DB Graph-engine draait ***eerst doordebreedte,*** terwijl TinkerPop Gremlin eerst diepte is. Dit gedrag bereikt betere prestaties in horizontaal schaalbaar systeem zoals Cosmos DB. 

## <a name="unsupported-features"></a>Niet-ondersteunde functies

* ***[Gremlin Bytecode](https://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** is een programmeertaal-agnostische specificatie voor het doorkruisen van grafieken. Cosmos DB Graph ondersteunt het nog niet. Gebruik `GremlinClient.SubmitAsync()` en pass traversal als een tekst string.

* ***`property(set, 'xyz', 1)`*** vaste kardinaliteit wordt vandaag niet ondersteund. Gebruik in plaats daarvan `property(list, 'xyz', 1)`. Zie [Vertex properties met TinkerPop](http://tinkerpop.apache.org/docs/current/reference/#vertex-properties)voor meer informatie.

* ***`atch()`*** hiermee u grafieken opvragen met behulp van declaratieve patroonmatching. Deze mogelijkheid is niet beschikbaar.

* ***Objecten als eigenschappen*** op vertices of randen worden niet ondersteund. Eigenschappen kunnen alleen primitieve typen of matrices zijn.

* ***Sorteren op arrayeigenschappen*** `order().by(<array property>)` wordt niet ondersteund. Alleen sorteren op primitieve typen wordt ondersteund.

* ***Niet-primitieve JSON-typen*** worden niet ondersteund. `string`Gebruik, `number`of `true` / `false` typen. `null`waarden worden niet ondersteund. 

* ***GraphSONv3*** serializer wordt momenteel niet ondersteund. Gebruik `GraphSONv2` de klassen Serializer, Reader en Writer in de verbindingsconfiguratie. De resultaten die worden geretourneerd door de Azure Cosmos DB Gremlin API hebben niet dezelfde indeling als de GraphSON-indeling. 

* **Lambda-uitdrukkingen en -functies** worden momenteel niet ondersteund. Dit omvat `.map{<expression>}`de `.by{<expression>}`, `.filter{<expression>}` de , en de functies. Zie [Een notitie op Lambdas](http://tinkerpop.apache.org/docs/current/reference/#a-note-on-lambdas)voor meer informatie en om te leren hoe u ze herschrijven met gremlin-stappen.

* ***Transacties*** worden niet ondersteund vanwege de gedistribueerde aard van het systeem.  Configureer het juiste consistentiemodel op Gremlin-account om "uw eigen schrijft te lezen" en gebruik optimistische gelijktijdigheid om conflicterende schrijfbewerkingen op te lossen.

## <a name="next-steps"></a>Volgende stappen
* Ga naar [de stempagina van Cosmos DB-gebruikers](https://feedback.azure.com/forums/263030-azure-cosmos-db) om feedback te delen en het team te helpen zich te concentreren op functies die belangrijk voor je zijn.
