---
title: Azure Cosmos DB compatibiliteit met TinkerPop-functies Gremlin
description: Naslag informatie over compatibiliteits problemen met de documentatie-engine
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: sngun
ms.openlocfilehash: 0ed5824859b8463919a809861993f9f98a4f9251
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/15/2019
ms.locfileid: "72327037"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Gremlin-compatibiliteit Azure Cosmos DB
Azure Cosmos DB Graph engine voldoet aan de specificaties van [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) traversal, maar er zijn verschillen.

## <a name="behavior-differences"></a>Gedrags verschillen

* Azure Cosmos DB Graph-engine voert een ***breedte-eerste-*** navigatie uit terwijl TinkerPop Gremlin de diepte-First heeft. Dit gedrag behaalt betere prestaties in horizon taal schaalbaar systeem zoals Cosmos DB. 

## <a name="unsupported-features"></a>Niet-ondersteunde functies

* ***[Gremlin Bytecode](http://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** is een programmeertaal-agnostische specificatie voor het doorkruisen van grafieken. Cosmos DB Graph ondersteunt dit nog niet. Gebruik ```GremlinClient.SubmitAsync()``` en geef passing door als een teken reeks.

* de kardinaliteit van de ***```property(set, 'xyz', 1)```-*** set wordt vandaag niet ondersteund. Gebruik in plaats daarvan ```property(list, 'xyz', 1)```.

* met ***```match()```*** kunt u een query uitvoeren op grafieken met declaratieve patroon vergelijking. Deze mogelijkheid is niet beschikbaar.

* ***Objecten als eigenschappen*** op hoek punten of randen worden niet ondersteund. Eigenschappen kunnen alleen primitieve typen of matrices zijn.

* ***Sorteren op matrix eigenschappen*** ```.order().by(<array property>)``` wordt niet ondersteund. Alleen sorteren op primitieve typen wordt ondersteund.

* ***Niet-PRIMITIEVE JSON-typen*** worden niet ondersteund. Gebruik ```string```, ```number``` of ```true``` @ no__t-3 @ no__t-4-typen. ```null```-waarden worden niet ondersteund. 

* ***GraphSONv3*** serializer is momenteel niet beschikbaar.

* ***Trans acties*** worden niet ondersteund vanwege gedistribueerde aard van het systeem.  Configureer het juiste consistentie model op het Gremlin-account om uw eigen schrijf bewerkingen te lezen en gebruik optimistische gelijktijdigheid om conflicterende schrijf bewerkingen op te lossen.

## <a name="next-steps"></a>Volgende stappen
* Ga naar [Cosmos DB gebruikers spraak](https://feedback.azure.com/forums/263030-azure-cosmos-db) pagina om feedback te delen en de focus te geven aan functies die voor u belang rijk zijn.
