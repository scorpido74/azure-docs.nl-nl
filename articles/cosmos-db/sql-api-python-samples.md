---
title: Voorbeelden van SQL API Python voor Azure Cosmos DB
description: Vind Python-voorbeelden op GitHub voor veelvoorkomende taken in Azure Cosmos DB, zoals CRUD-bewerkingen.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: python
ms.topic: sample
ms.date: 05/20/2020
ms.author: sngun
ms.custom: tracking-python
ms.openlocfilehash: c52651e15f648bede419501f516392acbc266e8a
ms.sourcegitcommit: 964af22b530263bb17fff94fd859321d37745d13
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/09/2020
ms.locfileid: "84558011"
---
# <a name="azure-cosmos-db-python-examples"></a>Python-voorbeelden voor Azure Cosmos DB

> [!div class="op_single_selector"]
> * [.NET V2 SDK-voorbeelden](sql-api-dotnet-samples.md)
> * [.NET V3 SDK-voorbeelden](sql-api-dotnet-v3sdk-samples.md)
> * [Java V4 SDK-voorbeelden](sql-api-java-sdk-samples.md)
> * [Node.js-voorbeelden](sql-api-nodejs-samples.md)
> * [Python-voorbeelden](sql-api-python-samples.md)
> * [Galerie met codevoorbeelden voor Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)

Voorbeeldoplossingen waarmee CRUD-bewerkingen en andere veelvoorkomende bewerkingen worden uitgevoerd in Azure Cosmos DB-resources zijn opgenomen in de GitHub-opslagplaats [azure-documentdb-python](https://github.com/Azure/azure-documentdb-python). Dit artikel bevat:

* Koppelingen naar de taken in elk van de Python-voorbeeldprojectbestanden.
* Koppelingen naar het bijbehorende API-referentiemateriaal.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- U kunt [voordelen als Visual Studio-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Via uw Visual Studio-abonnement ontvangt u elke maand een tegoed dat u voor betaalde Azure-services kunt gebruiken.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

U hebt ook de [Python SDK](sql-api-sdk-python.md) nodig.

   > [!NOTE]
   > Elk voorbeeld staat op zichzelf. Het stelt zichzelf in en aan het einde worden de gegevens automatisch opgeschoond. De voorbeelden doen meerdere aanroepen naar `CosmosClient.CreateContainer`. Steeds wanneer dit wordt gedaan, wordt uw abonnement gefactureerd voor het gebruik voor één uur. Zie [Azure Cosmos DB-prijzen](https://azure.microsoft.com/pricing/details/cosmos-db/) voor meer informatie over Azure Cosmos DB-facturering.

## <a name="database-examples"></a>Voorbeelden voor databases

In het Python-voorbeeld [database_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py) ziet u hoe u de volgende taken kunt uitvoeren. Zie het conceptuele artikel [Werken met databases, containers en items](databases-containers-items.md) voor meer informatie over de Azure Cosmos-databases voordat u de volgende voorbeelden uitvoert.

| Taak | API-verwijzing |
| --- | --- |
| [Een database maken](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L48-L56) |CosmosClient.create_database|
| [Een database lezen op id](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L59-L67) |CosmosClient.get_database_client|
| [Een query uitvoeren op de databases](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L32-L67) |CosmosClient.query_databases|
| [Databases voor een account weergeven](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L70-L81) |CosmosClient.list_databases|
| [Een database verwijderen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/database_management.py#L84-L93) |CosmosClient.delete_database|

## <a name="container-examples"></a>Voorbeelden van containers

In het Python-voorbeeld [container_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py) ziet u hoe u de volgende taken kunt uitvoeren. Zie het conceptuele artikel [Werken met databases, containers en items](databases-containers-items.md) voor meer informatie over de Azure Cosmos-verzamelingen voordat u de volgende voorbeelden uitvoert.

| Taak | API-verwijzing |
| --- | --- |
| [Een query uitvoeren voor een container](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L51-L66) |database.query_containers |
| [Een container maken](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L69-L163) |database.create_container |
| [Een lijst van alle containers in een database weergeven](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L206-L217) |database.list_containers |
| [Een container ophalen op basis van id](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L195-L203) |database.get_container_client |
| [Ingerichte doorvoer van de container beheren](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L166-L192) |container.read_offer, container.replace_throughput|
| [Container verwijderen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/container_management.py#L220-L229) |database.delete_container |

## <a name="item-examples"></a>Voorbeelden van items

In het Python-voorbeeld [item_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py) ziet u hoe u de volgende taken kunt uitvoeren. Zie het conceptuele artikel [Werken met databases, containers en items](databases-containers-items.md) voor meer informatie over de Azure Cosmos-documenten voordat u de volgende voorbeelden uitvoert.

| Taak | API-verwijzing |
| --- | --- |
| [Items in een container maken](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L26-L38) |container.create_item |
| [Een item lezen op basis van id](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L41-L49) |container.read_item |
| [Alle items in een container lezen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L52-L63) |container.read_all_items |
| [Een query uitvoeren op een item op basis van id](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L66-L78) |container.query_items |
| [Item vervangen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L81-L88) |container.replace_items |
| [Een item invoegen of bijwerken](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L91-L98) |container.upsert_item |
| [Item verwijderen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/document_management.py#L101-L106) |container.delete_item |
| [De wijzigingsfeed van items in een container ophalen](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/change_feed_management.py) |container.query_items_change_feed |

## <a name="indexing-examples"></a>Voorbeelden van indexen

In het Python-voorbeeld [index_management.py](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py) ziet u hoe u de volgende taken kunt uitvoeren. Zie de conceptuele artikelen [Indexeringsbeleid](index-policy.md), [Indexeringstypen](index-types.md) en [Indexeringspaden](index-paths.md) voor meer informatie over het indexeren in Azure Cosmos DB voordat u de volgende voorbeelden uitvoert.

| Taak | API-verwijzing |
| --- | --- |
| [Een specifiek item uitsluiten van indexeren](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L145-L201) | documents.IndexingDirective.Exclude|
| [Handmatige indexering gebruiken bij specifieke items die zijn geïndexeerd](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L204-L263) | documents.IndexingDirective.Include |
| [Paden uitsluiten van indexeren](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L266-L336) |Paden definiëren die moeten worden uitgesloten in eigenschap `IndexingPolicy` |
| [Bereikindexen voor tekenreeksen gebruiken](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L401-L485) | Definieer het indexeringsbeleid met bereikindexen voor het gegevenstype Reeks. `'kind': documents.IndexKind.Range`, `'dataType': documents.DataType.String`|
| [Een indextransformatie uitvoeren](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L488-L544) |database.replace_container (het bijgewerkte indexeringsbeleid gebruiken)|
| [Scans gebruiken wanneer alleen hash-index op het pad bestaat](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/cosmos/azure-cosmos/samples/index_management.py#L339-L398) | de `enable_scan_in_query=True` en `enable_cross_partition_query=True` instellen bij het opvragen van de items |
