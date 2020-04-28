---
title: Azure Resource Manager-sjablonen voor Azure Cosmos DB
description: Gebruik Azure Resource Manager sjablonen om Azure Cosmos DB te maken en te configureren.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 04/27/2020
ms.author: mjbrown
ms.openlocfilehash: 14c3438c5d6e7bed110f9e0dbfcd04655c3e9121
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82193255"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Resource Manager-sjablonen voor Azure Cosmos DB

De volgende tabellen bevatten koppelingen naar Azure Resource Manager sjablonen voor Azure Cosmos DB:

## <a name="sql-core-api"></a>SQL (Core) API

|**Sjabloon**|**Beschrijving**|
|---|---|
|[Een Azure Cosmos-account,-Data Base,-container maken](manage-sql-with-resource-manager.md#create-resource) | Met deze sjabloon maakt u een SQL-API-account (core) in twee regio's met twee containers met gezamenlijke data base-door Voer en een container met een speciale door voer. De door Voer kan worden bijgewerkt door de sjabloon opnieuw in te dienen met een bijgewerkte waarde voor de doorvoer eigenschap. |
|[Een Azure Cosmos-account,-data base en-container maken met een opgeslagen procedure, trigger en UDF](manage-sql-with-resource-manager.md#create-sproc) | Met deze sjabloon maakt u een SQL-API-account (core) in twee regio's met een opgeslagen procedure, trigger en UDF voor een container. |
|[Een persoonlijk eind punt maken voor een bestaand Azure Cosmos-account](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Met deze sjabloon maakt u een persoonlijk eind punt voor een bestaand Azure Cosmos SQL API-account in een bestaand virtueel netwerk. |
|[Een Azure Cosmos-account met een gratis laag maken](manage-sql-with-resource-manager.md#free-tier) |  Met deze sjabloon maakt u een Azure Cosmos DB-account op een gratis laag. |

## <a name="mongodb-api"></a>MongoDB-API

|**Sjabloon**|**Beschrijving**|
|---| ---|
|[Een Azure Cosmos-account,-Data Base,-verzameling maken](manage-mongodb-with-resource-manager.md#create-resource) | Met deze sjabloon maakt u een account met Azure Cosmos DB-API voor MongoDB in twee regio's waarvoor multi-master is ingeschakeld. Het Azure Cosmos-account heeft twee containers die door Voer op database niveau delen. |

## <a name="cassandra-api"></a>Cassandra-API

|**Sjabloon**|**Beschrijving**|
|---| ---|
|[Een Azure Cosmos-account maken, een spatie, tabel](manage-cassandra-with-resource-manager.md#create-resource) | Met deze sjabloon maakt u een Cassandra-API-account in twee regio's waarvoor multi-master is ingeschakeld. Het Azure Cosmos-account heeft twee tabellen die door Voer op het niveau van de bestands ruimte worden gedeeld. |

## <a name="gremlin-api"></a>Gremlin-API

|**Sjabloon**|**Beschrijving**|
|---| ---|
|[Een Azure Cosmos-account,-Data Base,-grafiek maken](manage-gremlin-with-resource-manager.md#create-resource) | Met deze sjabloon wordt een Gremlin-API-account gemaakt in twee regio's waarvoor multi-master is ingeschakeld. Het Azure Cosmos-account heeft twee grafieken die door Voer op database niveau delen. |

## <a name="table-api"></a>Tabel-API

|**Sjabloon**|**Beschrijving**|
|---| ---|
|[Een Azure Cosmos-account maken, tabel](manage-table-with-resource-manager.md#create-resource) | Met deze sjabloon maakt u een Table-API-account in twee regio's waarvoor multi-master is ingeschakeld. Het Azure Cosmos-account heeft één tabel. |

> [!TIP]
> Als u gedeelde door voer wilt inschakelen wanneer u Table-API gebruikt, schakelt u door Voer op account niveau in azure Portal.

Zie [Azure Resource Manager referentie voor Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) pagina voor de referentie documentatie.
