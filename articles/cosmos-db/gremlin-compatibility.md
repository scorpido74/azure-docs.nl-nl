---
title: Azure Cosmos DB compatibiliteit met TinkerPop-functies Gremlin
description: Naslag informatie over compatibiliteits problemen met de documentatie-engine
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 09/10/2019
ms.author: olignat
ms.openlocfilehash: d58d1ea50251c3568161ce008db2672ff0fd6372
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70911041"
---
# <a name="azure-cosmos-db-gremlin-compatibility"></a>Gremlin-compatibiliteit Azure Cosmos DB
Azure Cosmos DB Graph engine voldoet aan de specificaties van [Apache TinkerPop](https://tinkerpop.apache.org/docs/current/reference/#graph-traversal-steps) traversal, maar er zijn verschillen.

## <a name="behavior-differences"></a>Gedrags verschillen

* Azure Cosmos DB Graph-engine voert een ***breedte-eerste-*** navigatie uit terwijl TinkerPop Gremlin de diepte-First heeft. Dit gedrag behaalt betere prestaties in horizon taal schaalbaar systeem zoals Cosmos DB. 

## <a name="unsupported-features"></a>Niet-ondersteunde functies

* ***[Gremlin Bytecode](http://tinkerpop.apache.org/docs/current/tutorials/gremlin-language-variants/)*** is een programmeertaal-agnostische specificatie voor het doorkruisen van grafieken. Cosmos DB Graph ondersteunt dit nog niet. Gebruik ```GremlinClient.SubmitAsync()``` en laat door gaan als een teken reeks.

* ***```property(set, 'xyz', 1)```*** de kardinaliteit instellen wordt vandaag niet ondersteund. Gebruik ```property(list, 'xyz', 1)``` in plaats daarvan.

* ***```match()```*** Hiermee kunt u query's uitvoeren op grafieken met declaratieve patroon vergelijking. Deze mogelijkheid is niet beschikbaar.

* ***Objecten als eigenschappen*** op hoek punten of randen worden niet ondersteund. Eigenschappen kunnen alleen primitieve typen of matrices zijn.

* ***Sorteren op matrix eigenschappen*** ```.order().by(<array property>)``` wordt niet ondersteund. Alleen sorteren op primitieve typen wordt ondersteund.

* ***Niet-PRIMITIEVE JSON-typen*** worden niet ondersteund. Gebruik ```string```, ```number```of ```true``` typen./ ```false``` ```null```waarden worden niet ondersteund. 

* ***GraphSONv3*** serializer is momenteel niet beschikbaar.

* ***Trans acties*** worden niet ondersteund vanwege gedistribueerde aard van het systeem.  Configureer het juiste consistentie model op het Gremlin-account om uw eigen schrijf bewerkingen te lezen en gebruik optimistische gelijktijdigheid om conflicterende schrijf bewerkingen op te lossen.

## <a name="next-steps"></a>Volgende stappen
* Ga naar [Cosmos DB gebruikers spraak](https://feedback.azure.com/forums/263030-azure-cosmos-db) pagina om feedback te delen en de focus te geven aan functies die voor u belang rijk zijn.
