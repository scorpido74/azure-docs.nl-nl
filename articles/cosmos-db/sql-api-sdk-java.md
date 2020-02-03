---
title: 'Azure Cosmos DB: SQL Java API, SDK en resources'
description: Meer informatie over de SQL Java API en SDK, inclusief release datums, buiten gebruik stellen datums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB SQL Java SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 03/13/2019
ms.author: sngun
ms.openlocfilehash: 3a7eaabedd6736c107625204f982a530f2bab73d
ms.sourcegitcommit: f52ce6052c795035763dbba6de0b50ec17d7cd1d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/24/2020
ms.locfileid: "76718170"
---
# <a name="azure-cosmos-db-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB Java SDK voor SQL-API: releaseopmerkingen en resources
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-wijzigings feed](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resourceprovider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulk-uitvoerder-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk-uitvoerder-java](sql-api-sdk-bulk-executor-java.md)

De SQL API Java-SDK biedt ondersteuning voor synchrone bewerkingen. Voor asynchrone ondersteuning gebruikt u de [SQL API async Java SDK](sql-api-sdk-async-java.md). 

| |  |
|---|---|
|**SDK downloaden**|[Maven](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22com.microsoft.azure%22%20AND%20a%3A%22azure-documentdb%22)|
|**API-documentatie**|[Naslag documentatie voor Java API](/java/api/com.microsoft.azure.documentdb)|
|**Bijdragen aan SDK**|[GitHub](https://github.com/Azure/azure-documentdb-java/)|
|**Aan de slag**|[Aan de slag met de Java SDK](sql-api-java-get-started.md)|
|**Zelf studie voor web-apps**|[Ontwikkeling van webtoepassingen met Azure Cosmos DB](sql-api-java-application.md)|
|**Minimale ondersteunde runtime**|[Java Development Kit (JDK) 7 +](https://aka.ms/azure-jdks)|

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="a-name246246"></a><a name="2.4.6"/>2.4.6
* De juiste replica beleids label aan client zijde is bijgewerkt op databaseAccount en er zijn databaseAccount configuratie Lees bewerkingen van de cache gemaakt.

### <a name="a-name245245"></a><a name="2.4.5"/>2.4.5
* Nieuwe poging op ongeldige partitie sleutel bereik fout voor komen als de gebruiker pkRangeId levert.

### <a name="a-name244244"></a><a name="2.4.4"/>2.4.4
* Versleutelde cache sleutels voor het bereik van geoptimaliseerde partities.
* Hiermee wordt het scenario opgelost waarbij de SDK geen problemen met de splitsing van de partitie van de server oplevert en resulteert in onjuiste routerings caches voor client zijde vernieuwen.

### <a name="a-name242242"></a><a name="2.4.2"/>2.4.2
* Geoptimaliseerde verzamelings cache vernieuwt.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1
* Er is ondersteuning toegevoegd om een uitzonderings bericht op te halen uit de teken reeks voor aanvraag diagnose.

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0
* Versie-API is geïntroduceerd op PartitionKeyDefinition.

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Er is afzonderlijke time-outondersteuning toegevoegd voor de directe modus.

### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3
* Er wordt een null-fout bericht verbruikt van de service en de document-client uitzondering wordt geproduceerd.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Verbetering van socket verbinding, toevoegen SoKeepAlive standaard ingesteld op True.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Ondersteuning voor diagnostische teken reeksen voor aanvragen is toegevoegd.

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3
* Opgeloste fout in PartitionKey voor hash v2.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2
* Er is ondersteuning toegevoegd voor samengestelde indexen.
* Er is een probleem opgelost in Global Endpoint Manager om het vernieuwen af te dwingen.
* Er is een fout opgelost voor upsert met voor waarden in de directe modus.

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1
* Er is een fout opgelost in de cache van de gateway.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Ondersteuning voor meerdere regio's schrijven toegevoegd voor de directe modus.
* Er is ondersteuning toegevoegd voor het verwerken van IOExceptions gegenereerd als ServiceUnavailable uitzonderingen, van een proxy.
* Beleid voor opnieuw proberen van eindpunt detectie, een bug vast.
* Een opgelost om ervoor te zorgen null-aanwijzer uitzonderingen worden niet in BaseDatabaseAccountConfigurationProvider veroorzaakt.
* Er is een fout opgelost om ervoor te zorgen dat QueryIterator geen Null-waarden retourneert.
* Een probleem opgelost om ervoor te zorgen dat grote PartitionKey is toegestaan

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Ondersteuning voor meerdere regio's schrijven toegevoegd voor de gateway-modus.

### <a name="a-name11641164"></a><a name="1.16.4"/>1.16.4
* Vaste een bug in lezen partitie sleutel bereiken voor een query.

### <a name="a-name11631163"></a><a name="1.16.3"/>1.16.3
* Een opgelost bij het instellen van voortzetting van token header-grootte in de modus DirectHttps.

### <a name="a-name11621162"></a><a name="1.16.2"/>1.16.2
* Toegevoegde streaming failover-ondersteuning.
* Ondersteuning toegevoegd voor aangepaste metagegevens.
* Verbeterde sessie logische verwerken.
* Partitie sleutelbereik cache, een bug vast.
* Een NFE opgelost in de directe modus.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Ondersteuning toegevoegd voor unieke Index.
* Ondersteuning toegevoegd voor het beperken van de voortzetting van token grootte in de feed-opties.
* Een bug opgelost in Json-serialisatie (tijdstempel).
* Een bug opgelost in Json-serialisatie (enum).
* Afhankelijkheid van een upgrade uitgevoerd naar 2.9.5 com.fasterxml.jackson.core:jackson-DataBind oproept.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
* Verbeterde groepsgewijze verbindingen voor de directe modus.
* Prefetch verbetering voor orderby cross-partitiequery verbeterd.
* Verbeterde UUID genereren.
* Verbeterde sessie consistentie logica.
* Ondersteuning toegevoegd voor multipolygon.
* Ondersteuning toegevoegd voor partitie sleutel bereik statistieken voor de verzameling.
* Ondersteuning voor meerdere regio's, een bug vast.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
* Verbeterde prestaties van de Json-serialisatie.
* Voor deze SDK-versie is de nieuwste versie van Azure Cosmos DB emulator beschikbaar om te worden gedownload van https://aka.ms/cosmosdb-emulator.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
* Interne wijzigingen voor Microsoft vrienden bibliotheken.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Er is een probleem bij het lezen van één partitie sleutelbereiken opgelost.
* Er is een probleem opgelost in ResourceID parseren die van invloed is op de database met korte namen.
* Er is een probleem veroorzaakt door het partitie sleutels coderen opgelost.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Kritieke problemen opgelost voor aanvraagverwerking tijdens partitie worden opgesplitst.
* Een probleem opgelost met de sterke en BoundedStaleness consistentieniveaus.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Er is ondersteuning toegevoegd voor een nieuwe consistentieniveau ConsistentPrefix genoemd.
* Een opgelost bij het lezen van de verzameling in de modus voor sessies.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Ingeschakelde ondersteuning voor gepartitioneerde verzameling met als laag als 2.500 RU/sec. en schalen in stappen van 100 RU/sec.
* Een bug opgelost in de systeemeigen assembly die leiden NullRef uitzondering in sommige query's tot kan.

### <a name="a-name196196"></a><a name="1.9.6"/>1.9.6
* Een bug opgelost in de configuratie van de query-engine die uitzonderingen voor query's in de modus van de Gateway kan veroorzaken.
* Enkele fouten in de sessie-container die ertoe leiden een 'Eigenaar resource is niet gevonden'-uitzondering voor aanvragen onmiddellijk na het verzamelen van dat kan opgelost.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Er is ondersteuning toegevoegd voor aggregatie-query's (aantal, MIN, MAX, som en gemiddelde). Zie [ondersteuning voor aggregatie](sql-query-aggregates.md).
* Ondersteuning toegevoegd voor feed wijzigen.
* Ondersteuning toegevoegd voor verzameling quotumgegevens via RequestOptions.setPopulateQuotaInfo.
* Er is ondersteuning toegevoegd voor de opgeslagen procedure script logboekregistratie via RequestOptions.setScriptLoggingEnabled.
* Er is een fout opgelost waarbij de query in de DirectHttps-modus niet meer reageert wanneer er vertragings fouten optreden.
* Een bug opgelost in sessiemodus consistentie.
* Een opgelost waardoor NullReferenceException in HttpContext wanneer aanvraagsnelheid hoog is.
* Verbeterde prestaties van DirectHttps modus.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* Toegevoegde eenvoudige client exemplaar op basis van proxy-ondersteuning met ConnectionPolicy.setProxy()-API.
* Toegevoegde DocumentClient.close() API correct DocumentClient-exemplaar afsluiten.
* Verbeterde prestaties van query's in de modus voor directe verbinding met het queryplan die is afgeleid van de systeemeigen assembly in plaats van de Gateway.
* Stel FAIL_ON_UNKNOWN_PROPERTIES = false, zodat gebruikers niet hoeft te JsonIgnoreProperties in hun POJO definiëren.
* Geherstructureerd logboekregistratie voor het gebruik van SLF4J.
* Enkele andere fouten opgelost in de lezer van de consistentie.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Een bug opgelost bij het Verbindingsbeheer om te voorkomen dat het lekken van de verbinding in de modus voor directe connectiviteit.
* Er is een fout opgelost in de bovenste query waar deze NullReference-uitzonde ring kan genereren.
* Verbeterde prestaties doordat er minder van netwerkaanroep voor de interne caches.
* Toegevoegde statuscode, ActivityID en aanvraag-URI in DocumentClientException voor het oplossen van betere.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Een probleem opgelost bij het Verbindingsbeheer voor de stabiliteit.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1
* Ondersteuning toegevoegd voor BoundedStaleness consistentieniveau.
* Ondersteuning toegevoegd voor directe connectiviteit voor CRUD-bewerkingen voor gepartitioneerde verzamelingen.
* Een opgelost bij het zoeken van een database met SQL.
* Er is een bug opgelost in de sessiecache waar sessietoken niet goed kan worden ingesteld.

### <a name="a-name190190"></a><a name="1.9.0"/>1.9.0
* Ondersteuning toegevoegd voor cross-partitie parallelle query's.
* Ondersteuning toegevoegd voor bovenste/ORDER BY-query's voor gepartitioneerde verzamelingen.
* Ondersteuning toegevoegd voor sterke consistentie.
* Er is ondersteuning toegevoegd voor de naam op basis van aanvragen bij het gebruik van directe connectiviteit.
* Vaste waarmee ActivityId blijven consistent op alle nieuwe aanvraagpogingen.
* Een opgelost met betrekking tot de sessiecache bij het opnieuw genereren van een verzameling met dezelfde naam.
* Toegevoegde veelhoek en LineString DataTypes tijdens het instellen van de verzameling indexeringsbeleid voor geofencing ruimtelijke query's.
* Opgeloste problemen met Java-documentatie voor Java 1.8.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1
* Een bug opgelost in PartitionKeyDefinitionMap verzamelingen met één partitie in de cache en geen extra ophalen voor het partitioneren van sleutels aanvragen.
* Een opgelost om opnieuw te proberen niet als een waarde voor de onjuiste partitiesleutel is opgegeven.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* De ondersteuning voor databaseaccounts voor meerdere regio's toegevoegd.
* Ondersteuning toegevoegd voor automatische nieuwe pogingen op beperkte aanvragen met opties voor het aanpassen van de maximale nieuwe pogingen en de maximale wachttijd.  Zie RetryOptions en ConnectionPolicy.getRetryOptions().
* Afgeschafte IPartitionResolver op basis van aangepaste code voor partitionering. Gebruik gepartitioneerde verzamelingen voor hogere opslag en doorvoer.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Beleid-ondersteuning voor het toegevoegde nieuwe pogingen voor gelden enkele beperkingen.  

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Toegevoegde tijd naar live (TTL) ondersteuning voor documenten.

### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0
* Geïmplementeerde [gepartitioneerde verzamelingen](partition-data.md) en door de [gebruiker gedefinieerde prestatie niveaus](performance-levels.md).

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* Een bug opgelost in HashPartitionResolver voor het genereren van hash-waarden in weinig endian zodat deze overeenkomt met andere SDK's.

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Toevoegen van hash- & bereik partitie-resolvers om te helpen met sharding-toepassingen voor meerdere partities.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Upsert implementeren. Nieuwe upsertXXX methoden toegevoegd ter ondersteuning van Upsert-functie.
* Implementatie-ID op basis van routering. Er zijn geen openbare API-wijzigingen, worden alle interne wijzigingen.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Release overgeslagen zodat het versienummer in overeenstemming met andere SDK 's

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Ondersteunt georuimtelijke Index
* Valideert de eigenschap ID voor alle resources. Id's voor resources mogen geen?-,/, # \, tekens bevatten of eindigen met een spatie.
* Nieuwe header 'index transformatie uitgevoerd' toevoegen aan ResourceResponse.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Indexeringsbeleid V2 implementeert

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA-SDK

## <a name="release-and-retirement-dates"></a>Release-en buiten gebruik stellen
Micro soft zal ten minste **twaalf maanden** vóór het buiten gebruik stellen van een SDK een melding ontvangen om de overgang naar een nieuwere/ondersteunde versie te versoepelen.

Nieuwe functies en functionaliteit en -optimalisatie worden alleen toegevoegd aan de huidige SDK, daarom wordt aanbevolen dat u altijd een upgrade uitvoert naar de nieuwste SDK versie zo vroeg mogelijk.

Een aanvraag voor het Cosmos DB met behulp van een buiten gebruik gestelde SDK worden geweigerd door de service.

> [!WARNING]
> Alle versies **1. x** van de SQL-SDK voor Java zullen op **30 mei 2020**worden ingetrokken.
> 
>

> [!WARNING]
> Alle versies van de SQL-SDK voor Java vóór versie **1.0.0** zijn ingetrokken op **29 februari 2016**.
> 
> 

<br/>

| Version | Releasedatum | Vervaldatum |
| --- | --- | --- |
| [2.1.3](#2.1.3) |13 maart 2018 |--- |
| [omschreven](#2.1.2) |9 maart 2018 |--- |
| [2.1.1](#2.1.1) |13 december 2018 |--- |
| [2.1.0](#2.1.0) |20 november 2018 |--- |
| [2.0.0](#2.0.0) |21 september 2018 |--- |
| [1.16.4](#1.16.4) |10 september 2018 |30 mei 2020 |
| [1.16.3](#1.16.3) |09 september 2018 |30 mei 2020 |
| [1.16.2](#1.16.2) |29 juni 2018 |30 mei 2020 |
| [1.16.1](#1.16.1) |16 mei 2018 |30 mei 2020 |
| [1.16.0](#1.16.0) |15 maart 2018 |30 mei 2020 |
| [1.15.0](#1.15.0) |14 november 2017 |30 mei 2020 |
| [1.14.0](#1.14.0) |28 oktober 2017 |30 mei 2020 |
| [1.13.0](#1.13.0) |25 augustus 2017 |30 mei 2020 |
| [1.12.0](#1.12.0) |Op 11 juli 2017 |30 mei 2020 |
| [1.11.0](#1.11.0) |10 mei 2017 |30 mei 2020 |
| [1.10.0](#1.10.0) |11 maart 2017 |30 mei 2020 |
| [1.9.6](#1.9.6) |21 februari 2017 |30 mei 2020 |
| [1.9.5](#1.9.5) |En met 31 januari 2017 |30 mei 2020 |
| [1.9.4](#1.9.4) |24 november 2016 |30 mei 2020 |
| [1.9.3](#1.9.3) |30 oktober 2016 |30 mei 2020 |
| [1.9.2](#1.9.2) |28 oktober 2016 |30 mei 2020 |
| [1.9.1](#1.9.1) |26 oktober 2016 |30 mei 2020 |
| [1.9.0](#1.9.0) |03 oktober 2016 |30 mei 2020 |
| [1.8.1](#1.8.1) |Tot 30 juni 2016 |30 mei 2020 |
| [1.8.0](#1.8.0) |14 juni 2016 |30 mei 2020 |
| [1.7.1](#1.7.1) |30 april 2016 |30 mei 2020 |
| [1.7.0](#1.7.0) |27 april 2016 |30 mei 2020 |
| [1.6.0](#1.6.0) |29 maart 2016 |30 mei 2020 |
| [1.5.1](#1.5.1) |En met 31 december 2015 |30 mei 2020 |
| [1.5.0](#1.5.0) |04 december 2015 |30 mei 2020 |
| [1.4.0](#1.4.0) |05 oktober 2015 |30 mei 2020 |
| [1.3.0](#1.3.0) |05 oktober 2015 |30 mei 2020 |
| [1.2.0](#1.2.0) |05 augustus 2015 |30 mei 2020 |
| [1.1.0](#1.1.0) |09 juli 2015 |30 mei 2020 |
| 1.0.1 |12 mei 2015 |30 mei 2020 |
| [1.0.0](#1.0.0) |07 april 2015 |30 mei 2020 |
| 0.9.5-prelease |09 maart 2015 |En met 29 februari 2016 |
| 0.9.4-prelease |17 februari 2015 |En met 29 februari 2016 |
| 0.9.3-prelease |13 januari 2015 |En met 29 februari 2016 |
| 0.9.2-prelease |19 december 2014 |En met 29 februari 2016 |
| 0.9.1-prelease |19 december 2014 |En met 29 februari 2016 |
| 0.9.0-prelease |10 december 2014 |En met 29 februari 2016 |

## <a name="faq"></a>Veelgestelde vragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zie ook
Zie [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) service-pagina voor meer informatie over Cosmos db.

