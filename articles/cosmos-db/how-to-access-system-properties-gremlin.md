---
title: Toegang tot systeemdocumenteigenschappen via Azure Cosmos DB Graph
description: Meer informatie over het lezen en schrijven van cosmos DB-systeemdocumenteigenschappen via Gremlin API
ms.service: cosmos-db
ms.subservice: cosmosdb-graph
ms.topic: conceptual
ms.date: 09/10/2019
author: luisbosquez
ms.author: lbosq
ms.openlocfilehash: 4ed7e67ae0ef027b260d0e0f0407e4e05ed5a8f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898308"
---
# <a name="system-document-properties"></a>Eigenschappen van systeemdocument

Azure Cosmos DB heeft ```_ts``` [systeemeigenschappen](https://docs.microsoft.com/rest/api/cosmos-db/databases) ```_rid```zoals ```_etag``` , ```_self```, ```_attachments```, en op elk document. Daarnaast voegt de Gremlin-engine de ```inVPartition```- en ```outVPartition```-eigenschappen toe aan randen. Standaard zijn deze eigenschappen beschikbaar voor traversal. Het is echter mogelijk om specifieke eigenschappen, of allemaal, op te nemen in Gremlin traversal.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_ts').create())
```

## <a name="e-tag"></a>E-Tag

Deze eigenschap wordt gebruikt voor het beheer van optimistische gelijktijdigheid. Als de toepassing de werking moet opsplitsen in een paar afzonderlijke traversals, kan het de eigenschap eTag gebruiken om gegevensverlies in gelijktijdige schrijfbewerkingen te voorkomen.

```
g.withStrategies(ProjectionStrategy.build().IncludeSystemProperties('_etag').create()).V('1').has('_etag', '"00000100-0000-0800-0000-5d03edac0000"').property('test', '1')
```

## <a name="time-to-live-ttl"></a>TTL (time-to-live)

Als de verzameling is ingeschakeld ```ttl``` en documenten op de eigenschap zijn ingesteld, is deze eigenschap beschikbaar in Gremlin traversal als een gewone vertex- of edge-eigenschap. ```ProjectionStrategy```is niet nodig om time-to-live blootstelling aan onroerend goed mogelijk te maken.

Een hoekpunt dat is gemaakt met onderstaande doorkruising, wordt automatisch na **123 seconden** verwijderd.

```
g.addV('vertex-one').property('ttl', 123)
```

## <a name="next-steps"></a>Volgende stappen
* [Optimistische gelijktijdigheid van Cosmos DB](faq.md#how-does-the-sql-api-provide-concurrency)
* [Time to Live (TTL)](time-to-live.md) in Azure Cosmos DB
