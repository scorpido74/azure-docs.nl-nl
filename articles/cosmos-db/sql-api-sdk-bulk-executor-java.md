---
title: 'Azure Cosmos DB: bulk-upload Java API, SDK & resources'
description: Meer informatie over de bulk-lanceren Java API en SDK, inclusief release datums, pensioen datums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB bulk-uitvoerder Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/11/2020
ms.author: anfeldma
ms.openlocfilehash: c826b9f813c30a50d8b88b2cca1b188c328465b0
ms.sourcegitcommit: 1692e86772217fcd36d34914e4fb4868d145687b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/29/2020
ms.locfileid: "84171647"
---
# <a name="java-bulk-executor-library-download-information"></a>Java-bibliotheek voor bulksgewijs laden: Download informatie

> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-wijzigings feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java-SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java-SDK v2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resourceprovider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulk-uitvoerder-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk-uitvoerder-java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**Beschrijving**|Met de bibliotheek bulk-uitvoerder kunnen client toepassingen bulk bewerkingen uitvoeren in Azure Cosmos DB accounts. de bibliotheek voor bulk-uitvoerder biedt BulkImport-en BulkUpdate-naam ruimten. De BulkImport-module kan documenten bulksgewijs opnemen in een geoptimaliseerde manier, zodat de door Voer ingericht voor een verzameling voor het maximale aantal wordt verbruikt. De BulkUpdate-module kan bestaande gegevens in azure Cosmos-containers bulksgewijs bijwerken als patches.|
|**SDK downloaden**|[Maven](https://search.maven.org/#search%7Cga%7C1%7Cdocumentdb-bulkexecutor)|
|**Bibliotheek voor bulk-uitvoerder in GitHub**|[GitHub](https://github.com/Azure/azure-cosmosdb-bulkexecutor-java-getting-started)|
| **API-documentatie**| [Naslag documentatie voor Java API](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.bulkexecutor)|
|**Aan de slag**|[Aan de slag met de bibliotheek met Java-SDK voor bulksgewijs laden](bulk-executor-java.md)|
|**Minimale ondersteunde runtime**|[Java Development Kit (JDK) 7 +](/java/azure/jdk/?view=azure-java-stable)|

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="2100"></a><a name="2.10.0"></a>2.10.0

* Correctie voor DocumentAnalyzer. Java voor het uitpakken van geneste partitie sleutel waarden van JSON.

### <a name="294"></a><a name="2.9.4"></a>2.9.4

* Voeg functionaliteit toe aan BulkDelete-bewerkingen om een nieuwe poging te doen voor specifieke fouten en om ook een lijst met fouten te retour neren aan de gebruiker die opnieuw zou kunnen worden uitgevoerd.

### <a name="293"></a><a name="2.9.3"></a>2.9.3

* Update voor de Cosmos SDK-versie 2.4.7.

### <a name="292"></a><a name="2.9.2"></a>2.9.2

* Correctie voor ' mergeAll ' om door te gaan met ' id ' en de partitie sleutel waarde zodat alle patched document eigenschappen die zijn geplaatst na ' id ' en partitie sleutel waarde worden toegevoegd aan de lijst met bijgewerkte items.

### <a name="291"></a><a name="2.9.1"></a>2.9.1

* Hiermee wordt de begin graad van gelijktijdigheid bijgewerkt naar 1 en worden logboeken voor fout opsporing voor minibatch toegevoegd.


