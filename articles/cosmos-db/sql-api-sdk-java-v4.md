---
title: Opmerkingen en bronnen voor de release van Java SDK v4 voor SQL API Azure Cosmos DB
description: Meer informatie over de Azure Cosmos DB Java SDK v4 voor SQL-API en SDK, inclusief release datums, pensioen datums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB SQL async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 05/20/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: 6cd4d64361e1975a6752ff3a15600923ce3fdf98
ms.sourcegitcommit: a76ff927bd57d2fcc122fa36f7cb21eb22154cfa
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/28/2020
ms.locfileid: "87322887"
---
# <a name="azure-cosmos-db-java-sdk-v4-for-core-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK v4 for core (SQL) API: release opmerkingen en bronnen
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core-SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java-SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java-SDK v2](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-resourceprovider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulk-uitvoerder-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk-uitvoerder-java](sql-api-sdk-bulk-executor-java.md)

De Azure Cosmos DB Java SDK v4 for core (SQL) combineert een async API en een API voor synchronisatie in één maven-artefact. De v4-SDK biedt verbeterde prestaties, nieuwe API-functies en async-ondersteuning op basis van project reactor en de [Netty-bibliotheek](https://netty.io/). Gebruikers kunnen betere prestaties verwachten met Azure Cosmos DB Java SDK v4 versus de [Azure Cosmos DB async Java SDK v2](sql-api-sdk-async-java.md) en de [Azure Cosmos DB Java SDK v2 te synchroniseren](sql-api-sdk-java.md).

> [!IMPORTANT]  
> Deze release opmerkingen zijn alleen voor Azure Cosmos DB Java SDK v4. Als u momenteel een oudere versie dan v4 gebruikt, raadpleegt u de gids [Migreren naar Azure Cosmos DB Java SDK v4](migrate-java-v4-sdk.md) voor hulp om te upgraden naar v4.
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
|**API-documentatie** | [Naslag documentatie voor Java API](https://docs.microsoft.com/java/api/overview/azure/cosmosdb/client?view=azure-java-stable) |
|**Bijdragen aan SDK** | [Azure SDK voor Java Central opslag plaats op GitHub](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/cosmos) | 
|**Aan de slag** | [Quick Start: een Java-app maken voor het beheren van Azure Cosmos DB SQL-API-gegevens](https://docs.microsoft.com/azure/cosmos-db/create-sql-api-java) · [Github opslag plaats met Quick](https://github.com/Azure-Samples/azure-cosmos-java-getting-started) Start-code | 
|**Basic-code voorbeelden** | [Azure Cosmos DB: Java-voor beelden voor de SQL-API](sql-api-java-sdk-samples.md) · [Github opslag plaats met voorbeeld code](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-samples)|
|**Console-app met wijzigings feed**| [Wijzigings feed-Java SDK v4-voor beeld](create-sql-api-java-changefeed.md) · [Github opslag plaats met voorbeeld code](https://github.com/Azure-Samples/azure-cosmos-java-sql-app-example)| 
|**Voor beeld van web-app**| [Een web-app bouwen met Java SDK v4](sql-api-java-application.md) · [Github opslag plaats met voorbeeld code](https://github.com/Azure-Samples/azure-cosmos-java-sql-api-todo-app)|
| **Tips voor prestaties**| [Tips voor betere prestaties voor de Java-SDK v4](performance-tips-java-sdk-v4-sql.md)| 
| **Problemen oplossen** | [Problemen met Java-SDK v4 oplossen](troubleshoot-java-sdk-v4-sql.md) |
| **Migreren naar v4 vanuit een oudere SDK** | [Migreren naar Java v4-SDK](migrate-java-v4-sdk.md) |
| **Minimale ondersteunde runtime**|[JDK 8](/java/azure/jdk/?view=azure-java-stable) | 
| **Azure Cosmos DB workshops en Labs** |[Start pagina van Cosmos DB workshops](https://aka.ms/cosmosworkshop)

## <a name="release-history"></a>Release geschiedenis

### <a name="420-2020-07-14"></a>4.2.0 (2020-07-14)
* De API voor script logboek registratie is ingeschakeld `CosmosStoredProcedureRequestOptions` .
* De `DirectConnectionConfig` standaard waarde is bijgewerkt `idleEndpointTimeout` naar 1U en standaard ingesteld `connectTimeout` op 5s.
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Er is een probleem opgelost waarbij `GatewayConnectionConfig` `idleConnectionTimeout` het is overschreven `DirectConnectionConfig` `idleConnectionTimeout` .
* `responseContinuationTokenLimitInKb`Get en set api's in `CosmosQueryRequestOptions` .
* Er is een probleem opgelost in de query-en wijzigings feed bij het opnieuw maken van de verzameling met dezelfde naam.
* Probleem opgelost met top query-ClassCastException.
* Probleem opgelost met order by query NullPointerException.
* Probleem opgelost bij het verwerken van geannuleerde aanvragen in de directe modus waardoor reactor `onErrorDropped` wordt aangeroepen. 

### <a name="410-2020-06-25"></a>4.1.0 (2020-06-25)
#### <a name="new-features"></a>Nieuwe functies
* Er is ondersteuning toegevoegd voor de `GROUP BY` query.
* De standaard waarde van maxConnectionsPerEndpoint is verhoogd naar 130 in DirectConnectionConfig.
* De standaard waarde van maxRequestsPerConnection is verhoogd naar 30 in DirectConnectionConfig.
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Opgeloste problemen met order by query waarbij dubbele resultaten worden geretourneerd tijdens het hervatten met behulp van vervolg token. 
* Opgeloste problemen met een waarde-query die Null-waarden retourneert voor een genest object.
* Uitzonde ring voor een vaste null-aanwijzer op Request manager in RntbdClientChannelPool.

### <a name="401-2020-06-10"></a>4.0.1 (2020-06-10)
#### <a name="new-features"></a>Nieuwe functies
* De naam is gewijzigd `QueryRequestOptions` in `CosmosQueryRequestOptions` .
* Bijgewerkt `ChangeFeedProcessorBuilder` naar Builder-patroon.
* Bijgewerkt `CosmosPermissionProperties` met de nieuwe container naam en onderliggende bronnen-api's.
* Meer voor beelden & verrijkte documenten toegevoegd `CosmosClientBuilder` . 
* `CosmosDatabase`  &  `CosmosContainer` Api's bijgewerkt met throughputProperties voor ondersteuning bij automatisch schalen/auto pilot. 
* De naam is gewijzigd `CosmosClientException` in `CosmosException` . 
* Vervangen `AccessCondition`  &  `AccessConditionType` door `ifMatchETag()`  &  `ifNoneMatchETag()` api's. 
* Alle typen zijn samengevoegd `Cosmos*AsyncResponse`  &  `CosmosResponse` tot één `CosmosResponse` type.
* De naam is gewijzigd `CosmosResponseDiagnostics` in `CosmosDiagnostics` .  
* Ingepakt `FeedResponseDiagnostics` in `CosmosDiagnostics` . 
* De `jackson` afhankelijkheid van Azure-Cosmos is verwijderd & afhankelijk zijn van Azure core. 
* Vervangen `CosmosKeyCredential` door `AzureKeyCredential` type. 
* Api's zijn toegevoegd `ProxyOptions` aan `GatewayConnectionConfig` . 
* Bijgewerkte SDK voor gebruik `Instant` van type in plaats van `OffsetDateTime` . 
* Nieuw opsommings type toegevoegd `OperationKind` . 
* De naam is gewijzigd `FeedOptions` in `QueryRequestOptions` . 
* Api's zijn toegevoegd `getETag()`  &  `getTimestamp()` aan de `Cosmos*Properties` typen. 
* Er zijn `userAgent` gegevens toegevoegd in `CosmosException`  &  `CosmosDiagnostics` . 
* Het nieuwe regel teken is bijgewerkt `Diagnostics` naar het nieuwe regel teken van het systeem. 
* `readAll*`Api's zijn verwijderd. gebruik in plaats daarvan de query alle api's selecteren.
* De `ChangeFeedProcessor` API voor de schattings vertraging is toegevoegd.   
* Ondersteuning voor automatisch schalen/auto pilot-inrichting voor door Voer is toegevoegd aan SDK.  
* Vervangen `ConnectionPolicy` door nieuwe verbindings configuraties. Blootgestelde `DirectConnectionConfig`  &  `GatewayConnectionConfig` api's via `CosmosClientBuilder` voor directe & gateway modus verbindings configuraties.
* Verplaatst `JsonSerializable`  &  `Resource` naar implementatie pakket. 
* `contentResponseOnWriteEnabled`API toegevoegd aan CosmosClientBuilder, waarmee de inhoud van de volledige reactie voor schrijf bewerkingen wordt uitgeschakeld.
* Blootgestelde `getETag()` api's op antwoord typen.
* Verplaatst `CosmosAuthorizationTokenResolver` naar implementatie. 
* De naam van de API is gewijzigd `preferredLocations`  &  `multipleWriteLocations` in `preferredRegions`  &  `multipleWriteRegions` . 
* `reactor-core`Is bijgewerkt naar 3.3.5. release, `reactor-netty` naar 0.9.7. release & `netty` naar 4.1.49. Final verse. 
* Er is ondersteuning toegevoegd voor `analyticalStoreTimeToLive` in SDK.     
* `CosmosClientException`wordt uitgebreid `AzureException` . 
* `maxItemCount`  &  `requestContinuationToken` Api's zijn uit `FeedOptions` plaats daarvan verwijderd met behulp `byPage()` van api's van `CosmosPagedFlux`  &  `CosmosPagedIterable` .
* Geïntroduceerd `CosmosPermissionProperties` in het open bare Opper vlak voor `Permission` api's.
* Verwijderd `SqlParameterList` type & vervangen door`List`
* Opgelost meerdere geheugen lekken in directe TCP-client. 
* Er is ondersteuning toegevoegd voor `DISTINCT` query's. 
* Externe afhankelijkheden zijn verwijderd op `fasterxml.uuid, guava, commons-io, commons-collection4, commons-text` .  
* Verplaatst `CosmosPagedFlux`  &  `CosmosPagedIterable` naar het `utils` pakket. 
* De Netty is bijgewerkt naar 4.1.45. Final & project-reactor naar 3.3.3-versie.
* De open bare rest-contracten worden bijgewerkt naar `Final` klassen.
* Er is ondersteuning toegevoegd voor geavanceerde diagnostische gegevens voor punt bewerkingen.
* Pakket bijgewerkt naar`com.azure.cosmos`
* `models`Pakket toegevoegd voor model/rest-contracten
* `utils`Pakket toegevoegd voor `CosmosPagedFlux`  &  `CosmosPagedIterable` typen. 
* Bijgewerkte open bare Api's voor gebruik `Duration` in de SDK.
* Alle rest contracten aan het `models` pakket zijn toegevoegd.
* `RetryOptions`de naam is gewijzigd in `ThrottlingRetryOptions` .
* `CosmosPagedFlux`  &  `CosmosPagedIterable` Paginerings typen zijn toegevoegd voor query-api's. 
* Er is ondersteuning toegevoegd voor het delen van TransportClient over meerdere exemplaren van CosmosClients met een nieuwe API in de`CosmosClientBuilder#connectionSharingAcrossClientsEnabled(true)`
* Query optimalisatie door dubbele serialisatie/deserialisatie te verwijderen. 
* De antwoord headers worden geoptimaliseerd door onnodig kopiëren terug en heen te verwijderen. 
* Geoptimaliseerde `ByteBuffer` serialisatie/deserialisatie door tussenliggende teken reeks-exemplaren te verwijderen.
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Uitzonde `toString()` ring voor Connection Policy null-pointer.
* Probleem opgelost bij het parseren van de query resultaten in het geval van een waarde in de volg orde van query's. 
* Problemen met opgeloste socket lekkage met directe TCP-client.
* Opgelost `orderByQuery` met vervolg token fout.
* `ChangeFeedProcessor`fout oplossing voor het afsplitsen van partities & wanneer de partitie niet is gevonden.
* `ChangeFeedProcessor`fout oplossing bij het synchroniseren van lease-updates tussen verschillende threads.
* Er is een `ArrayIndexOutOfBound` uitzonde ring opgetreden in StoreReader vanwege een vaste race

## <a name="faq"></a>Veelgestelde vragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zie tevens
Zie [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-pagina voor meer informatie over Cosmos db.