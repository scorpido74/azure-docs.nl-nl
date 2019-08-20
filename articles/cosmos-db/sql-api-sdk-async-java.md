---
title: 'Azure Cosmos DB: SQL async Java API, SDK & resources'
description: Meer informatie over de SQL Async Java-API en SDK, inclusief release datums, buiten gebruik stellen datums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB SQL Async Java SDK.
author: moderakh
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: java
ms.topic: reference
ms.date: 07/01/2019
ms.author: moderakh
ms.openlocfilehash: 3bee473adde9c4762cf400dff6190eb76a4cc8d7
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624688"
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB asynchrone Java-SDK voor SQL-API: Release opmerkingen en bronnen
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-Wijzigingenfeed](sql-api-sdk-dotnet-changefeed.md)
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

De SQL-SDK voor Java-API asynchrone wijkt af van de Java-SDK van de SQL-API door te geven van asynchrone bewerkingen met ondersteuning van de [Netty bibliotheek](https://netty.io/). De vooraf bestaande [SQL API Java SDK](sql-api-sdk-java.md) biedt geen ondersteuning voor asynchrone bewerkingen. 

| |  |
|---|---|
| **SDK downloaden** | [Maven](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb) |
|**API-documentatie** |[Java API-referentiedocumentatie](https://docs.microsoft.com/java/api/com.microsoft.azure.cosmosdb.rx.asyncdocumentclient?view=azure-java-stable) | 
|**Bijdragen aan de SDK** | [GitHub](https://github.com/Azure/azure-cosmosdb-java) | 
|**Aan de slag** | [Aan de slag met de Async Java-SDK](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started) | 
|**Voorbeeld van code** | [GitHub](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)| 
| **Tips voor prestaties**| [GitHub Leesmij](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)| 
| **Minimaal ondersteunde runtime**|[JDK 8](https://aka.ms/azure-jdks) | 

## <a name="release-notes"></a>Releaseopmerkingen

### <a name="a-name250250"></a><a name="2.5.0"/>2.5.0
* TCP-modus nu standaard ingeschakeld
* Metrische query gegevens in Kruis partitie retourneert nu alle partities
* Global Strong werkt nu goed
* Failover voor query's heeft niet de juiste pogingen voor multi-master
* Afhankelijkheids bumpers voor beveiligings hotfixes

### <a name="a-name245245"></a><a name="2.4.5"/>2.4.5
* Bugfix voor ondersteuning van hash v2

### <a name="a-name243243"></a><a name="2.4.3"/>2.4.3
* Bugfix voor bron lekkage op client # Close () ([github #88](https://github.com/Azure/azure-cosmosdb-java/issues/88)).

### <a name="a-name242242"></a><a name="2.4.2"/>2.4.2
* Er zijn vervolg token ondersteuning toegevoegd voor kruis partitie query's.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1
* Er zijn fouten in de directe modus opgelost.
* Verbeterde logboek registratie in de directe modus.
* Verbeterd verbindings beheer.

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0
* Connectiviteit voor directe modus is nu algemeen beschikbaar (GA). Zie [Azure-cosmosdb-Java](https://github.com/Azure/azure-cosmosdb-java) github repository voor een voor beeld dat gebruikmaakt van connectiviteit in de directe modus.
* Er is ondersteuning toegevoegd voor QueryMetrics.
* De Api's die Java. util. verzameling accepteren en waarvan de volg orde belang rijk is om Java. util. list te accepteren, zijn gewijzigd. Nu Connection Policy # getPreferredLocations (), JsonSerialization en PartitionKey (.) acceptatie lijst.

### <a name="a-name240-beta-1240-beta-1"></a><a name="2.4.0-beta-1"/>2.4.0-bèta-1
* Er is ondersteuning toegevoegd voor de connectiviteit van de directe modus.
* De Api's die Java. util. verzameling accepteren en waarvan de volg orde belang rijk is om Java. util. list te accepteren, zijn gewijzigd.
  Nu Connection Policy # getPreferredLocations (), JsonSerialization en PartitionKey (.) acceptatie lijst.
* Er is een sessie fout opgelost voor document query in de gateway modus.
* Bijgewerkte afhankelijkheden (Netty 0.4.20 [github #79](https://github.com/Azure/azure-cosmosdb-java/issues/79), RxJava 1.3.8).

### <a name="a-name231231"></a><a name="2.3.1"/>2.3.1
* Herstelt het verwerken van zeer grote query Responses.
* Hiermee wordt het verwerken van bron tokens bij het instantiëren van client ([github #78](https://github.com/Azure/azure-cosmosdb-java/issues/78)) opgelost.
* Geüpgradede kwets bare afhankelijkheids Jackson-DataBind ([github #77](https://github.com/Azure/azure-cosmosdb-java/pull/77)).

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0
* Een resource geheugenlek bug opgelost.
* Er is ondersteuning toegevoegd voor MultiPolygon
* Ondersteuning toegevoegd voor aangepaste kopteksten in RequestOptions.

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2
* Een bug pakketten verholpen.

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1
* Een NFE opgelost in het pad voor schrijven probeer het opnieuw.
* Eindpunt management, een bug NFE vast.
* Geüpgradede kwets bare afhankelijkheden ([GitHub #68](https://github.com/Azure/azure-cosmosdb-java/issues/68)).
* Ondersteuning toegevoegd voor Netty netwerk logboekregistratie voor het oplossen van problemen.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0
* Er is ondersteuning toegevoegd voor meerdere regio's schrijven.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0
* Er is ondersteuning toegevoegd voor de Proxy.
* Ondersteuning toegevoegd voor bronautorisatie token.
* Er is een fout opgelost bij het verwerken van grote partitie sleutels ([GitHub #63](https://github.com/Azure/azure-cosmosdb-java/issues/63)).
* Documentatie is verbeterd.
* De SDK is geherstructureerd in gedetailleerdere modules.

### <a name="a-name201201"></a><a name="2.0.1"/>2.0.1
* Er is een fout opgelost voor niet-Engelse land instellingen ([GitHub #51](https://github.com/Azure/azure-cosmosdb-java/issues/51)).
* Toegevoegde helpermethoden in Conflict Resource.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0
* Het vervangen van org. json afhankelijkheid door middel van de oorzaak van de prestaties en licenties ([GitHub #29](https://github.com/Azure/azure-cosmosdb-java/issues/29)).
* Afgeschafte OfferV2 klasse is verwijderd.
* Toegevoegde accessor-methode voor het aanbod klasse voor de inhoud van de doorvoer.
* Een methode in Document/Resource org.json typen gewijzigd om terug te keren een jackson objecttype retourneren.
* methode getObject(.) van klassen uitbreiden JsonSerializable gewijzigd om terug te keren een ObjectNode jackson typt.
* getCollection(.) methode om te retourneren van de verzameling van ObjectNode gewijzigd.
* Verwijderde JsonSerializable subklassen constructors met org.json.JSONObject arg.
* JsonSerializable.toJson (SerializationFormattingPolicy.Indented) maakt nu gebruik van twee spaties voor inspringen.
  
### <a name="a-name102102"></a><a name="1.0.2"/>1.0.2
* Er is ondersteuning toegevoegd voor de unieke Index beleid.
* Ondersteuning toegevoegd voor het beperken van de reactie voortzetting van token grootte in de opties voor invoer.
* Ondersteuning toegevoegd voor partitie splitsen in meerdere Partitiequery.
* Er is een fout opgelost in JSON-time-serialisatie ([GitHub #32](https://github.com/Azure/azure-cosmosdb-java/issues/32)).
* Een bug opgelost in Json-serialisatie voor enum.
* Er is een fout opgelost in het beheer van documenten van de grootte van 2 MB ([GitHub #33](https://github.com/Azure/azure-cosmosdb-java/issues/33)).
* Afhankelijkheids com. fasterxml. Jackson. core: Jackson-DataBind is bijgewerkt naar 2.9.5 vanwege een fout ([Jackson-DataBind: GitHub #1599](https://github.com/FasterXML/jackson-databind/issues/1599))
* Afhankelijk van een fout ([rxjava-extra's: rxjava-extra's is er een upgrade uitgevoerd naar 0.8.0.17. GitHub #30](https://github.com/davidmoten/rxjava-extras/issues/30)).
* De beschrijving van de metagegevens in het pom-bestand bijgewerkt in verband met inline met de rest van documentatie zijn.
* Syntaxis verbetering ([GitHub #41](https://github.com/Azure/azure-cosmosdb-java/issues/41)), ([github #40](https://github.com/Azure/azure-cosmosdb-java/issues/40)).

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Toegevoegde ondersteuning voor back-druk in de query.
* Ondersteuning toegevoegd voor partitie-id sleutelbereik in de query.
* Oplossing om een grotere voortzettings token in de aanvraag header toe te staan (bugfix GitHub #24).
* Een upgrade uitgevoerd naar 4.1.22.Final om ervoor te zorgen JVM-netty afhankelijkheid afgesloten nadat de hoofdthread is voltooid.
* Om te voorkomen dat sessietoken wordt doorgegeven bij het lezen van de master-resources oplossen.
* Meer voorbeelden toegevoegd.
* Meer scenario's voor benchmarking toegevoegd.
* Header-bestanden van de vaste Java voor het genereren van de juiste java doc-bestand.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA-SDK met end-to-end-ondersteuning voor het gebruik van i/o-niet-blokkerende de [Netty bibliotheek](https://netty.io/) in de modus van de gateway. 

## <a name="release-and-retirement-dates"></a>Release-en buiten gebruik stellen
Microsoft biedt melding ten minste **12 maanden** voorafgaand aan buiten gebruik stellen van een SDK soepel te verwerken de overgang naar een nieuwere/ondersteunde versie.

Nieuwe functies en functionaliteit en -optimalisatie worden alleen toegevoegd aan de huidige SDK. Daarom wordt het aanbevolen dat u altijd een upgrade naar de nieuwste versie van de SDK zo vroeg mogelijk uitvoert.

Een aanvraag voor het Cosmos DB met behulp van een buiten gebruik gestelde SDK worden geweigerd door de service.

<br/>

| Versie | Releasedatum | Vervaldatum |
| --- | --- | --- |
| [2.4.3](#2.4.3) |5 maart 2019|--- |
| [2.4.2](#2.4.2) |1 maart 2019|--- |
| [2.4.1](#2.4.1) |20 februari 2019|--- |
| [2.4.0](#2.4.0) |8 februari 2019|--- |
| [2.4.0-beta-1](#2.4.0-beta-1) |4 februari 2019|--- |
| [2.3.1](#2.3.1) |15 januari 2019|--- |
| [2.3.0](#2.3.0) |29 november 2018|--- |
| [2.2.2](#2.2.2) |8 november 2018|--- |
| [2.2.1](#2.2.1) |2 november 2018|--- |
| [2.2.0](#2.2.0) |22 september 2018|--- |
| [2.1.0](#2.1.0) |5 september 2018|--- |
| [2.0.1](#2.0.1) |16 augustus 2018|--- |
| [2.0.0](#2.0.0) |20 juni 2018|--- |
| [1.0.2](#1.0.2) |18 mei 2018|--- |
| [1.0.1](#1.0.1) |20 april 2018|--- |
| [1.0.0](#1.0.0) |27 februari, 2018|--- |

## <a name="faq"></a>Veelgestelde vragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zie ook
Zie voor meer informatie over Cosmos DB, [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) servicepagina.

