---
title: Azure Resource Manager-sjablonen voor Azure Cosmos DB
description: Gebruik Azure Resource Manager-sjablonen om Azure Cosmos DB te maken en te configureren.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mjbrown
ms.openlocfilehash: 1c4f1a1920d98052231eaa3a7d5c0454441c88ee
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81390881"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Resource Manager-sjablonen voor Azure Cosmos DB

De volgende tabellen bevatten koppelingen naar Azure Resource Manager-sjablonen voor Azure Cosmos DB:

## <a name="sql-core-api"></a>SQL (Core) API

|**Sjabloon**|**Beschrijving**|
|---|---|
|[Een Azure Cosmos-account, database, container maken](manage-sql-with-resource-manager.md#create-resource) | Deze sjabloon maakt een SQL (Core) API-account in twee regio's met twee containers met gedeelde databasedoorvoer en een container met speciale doorvoer. Doorvoer kan worden bijgewerkt door de sjabloon opnieuw in te dienen met een bijgewerkte waarde van de doorvoereigenschap. |
|[Een Azure Cosmos-account, database en container maken met een opgeslagen procedure, trigger en UDF](manage-sql-with-resource-manager.md#create-sproc) | Met deze sjabloon wordt een SQL (Core) API-account gemaakt in twee regio's met een opgeslagen procedure, trigger en UDF voor een container. |
|[Een privéeindpunt maken voor een bestaand Azure Cosmos-account](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Met deze sjabloon wordt een privéeindpunt gemaakt voor een bestaand Azure Cosmos SQL API-account in een bestaand virtueel netwerk. |

## <a name="mongodb-api"></a>MongoDB-API

|**Sjabloon**|**Beschrijving**|
|---| ---|
|[Een Azure Cosmos-account, database, verzameling maken](manage-mongodb-with-resource-manager.md#create-resource) | Met deze sjabloon wordt een account gemaakt met Azure Cosmos DB API voor MongoDB in twee regio's waarvoor multi-master is ingeschakeld. Het Azure Cosmos-account bevat twee containers die de doorvoer op databaseniveau delen. |

## <a name="cassandra-api"></a>Cassandra-API

|**Sjabloon**|**Beschrijving**|
|---| ---|
|[Een Azure Cosmos-account, keyspace, tabel maken](manage-cassandra-with-resource-manager.md#create-resource) | Met deze sjabloon wordt een Cassandra API-account gemaakt in twee regio's met multi-master ingeschakeld. Het Azure Cosmos-account heeft twee tabellen die doorvoer op keyspace-niveau delen. |

## <a name="gremlin-api"></a>Gremlin-API

|**Sjabloon**|**Beschrijving**|
|---| ---|
|[Een Azure Cosmos-account, database, grafiek maken](manage-gremlin-with-resource-manager.md#create-resource) | Met deze sjabloon wordt een Gremlin API-account gemaakt in twee regio's met multi-master ingeschakeld. Het Azure Cosmos-account heeft twee grafieken die de doorvoer op databaseniveau delen. |

## <a name="table-api"></a>Tabel-API

|**Sjabloon**|**Beschrijving**|
|---| ---|
|[Een Azure Cosmos-account maken, tabel](manage-table-with-resource-manager.md#create-resource) | Met deze sjabloon wordt een Api-account voor tabel gemaakt in twee regio's met multi-master ingeschakeld. Het Azure Cosmos-account heeft één tabel. |

> [!TIP]
> Als u gedeelde doorvoer wilt inschakelen wanneer u Tabel-API gebruikt, schakelt u doorvoer op accountniveau in in de Azure Portal.

Zie [Azure Resource Manager-referentie voor azure cosmos DB-pagina](/azure/templates/microsoft.documentdb/allversions) voor de referentiedocumentatie.
