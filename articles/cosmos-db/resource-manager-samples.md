---
title: Azure Resource Manager sjablonen voor Azure Cosmos DB
description: Gebruik Azure Resource Manager sjablonen om Azure Cosmos DB te maken en te configureren.
author: TheovanKraay
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/08/2019
ms.author: thvankra
ms.openlocfilehash: 7b08ca98f25b079d831033b9393effd4ee4b65e3
ms.sourcegitcommit: 39da2d9675c3a2ac54ddc164da4568cf341ddecf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2019
ms.locfileid: "73961858"
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
