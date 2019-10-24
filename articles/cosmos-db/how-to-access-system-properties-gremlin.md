---
title: Toegang tot systeem document eigenschappen via Azure Cosmos DB Graph
description: Meer informatie over het lezen en schrijven van Cosmos DB eigenschappen van het systeem document via Gremlin API
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: overview
ms.date: 09/10/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: e762674936ab2fbdf198ca67f79acfa545127f02
ms.sourcegitcommit: 8074f482fcd1f61442b3b8101f153adb52cf35c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/22/2019
ms.locfileid: "72755060"
---
# <a name="system-document-properties"></a>Eigenschappen van systeem document

Azure Cosmos DB heeft [systeem eigenschappen](https://docs.microsoft.com/rest/api/cosmos-db/databases) , zoals ```_ts```, ```_self```, ```_attachments```, ```_rid``` en ```_etag``` op elk document. Daarnaast voegt de Gremlin-engine de ```inVPartition```- en ```outVPartition```-eigenschappen toe aan randen. Deze eigenschappen zijn standaard beschikbaar voor navigatie. Het is echter mogelijk om specifieke eigenschappen toe te voegen, of al deze, in Gremlin traversal.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>E-tag

Deze eigenschap wordt gebruikt voor het beheer van optimistische gelijktijdigheid. Als de toepassing de bewerking moet afsplitsen in een paar afzonderlijke navigatie, kan de eigenschap eTag worden gebruikt om gegevens verlies te voor komen in gelijktijdige schrijf bewerkingen.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>TTL (time-to-live)

Als voor de verzameling het verloop van het document is ingeschakeld en op documenten ```ttl``` eigenschap is ingesteld, is deze eigenschap beschikbaar in Gremlin-traversal als een normale vertex-of rand eigenschap. ```ProjectionStrategy``` is niet nodig om de bloot stelling van time-to-Live-eigenschappen mogelijk te maken.

Een hoekpunt dat is gemaakt met onderstaande doorkruising, wordt automatisch na **123 seconden** verwijderd.

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>Volgende stappen
* [Optimistische gelijktijdigheid van Cosmos DB](faq.md#how-does-the-sql-api-provide-concurrency)
* [Time to Live (TTL)](time-to-live.md) in azure Cosmos db
