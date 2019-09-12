---
title: Toegang tot systeem document eigenschappen via Azure Cosmos DB Graph
description: Meer informatie over het lezen en schrijven van Cosmos DB eigenschappen van het systeem document via Gremlin API
author: olignat
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/10/2019
ms.author: olignat
ms.openlocfilehash: 4354d37a32bde006a9bee70c39df1fee9b269365
ms.sourcegitcommit: d70c74e11fa95f70077620b4613bb35d9bf78484
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70910661"
---
# <a name="system-document-properties"></a>Eigenschappen van systeem document

Azure Cosmos DB heeft [systeem eigenschappen](https://docs.microsoft.com/rest/api/cosmos-db/databases) ```_ts```, zoals, ```_self``` ```_attachments``` ```_rid```,, en ```_etag``` op elk document. Daarnaast voegt de Gremlin-engine de ```inVPartition```- en ```outVPartition```-eigenschappen toe aan randen. Deze eigenschappen zijn standaard beschikbaar voor navigatie. Het is echter mogelijk om specifieke eigenschappen toe te voegen, of al deze, in Gremlin traversal.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>E-tag

Deze eigenschap wordt gebruikt voor het beheer van optimistische gelijktijdigheid. Als de toepassing de bewerking moet afsplitsen in een paar afzonderlijke navigatie, kan de eigenschap eTag worden gebruikt om gegevens verlies te voor komen in gelijktijdige schrijf bewerkingen.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>TTL (time-to-live)

Als voor de verzameling het verloop van het document ```ttl``` is ingeschakeld en op documenten een eigenschap is ingesteld, is deze eigenschap beschikbaar in Gremlin-traversal als een normale vertex-of rand eigenschap. ```ProjectionStrategy```het is niet nodig om de bloot stelling van time-to-Live-eigenschappen in te scha kelen.

Een hoekpunt dat is gemaakt met onderstaande doorkruising, wordt automatisch na **123 seconden** verwijderd.

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>Volgende stappen
* [Optimistische gelijktijdigheid van Cosmos DB](faq.md#how-does-the-sql-api-provide-concurrency)
* [Time to Live (TTL)](time-to-live.md) in azure Cosmos db
