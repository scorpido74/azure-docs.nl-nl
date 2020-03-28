---
title: Voorbeelden van Node.js voor het beheren van gegevens in de Azure Cosmos-database
description: Zoek Node.js-voorbeelden op GitHub voor veelvoorkomende taken in Azure Cosmos DB, zoals CRUD-bewerkingen.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: sample
ms.date: 08/23/2019
ms.author: dech
ms.openlocfilehash: 4ac3d74cb76a936cc88261a2fedf16764e8ae52f
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "76718290"
---
# <a name="nodejs-examples-to-manage-data-in-azure-cosmos-db"></a>Voorbeelden van Node.js voor het beheren van gegevens in Azure Cosmos DB

> [!div class="op_single_selector"]
> * [Voorbeelden van .NET V2 SDK](sql-api-dotnet-samples.md)
> * [Voorbeelden van .NET V3 SDK](sql-api-dotnet-v3sdk-samples.md)
> * [Java-voorbeelden](sql-api-java-samples.md)
> * [Async Java-voorbeelden](sql-api-async-java-samples.md)
> * [Node.js-voorbeelden](sql-api-nodejs-samples.md)
> * [Voorbeelden van Python](sql-api-python-samples.md)
> * [Galerie met codevoorbeelden voor Azure](https://azure.microsoft.com/resources/samples/?sort=0&service=cosmos-db)
> 
> 

Voorbeeldoplossingen waarmee CRUD-bewerkingen en andere veelvoorkomende bewerkingen worden uitgevoerd in Azure Cosmos DB-resources worden uitgevoerd, zijn opgenomen in de GitHub-opslagplaats [azure-cosmos-js](https://github.com/Azure/azure-cosmos-js/tree/master/samples). Dit artikel bevat:

* Koppelingen naar de taken in elk van de Node.js-voorbeeldprojectbestanden.
* Koppelingen naar het bijbehorende API-referentiemateriaal.

**Vereisten**

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

- U kunt de [voordelen voor Visual Studio-abonnees activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio): via uw Visual Studio-abonnement ontvangt u elke maand tegoeden die u voor betaalde Azure-services kunt gebruiken.

[!INCLUDE [cosmos-db-emulator-docdb-api](../../includes/cosmos-db-emulator-docdb-api.md)]

U hebt ook de [JavaScript SDK](sql-api-sdk-node.md) nodig.
   
   > [!NOTE]
   > Elk voorbeeld staat op zichzelf. Het stelt zichzelf in en aan het einde worden de gegevens automatisch opgeschoond. Als zodanig wordt in de voorbeelden [Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest) meerdere keren aangeroepen. Telkens wanneer dit wordt gedaan, wordt uw abonnement gefactureerd voor één uur gebruik per prestatielaag van de container die wordt gemaakt.
   > 
   > 

## <a name="database-examples"></a>Voorbeelden voor databases

Het [bestand DatabaseManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts) laat zien hoe u de CRUD-bewerkingen in de database uitvoert. Zie [Werken met databases, containers en items](databases-containers-items.md) conceptueel artikel voor meer informatie over de Azure Cosmos-databases voordat u de volgende voorbeelden uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| [Database maken indien deze niet bestaat](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L12-L14) |[Databases.createIfNotExists](/javascript/api/@azure/cosmos/databases?view=azure-node-latest#createifnotexists-databaserequest--requestoptions-) |
| [Databases voor een account weergeven](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L16-L18) |[Databases.readAll](/javascript/api/@azure/cosmos/databases?view=azure-node-latest#readall-feedoptions-) |
| [Een database lezen op id](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L20-L29) |[Database.read](/javascript/api/@azure/cosmos/database?view=azure-node-latest#read-requestoptions-) |
| [Een database verwijderen](https://github.com/Azure/azure-cosmos-js/blob/master/samples/DatabaseManagement.ts#L31-L32) |[Database.delete](/javascript/api/@azure/cosmos/database?view=azure-node-latest#delete-requestoptions-) |

## <a name="container-examples"></a>Voorbeelden van containers

Het [containermanagementbestand](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts) laat zien hoe u de CRUD-bewerkingen op de container uitvoert. Zie [Werken met databases, containers en items](databases-containers-items.md) conceptueel artikel voor meer informatie over de Azure Cosmos-verzamelingen voordat u de volgende voorbeelden uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| [Container maken indien deze niet bestaat](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L14-L15) |[Containers.createIfNotExists](/javascript/api/@azure/cosmos/containers?view=azure-node-latest#createifnotexists-containerrequest--requestoptions-) |
| [Containers voor een account weergeven](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L17-L21) |[Containers.readAll](/javascript/api/@azure/cosmos/containers?view=azure-node-latest#readall-feedoptions-) |
| [Een containerdefinitie lezen](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L23-L26) |[Container.read](/javascript/api/@azure/cosmos/container?view=azure-node-latest#read-requestoptions-) |
| [Een container verwijderen](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ContainerManagement.ts#L28-L30) |[Container.delete](/javascript/api/@azure/cosmos/container?view=azure-node-latest#delete-requestoptions-) |

## <a name="item-examples"></a>Voorbeelden van items

In het [itemmanagement-bestand](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts) wordt uitgelegd hoe u de CRUD-bewerkingen op het item uitvoert. Zie [Werken met databases, containers en items](databases-containers-items.md) conceptueel artikel voor meer informatie over de Azure Cosmos-documenten voordat u de volgende voorbeelden uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| [Items maken](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L18-L21) |[Items.create](/javascript/api/@azure/cosmos/items?view=azure-node-latest#create-t--requestoptions-) |
| [Alle items in een container lezen](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L23-L28) |[Items.readAll](/javascript/api/@azure/cosmos/items?view=azure-node-latest#readall-feedoptions-) |
| [Een item op id lezen](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L30-L33) |[Item.read](/javascript/api/@azure/cosmos/item?view=azure-node-latest#read-requestoptions-) |
| [Item alleen lezen als het is gewijzigd](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L45-L56) |[Item.read](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [Een query uitvoeren voor documenten](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L58-L79) |[Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Een item vervangen](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L81-L96) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest) |
| [Item vervangen door voorwaardelijke ETag-controle](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L98-L135) |[Item.replace](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest)<br/>[RequestOptions.accessCondition](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#accesscondition) |
| [Een item verwijderen](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ItemManagement.ts#L137-L140) |[Item.delete](https://docs.microsoft.com/javascript/api/%40azure/cosmos/item?view=azure-node-latest) |

## <a name="indexing-examples"></a>Voorbeelden van indexen

Het bestand [IndexManagement](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts) laat zien hoe u indexering beheert. Zie [indexeringsbeleid,](index-policy.md) [indexeringstypen](index-types.md)en [indexeringspaden](index-paths.md) conceptuele artikelen voor meer informatie over indexeren in Azure Cosmos DB voordat u de volgende voorbeelden uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| [Een specifiek item handmatig indexeren](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L52-L75) |[RequestOptions.indexingDirective: 'include'](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [Een specifiek item handmatig uitsluiten van de index](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L17-L29) |[RequestOptions.indexingDirective: 'exclude'](https://docs.microsoft.com/javascript/api/%40azure/cosmos/requestoptions?view=azure-node-latest#indexingdirective) |
| [Een pad uitsluiten van de index](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L142-L167) |[IndexingPolicy.ExcludedPath](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest#excludedpaths) |
| [Een bereikindex maken op een tekenreekspad](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L87-L112) |[IndexKind.Range](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexkind?view=azure-node-latest), [IndexingPolicy](https://docs.microsoft.com/javascript/api/%40azure/cosmos/indexingpolicy?view=azure-node-latest), [Items.query](https://docs.microsoft.com/javascript/api/%40azure/cosmos/items?view=azure-node-latest) |
| [Een container maken met standaard indexPolicy en vervolgens online bijwerken](https://github.com/Azure/azure-cosmos-js/blob/master/samples/IndexManagement.ts#L13-L15) |[Containers.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/containers?view=azure-node-latest)

## <a name="server-side-programming-examples"></a>Voorbeelden van programmering op de server

Het [index.ts-bestand](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) van het [project ServerSideScripts](https://github.com/Azure/azure-cosmos-js/tree/master/samples/ServerSideScripts) geeft aan hoe u de volgende taken uitvoert. Zie [Opgeslagen procedures, triggers en door de gebruiker gedefinieerde functies](stored-procedures-triggers-udfs.md) conceptueel artikel voor meer informatie over serverprogrammering in Azure Cosmos DB voordat u de volgende voorbeelden uitvoert. 

| Taak | API-verwijzing |
| --- | --- |
| [Een opgeslagen procedure maken](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/upsert.js) |[StoredProcedures.create](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedures?view=azure-node-latest) |
| [Een opgeslagen procedure uitvoeren](https://github.com/Azure/azure-cosmos-js/blob/master/samples/ServerSideScripts/index.ts) |[StoredProcedure.execute](https://docs.microsoft.com/javascript/api/%40azure/cosmos/storedprocedure?view=azure-node-latest) |

Zie voor meer informatie over het programmeren op de server [Programmeren op de server met Azure Cosmos DB: opgeslagen procedures, databasetriggers en UDF's](stored-procedures-triggers-udfs.md).

