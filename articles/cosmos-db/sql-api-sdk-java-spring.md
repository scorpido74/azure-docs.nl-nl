---
title: Lente gegevens Azure Cosmos DB voor opmerkingen bij de release van SQL API en bronnen
description: Meer informatie over de lente gegevens Azure Cosmos DB voor SQL-API, inclusief release datums, pensioen datums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB SQL async Java SDK.
author: anfeldma-ms
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 08/05/2020
ms.author: anfeldma
ms.custom: devx-track-java
ms.openlocfilehash: f94ea37966c4942f72ae2d7b701e742891fa6cd2
ms.sourcegitcommit: 7fe8df79526a0067be4651ce6fa96fa9d4f21355
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/06/2020
ms.locfileid: "87854241"
---
# <a name="spring-data-azure-cosmos-db-for-core-sql-api-release-notes-and-resources"></a>Lente gegevens Azure Cosmos DB voor Core-API (SQL): release opmerkingen en bronnen
> [!div class="op_single_selector"]
> * [.NET SDK v3](sql-api-sdk-dotnet-standard.md)
> * [.NET SDK v2](sql-api-sdk-dotnet.md)
> * [.NET Core-SDK v2](sql-api-sdk-dotnet-core.md)
> * [.NET Change feed SDK v2](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Java SDK v4](sql-api-sdk-java-v4.md)
> * [Async Java-SDK v2](sql-api-sdk-async-java.md)
> * [Sync Java-SDK v2](sql-api-sdk-java.md)
> * [Spring Data](sql-api-sdk-java-spring.md)
> * [Spark-connector](sql-api-sdk-java-spark.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](/rest/api/cosmos-db/)
> * [REST-resourceprovider](/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulk-uitvoerder-.NET v2](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk-uitvoerder-java](sql-api-sdk-bulk-executor-java.md)

Met de lente gegevens Azure Cosmos DB voor core (SQL) kunnen ontwikkel aars Azure Cosmos DB gebruiken in lente-toepassingen. Lente gegevens Azure Cosmos DB maakt de lente gegevens interface beschikbaar voor het bewerken van data bases en verzamelingen, het werken met documenten en het uitgeven van query's. Zowel synchronisatie-als async-Api's (reactieve) worden ondersteund in hetzelfde maven-artefact. 

