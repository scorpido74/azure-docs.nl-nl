---
title: Azure Resource Manager-sjablonen voor Azure Cosmos DB
description: Gebruik Azure Resource Manager sjablonen om Azure Cosmos DB te maken en te configureren.
author: markjbrown
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/08/2020
ms.author: mjbrown
ms.openlocfilehash: a98aee61c3a330344b5b7fa0a5f7f53a65e5ecdb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "83586147"
---
# <a name="azure-resource-manager-templates-for-azure-cosmos-db"></a>Azure Resource Manager-sjablonen voor Azure Cosmos DB

De volgende tabellen bevatten koppelingen naar Azure Resource Manager sjablonen voor Azure Cosmos DB:

## <a name="core-sql-api"></a>Core (SQL) API

|**Sjabloon**|**Beschrijving**|
|---|---|
|[Een Azure Cosmos-account,-Data Base,-container maken met de door Voer van automatisch schalen](manage-sql-with-resource-manager.md#create-autoscale) | Met deze sjabloon maakt u een core (SQL) API-account in twee regio's, een Data Base en container met de door Voer van automatisch schalen. |
|[Een Azure Cosmos-account, Data Base, container met een analytische opslag maken](manage-sql-with-resource-manager.md#create-analytical-store) | Met deze sjabloon maakt u een core-API-account (SQL) in één regio met een container die is geconfigureerd met de optie voor analyse-TTL ingeschakeld en selecteert u hand matig of automatisch schalen door voer. |
|[Een Azure Cosmos-account, Data Base, container met de standaard doorvoer (hand matig) maken](manage-sql-with-resource-manager.md#create-manual) | Met deze sjabloon maakt u een core (SQL) API-account in twee regio's, een Data Base en container met een standaard doorvoer. |
|[Een Azure Cosmos-account,-data base en-container maken met een opgeslagen procedure, trigger en UDF](manage-sql-with-resource-manager.md#create-sproc) | Met deze sjabloon maakt u een core-API-account (SQL) in twee regio's met een opgeslagen procedure, trigger en UDF voor een container. |
|[Een persoonlijk eind punt maken voor een bestaand Azure Cosmos-account](how-to-configure-private-endpoints.md#create-a-private-endpoint-by-using-a-resource-manager-template) |  Met deze sjabloon maakt u een persoonlijk eind punt voor een bestaand Azure Cosmos core-API-account in een bestaand virtueel netwerk. |
|[Een Azure Cosmos-account met een gratis laag maken](manage-sql-with-resource-manager.md#free-tier) |  Met deze sjabloon wordt een Azure Cosmos DB core-API-account gemaakt op de laag gratis. |

## <a name="mongodb-api"></a>MongoDB-API

|**Sjabloon**|**Beschrijving**|
|---| ---|
|[Een Azure Cosmos-account,-Data Base,-verzameling maken met de door Voer van automatisch schalen](manage-mongodb-with-resource-manager.md#create-autoscale) | Met deze sjabloon maakt u een account met Azure Cosmos DB-API voor MongoDB in twee regio's met twee containers die de door Voer van automatisch schalen op database niveau delen. |
|[Een Azure Cosmos-account,-Data Base,-verzameling maken met de standaard doorvoer (hand matig)](manage-mongodb-with-resource-manager.md#create-manual) | Met deze sjabloon maakt u een account met behulp van Azure Cosmos DB-API voor MongoDB in twee regio's met twee containers die de standaard doorvoer op database niveau delen. |

## <a name="cassandra-api"></a>Cassandra-API

|**Sjabloon**|**Beschrijving**|
|---| ---|
|[Een Azure Cosmos-account maken met behulp van de functie voor automatisch schalen](manage-cassandra-with-resource-manager.md#create-autoscale) | Met deze sjabloon maakt u een Cassandra-API account in twee regio's met een spatie en een tabel met de door Voer van automatisch schalen. |
|[Een Azure Cosmos-account maken met behulp van de standaard (hand matig) door Voer](manage-cassandra-with-resource-manager.md#create-manual) | Met deze sjabloon maakt u een Cassandra-API account in twee regio's met een spatie en een tabel met hand matige door voer. |

## <a name="gremlin-api"></a>Gremlin-API

|**Sjabloon**|**Beschrijving**|
|---| ---|
|[Een Azure Cosmos-account,-Data Base,-grafiek maken met de door Voer van automatisch schalen](manage-gremlin-with-resource-manager.md#create-autoscale) | Met deze sjabloon maakt u een Gremlin-API-account in twee regio's met een Data Base en grafiek met de door Voer van automatisch schalen. |
|[Een Azure Cosmos-account, Data Base, grafiek met standaard (hand matig) door Voer maken](manage-gremlin-with-resource-manager.md#create-manual) | Met deze sjabloon maakt u een Gremlin-API-account in twee regio's met een Data Base en grafiek met een standaard doorvoer. |

## <a name="table-api"></a>Tabel-API

|**Sjabloon**|**Beschrijving**|
|---| ---|
|[Een Azure Cosmos-account maken, tabel met door Voer automatisch schalen](manage-table-with-resource-manager.md#create-autoscale) | Met deze sjabloon maakt u een Table-API account in twee regio's en één tabel met de door Voer van automatisch schalen. |
|[Een Azure Cosmos-account maken, tabel met standaard (hand matig) door Voer](manage-table-with-resource-manager.md#create-manual) | Met deze sjabloon wordt een Table-API-account gemaakt in twee regio's en één tabel met een standaard doorvoer. |

Zie [Azure Resource Manager referentie voor Azure Cosmos DB](/azure/templates/microsoft.documentdb/allversions) pagina voor de referentie documentatie.
