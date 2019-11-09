---
title: Azure Resource Manager sjablonen voor Azure Cosmos DB
description: Gebruik Azure Resource Manager sjablonen om Azure Cosmos DB te maken en te configureren.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/07/2019
ms.author: thvankra
ms.openlocfilehash: 7c508d736c7c04cc6df2b42e0a90a2906052adbc
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/08/2019
ms.locfileid: "73835522"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Resource Manager sjablonen voor Azure Cosmos DB

De volgende tabellen bevatten koppelingen naar Azure Resource Manager sjablonen voor Azure Cosmos DB:

## <a name="sql-core-api"></a>SQL (Core) API

|**Sjabloon**|**Beschrijving**|
|---|---|
|[Een Azure Cosmos-account,-Data Base,-container maken](manage-sql-with-resource-manager.md#create-resource) | Met deze sjabloon maakt u een SQL-API-account (core) in twee regio's met twee containers met gezamenlijke data base-door Voer en een container met een speciale door voer. De door Voer kan worden bijgewerkt door de sjabloon opnieuw in te dienen met een bijgewerkte waarde voor de doorvoer eigenschap. |
|[Een Azure Cosmos-account,-data base en-container maken met een opgeslagen procedure, trigger en UDF](manage-sql-with-resource-manager.md#create-sproc) | Met deze sjabloon maakt u een SQL-API-account (core) in twee regio's met een opgeslagen procedure, trigger en UDF voor een container. |

## <a name="mongodb-api"></a>MongoDB-API

|**Sjabloon**|**Beschrijving**|
|---| ---|
|[Een Azure Cosmos-account,-Data Base,-verzameling maken](manage-mongodb-with-resource-manager.md#create-resource) | Met deze sjabloon maakt u een account met Azure Cosmos DB-API voor MongoDB in twee regio's waarvoor multi-master is ingeschakeld. Het Azure Cosmos-account heeft twee containers die door Voer op database niveau delen. |
|[Door Voer (RU/s) bijwerken voor een Data Base](manage-mongodb-with-resource-manager.md#database-ru-update) | Met deze sjabloon wordt de door Voer voor een data base in een MongoDB-API-account bijgewerkt. |
|[De door Voer (RU/s) voor een verzameling bijwerken](manage-mongodb-with-resource-manager.md#collection-ru-update) | Met deze sjabloon wordt de door Voer voor een container in een MongoDB-API-account bijgewerkt. |

## <a name="cassandra-api"></a>Cassandra-API

|**Sjabloon**|**Beschrijving**|
|---| ---|
|[Een Azure Cosmos-account maken, een spatie, tabel](manage-cassandra-with-resource-manager.md#create-resource) | Met deze sjabloon maakt u een Cassandra-API-account in twee regio's waarvoor multi-master is ingeschakeld. Het Azure Cosmos-account heeft twee tabellen die door Voer op het niveau van de bestands ruimte worden gedeeld. |

## <a name="gremlin-api"></a>Gremlin-API

|**Sjabloon**|**Beschrijving**|
|---| ---|
|[Een Azure Cosmos-account,-Data Base,-grafiek maken](manage-gremlin-with-resource-manager.md#create-resource) | Met deze sjabloon wordt een Gremlin-API-account gemaakt in twee regio's waarvoor multi-master is ingeschakeld. Het Azure Cosmos-account heeft twee grafieken die door Voer op database niveau delen. |
|[Door Voer (RU/s) bijwerken voor een Data Base](manage-gremlin-with-resource-manager.md#database-ru-update) | Met deze sjabloon wordt de door Voer voor een data base in een Gremlin-API-account bijgewerkt. |
|[De door Voer (RU/s) voor een grafiek bijwerken](manage-gremlin-with-resource-manager.md#graph-ru-update) | Met deze sjabloon wordt de door Voer voor een grafiek in een Gremlin-API-account bijgewerkt. |

## <a name="table-api"></a>Tabel-API

|**Sjabloon**|**Beschrijving**|
|---| ---|
|[Een Azure Cosmos-account maken, tabel](manage-table-with-resource-manager.md#create-resource) | Met deze sjabloon maakt u een Table-API-account in twee regio's waarvoor multi-master is ingeschakeld. Het Azure Cosmos-account heeft één tabel. |
|[De door Voer (RU/s) voor een tabel bijwerken](manage-table-with-resource-manager.md#table-ru-update) | Met deze sjabloon wordt de door Voer voor een tabel in een Table-API-account bijgewerkt. |

> [!TIP]
> Als u gedeelde door voer wilt inschakelen wanneer u Table-API gebruikt, schakelt u door Voer op account niveau in azure Portal.

Zie [arm-Naslag informatie voor Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) -pagina voor de referentie documentatie.
