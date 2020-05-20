---
title: Opmerkingen en bronnen voor de release van Java SDK v4 voor SQL API Azure Cosmos DB
description: Meer informatie over de Azure Cosmos DB Java SDK v4 voor SQL-API en SDK, inclusief release datums, pensioen datums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB SQL async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/11/2020
ms.author: anfeldma
ms.openlocfilehash: cdbb6d0f26b6a999d289e7e0ddbd8cb45dac8e47
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83665199"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 for core (SQL) API: release opmerkingen en bronnen
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

De Azure Cosmos DB Java SDK v4 for core (SQL) combineert een async API en een API voor synchronisatie in één maven-artefact. De v4-SDK biedt verbeterde prestaties, nieuwe API-functies en async-ondersteuning op basis van project reactor en de [Netty-bibliotheek](https://netty.io/). Gebruikers kunnen betere prestaties verwachten met Azure Cosmos DB Java SDK v4 versus de [Azure Cosmos DB async Java SDK v2](sql-api-sdk-async-java.md) en de [Azure Cosmos DB Java SDK v2 te synchroniseren](sql-api-sdk-java.md).

> [!IMPORTANT]  
> Deze release opmerkingen zijn alleen voor Azure Cosmos DB Java SDK v4. Als u momenteel een oudere versie dan V4 gebruikt, raadpleegt u de [migratie naar Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) -hand leiding voor hulp bij het upgraden naar v4.
>
> Hier volgen drie stappen om snel aan de slag te gaan.
> 1. Installeer de [Mini maal ondersteunde Java-runtime, JDK 8](/java/azure/jdk/?view=azure-java-stable) , zodat u de SDK kunt gebruiken.
> 2. Werk aan de [Snelstartgids voor Azure Cosmos DB Java SDK v4](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) , waarmee u toegang krijgt tot het maven-artefact en basis Azure Cosmos DB aanvragen doorloopt.
> 3. Lees de Azure Cosmos DB Java SDK v4- [prestatie tips](performance-tips-java-sdk-v4-sql.md) en richt lijnen voor [probleem oplossing](troubleshoot-java-sdk-v4-sql.md) om de SDK voor uw toepassing te optimaliseren.
>
> De [Azure Cosmos DB workshops en Labs](https://aka.ms/cosmosworkshop) zijn een fantastische resource om te leren hoe u Azure Cosmos DB Java SDK v4 kunt gebruiken.
>

| |  |
|---|---|
| **SDK downloaden** | [Maven](https://mvnrepository.com/artifact/com.azure/azure-cosmos) |
|**API-documentatie** |Naslag documentatie voor Java API|
|**Bijdragen aan SDK** | [Azure SDK voor Java Central opslag plaats op GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos) | 
|**Aan de slag** | [Quick Start: een Java-app maken voor het beheren van Azure Cosmos DB SQL-API-gegevens](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) · [Github opslag plaats met Quick](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) Start-code | 
|**Basic-code voorbeelden** | Azure Cosmos DB: Java-voor beelden voor de SQL-API · [Github opslag plaats met voorbeeld code](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Console-app met wijzigings feed**| [Wijzigings feed-Java SDK v4-voor beeld](create-sql-api-java-changefeed.md) · [Github opslag plaats met voorbeeld code](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Voor beeld van web-app**| [Een web-app bouwen met Java SDK v4](sql-api-java-application.md) · [Github opslag plaats met voorbeeld code](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Tips voor prestaties**| [Tips voor betere prestaties voor de Java-SDK v4](performance-tips-java-sdk-v4-sql.md)| 
| **Problemen oplossen** | [Problemen met Java-SDK v4 oplossen](troubleshoot-java-sdk-v4-sql.md) |
| **Migreren naar v4 vanuit een oudere SDK** | [Migreren naar Java v4-SDK](migrate-java-v4-sdk.md) |
| **Minimale ondersteunde runtime**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Azure Cosmos DB workshops en Labs** |[Start pagina van Cosmos DB workshops](https://aka.ms/cosmosworkshop)