Het [lente-Framework](https://spring.io/projects/spring-framework) is een programmeer-en configuratie model dat het ontwikkelen van Java-toepassingen stroomlijnt. Als u de website van de organisatie wilt citeren, stroomlijnt lente de ' sanitaire ' van toepassingen die gebruikmaken van afhankelijkheids injectie. Veel ontwikkel aars zoals lente, want het maken en testen van toepassingen wordt gecompliceerder. [Spring boot](https://spring.io/projects/spring-boot) breidt dit idee uit van het afhandelen van de sanitair met een ogen ding van webtoepassingen en de ontwikkeling van micro Services. [Lente gegevens](https://spring.io/projects/spring-data) is een programmeer model voor het verkrijgen van toegang tot gegevens opslag zoals Azure Cosmos DB vanuit de context van een lente-of Spring boot-toepassing. 

U kunt lente gegevens Azure Cosmos DB gebruiken in uw [Azure lente-Cloud](https://azure.microsoft.com/services/spring-cloud/) toepassingen.

> [!IMPORTANT]  
> Deze release opmerkingen zijn alleen voor Azure Cosmos DB SQL-API. 
>
> De volgende hand leidingen ondersteunen lente gegevens op andere Azure Cosmos DB-Api's:
> * [Lente gegevens voor Apache Cassandra met Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-apache-cassandra-with-cosmos-db)
> * [Lente gegevens MongoDB met Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-mongodb-with-cosmos-db)
> * [Lente gegevens Gremlin met Azure Cosmos DB](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-data-gremlin-java-app-with-cosmos-db)
>
> Wilt u snel aan de slag?
> 1. Installeer de [Mini maal ondersteunde Java-runtime, JDK 8](/java/azure/jdk/?view=azure-java-stable) , zodat u de SDK kunt gebruiken.
> 2. Maak een lente-data Azure Cosmos DB-app met behulp [van](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db)de starter.
> 3. Werk via de [hand leiding voor de lente gegevens Azure Cosmos DB ontwikkel aars](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) die de basis Azure Cosmos DB aanvragen doorloopt.
>
> U kunt snelle opstart starter-apps snel met [lente initialiseren](https://start.spring.io/).
>

## <a name="helpful-content"></a>Nuttige inhoud

| Inhoud | Koppeling |
|---|---|
| **SDK downloaden** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/spring-data-cosmosdb) |
|**API-documentatie** | [Referentie documentatie voor lente gegevens Azure Cosmos DB]() |
|**Bijdragen aan SDK** | [Lente gegevens Azure Cosmos DB opslag plaats op GitHub](https://github.com/microsoft/spring-data-cosmosdb) | 
|**Spring boot starter**| [Azure Cosmos DB lente boot starter-client bibliotheek voor Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Voor beeld van een app voor een nieuwe TODO met Azure Cosmos DB**| [End-to-end Java-ervaring in App Service Linux (deel 2)](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2) |
|**Hand leiding voor ontwikkel aars** | [Hand leiding voor lente gegevens Azure Cosmos DB ontwikkel aars](https://docs.microsoft.com/azure/developer/java/spring-framework/how-to-guides-spring-data-cosmosdb) | 
|**Hand leiding voor het gebruik van starter** | [Spring Boot Starter gebruiken met de Azure Cosmos DB SQL-API](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-boot-starter-java-app-with-cosmos-db) <br> [GitHub opslag plaats voor Azure Spring boot starter Cosmos DB](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/spring/azure-spring-boot-starter-cosmosdb) |
|**Voor beeld met App Services** | [Spring en Azure Cosmos DB met App Service op Linux gebruiken](https://docs.microsoft.com/azure/developer/java/spring-framework/configure-spring-app-with-cosmos-db-on-app-service-linux) <br> [Voor beeld van TODO-app](https://github.com/Azure-Samples/e2e-java-experience-in-app-service-linux-part-2.git) |

## <a name="release-history"></a>Release geschiedenis

### <a name="230-2020-05-21"></a>2.3.0 (2020-05-21)
#### <a name="new-features"></a>Nieuwe functies
* Spring boot-versie bijwerken naar 2.3.0 
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten

### <a name="225-2020-05-19"></a>2.2.5 (2020-05-19)
#### <a name="new-features"></a>Nieuwe functies
* Azure Cosmos DB versie bijgewerkt naar v 3.7.3
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Bevat patches voor geheugen lekkage en Netty-versie-upgrades van Cosmos SDK v 3.7.3 

### <a name="224-2020-04-06"></a>2.2.4 (2020-04-06)
#### <a name="new-features"></a>Nieuwe functies
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* De vlag Fixed allowTelemetry om rekening te houden met CosmosDbConfig
* Vaste TTL-eigenschap in container

### <a name="223-2020-02-25"></a>2.2.3 (2020-02-25)
#### <a name="new-features"></a>Nieuwe functies
* Nieuwe findAll toegevoegd door de partitie sleutel-API
* De Azure-Cosmos-versie is bijgewerkt naar 3.7.0
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Vaste verzamelingsset-> containerName
* Vaste entityClass & Domain class-> Domain type
* Fixed "retour entiteit verzameling die is opgeslagen door de opslag plaats in plaats van invoer entiteit"

### <a name="2110-2020-02-25"></a>2.1.10 (2020-02-25)
#### <a name="new-features"></a>Nieuwe functies
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Backported-oplossing voor het retour neren van entiteits verzameling die is opgeslagen door de opslag plaats in plaats van invoer entiteit

### <a name="222-2020-01-15"></a>2.2.2 (2020-01-15)
#### <a name="new-features"></a>Nieuwe functies
* De Azure-Cosmos-versie is bijgewerkt naar v 3.6.0
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten

### <a name="221-2019-12-31"></a>2.2.1 (2019-12-31)
#### <a name="new-features"></a>Nieuwe functies
* Cosmos DB SDK-versie bijgewerkt naar 3.5.0
* Aantekening veld toegevoegd om het automatisch maken van de verzameling in of uit te scha kelen
* Betere afhandeling van uitzonde ringen, weer gegeven CosmosClientException via CosmosDBAccessException
* Blootgestelde requestCharge en activityId via ResponseDiagnostics
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* SDK 3.5.0 update Fixes ' uitzonde ring wanneer Cosmos DB HTTP-reactie header groter is dan 8192 bytes ', ' ConsistencyPolicy. defaultConsistencyLevel () mislukt bij gebonden veroudering en consistent voor voegsel '
* Gedrag van opgeloste findById-Api's, retour neren is leeg op niet gevonden, in plaats van uitzonde ring
* Er is een fout opgelost waarbij sorteren niet werd toegepast op de volgende pagina bij gebruik van CosmosPageRequest

### <a name="219-2019-12-26"></a>2.1.9 (2019-12-26)
#### <a name="new-features"></a>Nieuwe functies
* Aantekening veld toegevoegd om het automatisch maken van de verzameling in of uit te scha kelen
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Gedrag van opgeloste findById-Api's, retour neren is leeg op niet gevonden, in plaats van uitzonde ring

### <a name="220-2019-10-21"></a>2.2.0 (2019-10-21)
#### <a name="new-features"></a>Nieuwe functies
* De ondersteuning voor reactieve Cosmos-opslag plaatsen volt ooien
* Ondersteuning voor Cosmos DB teken reeks voor diagnostische gegevens en metrische gegevens voor query
* Cosmos DB SDK-versie-update naar 3.3.1
* Upgrade van Lente Framework-versie naar 5.2.0. RELEASE
* Upgrade naar 2.2.0. RELEASE van Lente data Commons-versie
* FindByIdAndPartitionKey, deleteByIdAndPartitionKey-Api's toegevoegd
* De afhankelijkheid is verwijderd uit Azure-doumentdb
* DocumentDb is opnieuw op Cosmos
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Fixed ' Sortes worden uitgecheckt wanneer pageSize minder is dan het totale aantal items in de opslag plaats '

### <a name="218-2019-10-18"></a>2.1.8 (2019-10-18)
#### <a name="new-features"></a>Nieuwe functies
* Document DB-Api's afafschaffen
* FindByIdAndPartitionKey, deleteByIdAndPartitionKey-Api's zijn toegevoegd.
* Optimistische vergren deling is toegevoegd op basis van _etag
* SPeL-expressie ingeschakeld voor naam van document verzameling
* Verbeteringen in ObjectMapper.
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten

### <a name="217-2019-10-18"></a>2.1.7 (2019-10-18)
#### <a name="new-features"></a>Nieuwe functies
* Cosmos SDK v3-afhankelijkheid toegevoegd
* Reactieve Cosmos-opslag plaats toegevoegd
* De implementatie van DocumentDbTemplate is bijgewerkt om Cosmos SDK v3 te gebruiken.
* Andere configuratie wijzigingen voor de ondersteuning van Reactive Cosmos-opslag plaatsen.
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten

### <a name="212-2019-03-19"></a>2.1.2 (2019-03-19)
#### <a name="new-features"></a>Nieuwe functies
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* ApplicationInsights-afhankelijkheid verwijderen voor
    * Mogelijk risico van verontreiniging van afhankelijkheden
    * Java 11 incompatibiliteit
    * Voorkom mogelijke prestatie-impact op CPU en/of geheugen.

### <a name="207-2019-03-20"></a>2.0.7 (2019-03-20)
#### <a name="new-features"></a>Nieuwe functies
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Backport verwijdert applicationInsights-afhankelijkheid voor
    * Mogelijk risico van verontreiniging van afhankelijkheden
    * Java 11 incompatibiliteit
    * Voorkom mogelijke prestatie-impact op CPU en/of geheugen.

### <a name="211-2019-03-07"></a>2.1.1 (2019-03-07)
#### <a name="new-features"></a>Nieuwe functies
* Hoofd versie bijwerken naar 2.1.1
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten

### <a name="206-2019-03-07"></a>2.0.6 (2019-03-07)
#### <a name="new-features"></a>Nieuwe functies
* Alle uitzonde ringen van telemetrie negeren
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten

### <a name="210-2018-12-17"></a>2.1.0 (2018-12-17)
#### <a name="new-features"></a>Nieuwe functies
* De versie bijwerken naar 2.1.0 voor het oplossen van problemen
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten

### <a name="205-2018-09-13"></a>2.0.5 (2018-09-13)
#### <a name="new-features"></a>Nieuwe functies
* Sleutel woord toevoegen bestaat, startsWith
* Leesmij bijwerken
#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Fix ' kan Self href rechtstreeks voor entiteit '
* Fix ' findAll mislukt als de verzameling niet wordt gemaakt '

### <a name="204-pre-release-2018-08-23"></a>2.0.4 (voorlopige versie) (2018-08-23)
#### <a name="new-features"></a>Nieuwe functies
* Naam van pakket wijzigen van documentdb in cosmosdb,
* Nieuwe functie toevoegen tref woord voor query methode, 16 tref woorden uit SQL API ondersteund.
* Voeg een nieuwe functie van de query toe met paginering en sorteren.
* Vereenvoudig de configuratie van de lente-data-cosmosdb.
* DeleteCollection-en deleteAll-API toevoegen.

#### <a name="key-bug-fixes"></a>Oplossingen voor belang rijke fouten
* Probleem oplossing en uitbrei ding van fouten.

## <a name="faq"></a>Veelgestelde vragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="next-steps"></a>Volgende stappen
Zie [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-pagina voor meer informatie over Cosmos db.

Ga naar de [Start pagina](https://spring.io/projects/spring-framework)van het project voor meer informatie over het lente-Framework.

Ga naar de [Start pagina](https://spring.io/projects/spring-boot)van het project voor meer informatie over de lente boot.

Ga naar de [Start pagina](https://spring.io/projects/spring-data)van het project voor meer informatie over Lente gegevens.