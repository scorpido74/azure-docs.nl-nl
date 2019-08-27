---
title: Node.js-voorbeelden voor Azure Cosmos DB
description: Zoek Node.js-voorbeelden op GitHub voor veelvoorkomende taken in Azure Cosmos DB, zoals CRUD-bewerkingen.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 08/23/2019
ms.author: dech
ms.openlocfilehash: d5094fb01c9640704f02c5128c64edfd31d77f20
ms.sourcegitcommit: 3f78a6ffee0b83788d554959db7efc5d00130376
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/26/2019
ms.locfileid: "70018813"
---
# <a name="azure-cosmos-db-nodejs-examples"></a>Node.js-voorbeelden voor Azure Cosmos DB
> [!div class="op_single_selector"]
> * [.NET-voorbeelden](sql-api-dotnet-samples.md)
> * [Java-voorbeelden](sql-api-java-samples.md)
> * [Async Java-voorbeelden](sql-api-async-java-samples.md)
> * [Node.js-voorbeelden](sql-api-nodejs-samples.md)
> * [Python-voorbeelden](sql-api-python-samples.md)
> * [Galerie met codevoorbeelden voor Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Voorbeeldoplossingen waarmee CRUD-bewerkingen en andere veelvoorkomende bewerkingen worden uitgevoerd in Azure Cosmos DB-resources worden uitgevoerd, zijn opgenomen in de GitHub-opslagplaats [azure-cosmos-js](https://github.com/Azure/azure-cosmos-js/tree/master/samples). Dit artikel bevat:

* Koppelingen naar de taken in elk van de Node.js-voorbeeldprojectbestanden.
* Koppelingen naar het bijbehorende API-referentiemateriaal.

**Vereisten**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- U kunt [voordelen als Visual Studio-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): Via uw Visual Studio-abonnement ontvangt u elke maand een tegoed dat u voor betaalde Azure-services kunt gebruiken.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

U hebt ook de [JavaScript SDK](sql-api-sdk-node.md) nodig.
   
   > [!NOTE]
   > Elk voorbeeld staat op zichzelf. Het stelt zichzelf in en aan het einde worden de gegevens automatisch opgeschoond. Als zodanig wordt in de voorbeelden [Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest) meerdere keren aangeroepen. Telkens wanneer dit wordt gedaan, wordt uw abonnement gefactureerd voor één uur gebruik per prestatielaag van de container die wordt gemaakt.
   > 
   > 

## <a name="database-examples"></a>Voorbeelden voor databases

In het [DatabaseManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts) -bestand ziet u hoe de ruwe bewerkingen op de Data Base worden uitgevoerd. Zie [werken met data bases, containers en](databases-containers-items.md) artikel concepten voor meer informatie over de Azure Cosmos-data bases voordat u de volgende voor beelden uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| [Database maken indien deze niet bestaat](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L12-L14) |[Databases.createIfNotExists](/javascript/api/@azure/cosmos/databases?view=azure-node-latest#createifnotexists-databaserequest--requestoptions-) |
| [Databases voor een account weergeven](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L16-L18) |[Databases.readAll](/javascript/api/@azure/cosmos/databases?view=azure-node-latest#readall-feedoptions-) |
| [Een database lezen op id](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L20-L29) |[Database.read](/javascript/api/@azure/cosmos/database?view=azure-node-latest#read-requestoptions-) |
| [Een database verwijderen](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L31-L32) |[Database.delete](/javascript/api/@azure/cosmos/database?view=azure-node-latest#delete-requestoptions-) |

## <a name="container-examples"></a>Voorbeelden van containers

Het [ContainerManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts) -bestand laat zien hoe u de ruwe bewerkingen op de container uitvoert. Zie [werken met data bases, containers en](databases-containers-items.md) artikel concepten voor meer informatie over de Azure Cosmos-verzamelingen voordat u de volgende voor beelden uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| [Container maken indien deze niet bestaat](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L14-L15) |[Containers.createIfNotExists](/javascript/api/@azure/cosmos/containers?view=azure-node-latest#createifnotexists-containerrequest--requestoptions-) |
| [Containers voor een account weergeven](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L17-L21) |[Containers.readAll](/javascript/api/@azure/cosmos/containers?view=azure-node-latest#readall-feedoptions-) |
| [Een container definitie lezen](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L23-L26) |[Container.read](/javascript/api/@azure/cosmos/container?view=azure-node-latest#read-requestoptions-) |
| [Container verwijderen](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L28-L30) |[Container.delete](/javascript/api/@azure/cosmos/container?view=azure-node-latest#delete-requestoptions-) |

## <a name="item-examples"></a>Voorbeelden van items

In het [ItemManagement](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ItemManagement) -bestand ziet u hoe de ruwe bewerkingen op het item worden uitgevoerd. Zie [werken met data bases, containers en](databases-containers-items.md) artikel concepten voor meer informatie over de Azure Cosmos-documenten voordat u de volgende voor beelden uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| [Items maken](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L18-L21) |[Items.create](/javascript/api/@azure/cosmos/items?view=azure-node-latest#create-t--requestoptions-) |
| [Alle items in een container lezen](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L23-L28) |[Items.readAll](/javascript/api/@azure/cosmos/items?view=azure-node-latest#readall-feedoptions-) |
| [Een item op id lezen](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L30-L33) |[Item.read](/javascript/api/@azure/cosmos/item?view=azure-node-latest#read-requestoptions-) |
| [Item alleen lezen als het is gewijzigd](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L45-L56) |[Item.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [Een query uitvoeren voor documenten](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79) |[Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Item vervangen](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L81-L96) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest) |
| [Item vervangen door voorwaardelijke ETag-controle](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L98-L135) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [Item verwijderen](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L137-L140) |[Item.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest) |

## <a name="indexing-examples"></a>Voorbeelden van indexen

Het [IndexManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts) -bestand bevat informatie over het beheren van indexeren. Zie [Indexing policies](index-policy.md), Indexing [types](index-types.md)en Indexing [Path](index-paths.md) conceptuele articles (Engelstalig) voor meer informatie over het indexeren in azure Cosmos DB voordat u de volgende voor beelden uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| [Een specifiek item hand matig indexeren](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L52-L75) |[RequestOptions.indexingDirective: 'include'](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [Hand matig een specifiek item uit de index uitsluiten](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L17-L29) |[RequestOptions.indexingDirective: 'exclude'](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [Een pad uitsluiten van de index](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L142-L167) |[IndexingPolicy.ExcludedPath](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest#excludedpaths) |
| [Een bereikindex maken op een tekenreekspad](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L87-L112) |[IndexKind.Range](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexkind?view=azure-node-latest), [IndexingPolicy](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest), [Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Een container maken met standaard indexPolicy en vervolgens online bijwerken](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L13-L15) |[Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest)

## <a name="server-side-programming-examples"></a>Voorbeelden van programmering op de server

In het bestand [app.js](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/app.js) van het project [ServerSideScripts](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ServerSideScripts) ziet u hoe de volgende taken worden uitgevoerd. Zie voor meer informatie over het Program meren van de server in Azure Cosmos DB voordat u de volgende voor beelden uitvoert, [opgeslagen procedures, triggers en](stored-procedures-triggers-udfs.md) conceptueel artikel over door de gebruiker gedefinieerde functies. 

| Taak | API-verwijzing |
| --- | --- |
| [Een opgeslagen procedure maken](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/upsert.js) |[StoredProcedures.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedures?view=azure-node-latest) |
| [Een opgeslagen procedure uitvoeren](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) |[StoredProcedure.execute](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedure?view=azure-node-latest) |

Voor meer informatie over programmeren aan de serverzijde ziet u [Programmeren aan de serverzijde van Azure Cosmos DB: opgeslagen procedures, databasetriggers en UDF's](stored-procedures-triggers-udfs.md).

