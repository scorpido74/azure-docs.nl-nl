---
title: Limieten van Azure Cosmos DB Gremlin
description: Referentiedocumentatie voor runtime-beperkingen van Graph-engine
author: LuisBosquez
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: reference
ms.date: 10/04/2019
ms.author: lbosq
ms.openlocfilehash: 76ad787990c355d29613c05ca9fce31885a2eccc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "72029845"
---
# <a name="azure-cosmos-db-gremlin-limits"></a>Limieten van Azure Cosmos DB Gremlin
In dit artikel wordt gesproken over de grenzen van azure cosmos DB Gremlin-engine en wordt uitgelegd hoe deze van invloed kunnen zijn op klanttraversals.

Cosmos DB Gremlin is gebouwd op de top van Cosmos DB infrastructuur. Hierdoor gelden nog steeds alle limieten die zijn uitgelegd in [Azure Cosmos DB-servicelimieten.](https://docs.microsoft.com/azure/cosmos-db/concepts-limits) 

## <a name="limits"></a>Limieten

Wanneer gremlin limiet is bereikt, wordt traversal geannuleerd met een **x-ms-status-code** van 429 die een beperkingsfout aangeeft. Zie [Gremlin-serverantwoordkoppen](gremlin-limits.md) voor meer informatie.

**Resource**    | **Standaardlimiet** | **Uitleg**
--- | --- | ---
*Scriptlengte* | **64 kB** | Maximale lengte van een Gremlin-doorkruisingsscript per aanvraag.
*Operatordiepte* | **400** |  Totaal aantal unieke stappen in een doorkruising. Heeft bijvoorbeeld ```g.V().out()``` een operatortelling van 2: V() ```g.V('label').repeat(out()).times(100)``` en out(), heeft operatordiepte van 3: ```.times(100)``` V(), ```.repeat()``` repeat() en out() omdat het een parameter is voor de operator.
*Mate van parallelle uitvoering* | **32** | Maximum aantal opslagpartities dat in één aanvraag naar de opslaglaag wordt bevraagd. Grafieken met honderden partities worden beïnvloed door deze limiet.
*Herhaallimiet* | **32** | Maximum aantal iteraties dat een ```.repeat()```-operator kan uitvoeren. Elke iteratie ```.repeat()``` van stap in de meeste gevallen loopt breedte-eerste traversal, wat betekent dat elke traversal is beperkt tot maximaal 32 hop tussen vertices.
*Traversal time-out* | **30 seconden** | Traversal zal worden geannuleerd wanneer het deze tijd overschrijdt. Cosmos DB Graph is een OLTP-database waar de meeste doorkruisingen binnen enkele milliseconden worden uitgevoerd. Als u OLAP-query's wilt uitvoeren op Cosmos DB Graph, gebruikt u [Apache Spark](https://azure.microsoft.com/services/cosmos-db/) met Graph [Data Frames](https://spark.apache.org/docs/latest/sql-programming-guide.html#datasets-and-dataframes) en Cosmos DB [Spark Connector](https://github.com/Azure/azure-cosmosdb-spark).
*Time-out van niet-actieve verbinding* | **1 uur** | De tijd seintijd houdt de Gremlin-service niet-actieve websocketverbindingen open. TCP-keep-alive-pakketten of HTTP-keep-alive-verzoeken verlengen de levensduur van de verbinding niet buiten deze limiet. Cosmos DB Graph-engine beschouwt websocketverbindingen als idle als er geen actieve Gremlin-aanvragen op worden uitgevoerd.
*Resourcetokens per uur* | **100** | Het aantal unieke resourcetokens dat door Gremlin-clients wordt gebruikt om verbinding te maken met het Gremlin-account in een regio. Wanneer de toepassing de unieke `"Exceeded allowed resource token limit of 100 that can be used concurrently"` tokenlimiet per uur overschrijdt, wordt deze geretourneerd op het volgende verificatieverzoek.

## <a name="next-steps"></a>Volgende stappen
* [Azure Cosmos DB Gremlin-antwoordkoppen](gremlin-headers.md) 
* [Azure Cosmos DB Resource Tokens met Gremlin](how-to-use-resource-tokens-gremlin.md)
