---
title: 'Azure Cosmos DB: SQL .NET core-API, SDK-& resources'
description: Meer informatie over de SQL .NET Core-API en SDK, inclusief release datums, buiten gebruik stellen datums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB .NET Core SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/22/2018
ms.author: sngun
ms.openlocfilehash: 1b1e9338f92aaefab8e7754139066ac03e8b8213
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142621"
---
# <a name="azure-cosmos-db-net-core-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB .NET Core SDK voor SQL-API: Release opmerkingen en bronnen
> [!div class="op_single_selector"]
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [.NET Standard](sql-api-sdk-dotnet-standard.md)
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-Wijzigingenfeed](sql-api-sdk-dotnet-changefeed.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Async Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST-resourceprovider](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](sql-api-query-reference.md)
> * [Bulk-uitvoerder-.NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [Bulk-uitvoerder-java](sql-api-sdk-bulk-executor-java.md)

| |  |
|---|---|
|**SDK downloaden**| [NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)|
|**API-documentatie**|[.NET API-referentiedocumentatie](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Voorbeelden**|[.NET-codevoorbeelden](sql-api-dotnet-samples.md)|
|**Aan de slag**|[Aan de slag met de Azure Cosmos DB .NET](sql-api-sdk-dotnet.md)|
|**Zelfstudie-web-app**|[Ontwikkeling van webtoepassingen met Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Huidige ondersteunde framework**|[.NET standard 1.6 en .NET Standard 1.5](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Releaseopmerkingen

> [!NOTE]
> Als u .NET Core gebruikt, raadpleegt u de meest recente versie 3. x van de [.NET SDK](sql-api-sdk-dotnet-standard.md), die de .net-standaard richt. 

### <a name="a-name251251"></a><a name="2.5.1"/>2.5.1

* De versie van de SDK van het systeem .net. http komt nu overeen met wat er in het NuGet-pakket is gedefinieerd.
* Schrijf aanvragen om te leiden naar een andere regio als het oorspronkelijke item mislukt.
* Beleid voor opnieuw proberen van sessie voor schrijf aanvraag toevoegen.

### <a name="a-name241241"></a><a name="2.4.1"/>2.4.1

* Oplossingen voor het traceren van de race voorwaarde voor query's die lege pagina's hebben veroorzaakt

### <a name="a-name240240"></a><a name="2.4.0"/>2.4.0

* Verhoogde decimale nauwkeurigheids grootte voor LINQ-query's.
* Nieuwe klassen CompositePath, CompositePathSortOrder, SpatialSpec, SpatialType en PartitionKeyDefinitionVersion toegevoegd
* TimeToLivePropertyPath toegevoegd aan DocumentCollection
* CompositeIndexes en SpatialIndexes toegevoegd aan IndexPolicy
* De versie is toegevoegd aan PartitionKeyDefinition
* Geen toegevoegd aan PartitionKey

### <a name="a-name230230"></a><a name="2.3.0"/>2.3.0

 * IdleTcpConnectionTimeout, OpenTcpConnectionTimeout, MaxRequestsPerTcpConnection en MaxTcpConnectionsPerEndpoint zijn toegevoegd aan Connection Policy.
 
### <a name="a-name223223"></a><a name="2.2.3"/>2.2.3

* Verbeteringen voor diagnostische gegevens

### <a name="a-name222222"></a><a name="2.2.2"/>2.2.2

* De instelling ' POCOSerializationOnly ' voor de omgevings variabele is toegevoegd.

* DocumentDB. Spatial. SQL. dll is verwijderd en nu opgenomen in micro soft. Azure. Documents. ServiceInterop. dll

### <a name="a-name221221"></a><a name="2.2.1"/>2.2.1

* Verbetering van de logica voor opnieuw proberen tijdens een failover voor procedurele uitvoer-aanroepen.

* DocumentClientEventSource Singleton gemaakt. 

* Fix GatewayAddressCache-time-out heeft niet gehonoreerd Connection Policy RequestTimeout.

### <a name="a-name220220"></a><a name="2.2.0"/>2.2.0

* Voor direct/TCP-transport diagnostiek, toegevoegde TransportException, een intern uitzonderings type van de SDK. Bij uitzonderings berichten wordt in dit type aanvullende informatie afgedrukt voor het oplossen van problemen met client connectiviteit.

* Er is een nieuwe constructor overbelasting toegevoegd die een HttpMessageHandler, een HTTP-handlertoewijzing die moet worden gebruikt voor het verzenden van httpclient maakt-aanvragen (bijvoorbeeld HttpClientHandler).

* Los de fout op waarbij de header met Null-waarden niet goed wordt afgehandeld.

* Verbeterde validatie van de verzamelings cache.

### <a name="a-name213213"></a><a name="2.1.3"/>2.1.3

* Bijgewerkte System.Net.Security naar 4.3.2.

### <a name="a-name212212"></a><a name="2.1.2"/>2.1.2

* Diagnostische tracering verbeteringen.

### <a name="a-name211211"></a><a name="2.1.1"/>2.1.1

* Meer flexibiliteit toegevoegd aan meerdere regio's aanvraag tijdelijke fouten.

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* Toegevoegde meerdere regio's schrijven ondersteuning.
* Cross-partitie prestatieverbeteringen voor query's met boven- en MaxBufferedItemCount.

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* Ondersteuning voor toegevoegde aanvraag annuleren.
* Toegevoegde SetCurrentLocation naar ConnectionPolicy, die automatisch wordt ingevuld de gewenste locaties op basis van de regio.
* Probleem opgelost in een Cross-partitie query's met Min/Max en een filter op dat overeenkomt met geen documenten op een afzonderlijke partitie.
* DocumentClient methoden hebben nu pariteit met IDocumentClient.
* Bijgewerkte direct TCP-transport-stack om te beperken van het aantal verbindingen tot stand gebracht.
* Ondersteuning toegevoegd voor Direct via TCP-modus voor niet-Windows-clients.

### <a name="a-name200-preview2200-preview2"></a><a name="2.0.0-preview2"/>2.0.0-preview2

* Ondersteuning voor toegevoegde aanvraag annuleren.
* Toegevoegde SetCurrentLocation naar ConnectionPolicy, die automatisch wordt ingevuld de gewenste locaties op basis van de regio.
* Probleem opgelost in een Cross-partitie query's met Min/Max en een filter op dat overeenkomt met geen documenten op een afzonderlijke partitie.

### <a name="a-name200-preview200-preview"></a><a name="2.0.0-preview"/>2.0.0-Preview

* DocumentClient methoden hebben nu pariteit met IDocumentClient.
* Bijgewerkte direct TCP-transport-stack om te beperken van het aantal verbindingen tot stand gebracht.
* Ondersteuning toegevoegd voor Direct via TCP-modus voor niet-Windows-clients.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0

* Toegevoegde ConsistencyLevel eigenschap FeedOptions.
* Toegevoegde JsonSerializerSettings RequestOptions en FeedOptions.
* Toegevoegde EnableReadRequestsFallback naar ConnectionPolicy.

### <a name="a-name191191"></a><a name="1.9.1"/>1.9.1

* Vaste KeyNotFoundException voor cross-partitie OrderBy-query's in de hoek gevallen.
* Er is een fout opgelost waarbij het kenmerk JsonProperty in de component SELECT voor LINQ-query's niet wordt nageleefd.

### <a name="a-name182182"></a><a name="1.8.2"/>1.8.2

* Er is een probleem opgelost waarbij bepaalde race voorwaarden worden bereikt. Dit resulteert in een regel matig ' micro soft. Azure. Documents. NotFoundException: De Lees sessie is niet beschikbaar voor de invoer sessie token fouten wanneer consistentie niveau van de sessie wordt gebruikt.

### <a name="a-name181181"></a><a name="1.8.1"/>1.8.1

* Regressie opgelost waarbij FeedOptions.MaxItemCount = -1 heeft veroorzaakt een System.ArithmeticException: het formaat van pagina negatief is.
* Een nieuwe functie ToString() aan QueryMetrics toegevoegd.
* Beschikbaar gemaakte partitiestatistieken voor het lezen van verzamelingen.
* PartitionKey-eigenschap toegevoegd aan ChangeFeedOptions.
* Kleine oplossingen voor problemen.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
 
 * Voegt de mogelijkheid om op te geven unieke indexen voor de documenten met behulp van de eigenschap UniqueKeyPolicy op de documentcollection maakt.
 * Een opgelost waar zijn de aangepaste instellingen voor JsonSerializer niet wordt herkend voor sommige query's en het uitvoeren van een opgeslagen procedure.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
 
 * Wijziging van de Azure DocumentDB in Azure Cosmos DB in de API-verwijzing huisstijl documentatie, informatie over de metagegevens in assembly's en het NuGet-pakket.
 * Diagnostische gegevens en latentie van de reactie op aanvragen die worden verzonden met directe connectiviteitsmodus weergeven. De namen van eigenschappen zijn RequestDiagnosticsString en RequestLatency op ResourceResponse klasse.
 * Deze SDK-versie vereist de meest recente versie van Azure Cosmos DB Emulator beschikbaar voor downloaden van https://aka.ms/cosmosdb-emulator.
 
### <a name="a-name160160"></a><a name="1.6.0"/>1.6.0

* Verschillende betrouwbaarheidsoplossingen en verbeteringen toegevoegd.

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1 

* Interne wijzigingen met betrekking tot het ondersteunen van [Microsoft.Azure.Graphs](https://docs.microsoft.com/azure/cosmos-db/graph-sdk-dotnet)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0 

* Ondersteuning toegevoegd voor PartitionKeyRangeId als een FeedOption voor het vaststellen van de resultaten van de query op een specifieke partitie sleutelbereik-waarde.
* Ondersteuning toegevoegd voor StartTime als een ChangeFeedOption te bekijken om de wijzigingen na deze periode.

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1

*   Een probleem opgelost in de klasse JsonSerializable dat leiden een uitzondering voor stack overflow tot kan.

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0

*   Ondersteuning toegevoegd voor aangepaste JsonSerializerSettings op te geven bij het instantiëren van een [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet) exemplaar.

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2

*   Ondersteunt .NET Standard 1.5 als een van de doel-frameworks.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1

*   Een probleem dat beïnvloed x64 opgelost machines die geen ondersteuning voor SSE4 instructie en SEHException throw bij het uitvoeren van Azure Cosmos DB-query's.

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0

*   Er is ondersteuning toegevoegd voor een nieuwe consistentieniveau ConsistentPrefix genoemd.
*   Er is ondersteuning toegevoegd voor query metrische gegevens voor afzonderlijke partities.
*   Er is ondersteuning toegevoegd voor het beperken van de grootte van het vervolgtoken voor query's.
*   Ondersteuning toegevoegd voor meer gedetailleerde tracering van mislukte aanvragen.
*   Sommige prestatieverbeteringen doorgevoerd in de SDK.

### <a name="a-name122122"></a><a name="1.2.2"/>1.2.2

* Er is een probleem dat de opgegeven FeedOptions voor statistische query's PartitionKey-waarde genegeerd opgelost.
* Een probleem opgelost in transparante verwerking van partitie management tijdens halverwege vlucht over meerdere partities Order By queryuitvoering.

### <a name="a-name121121"></a><a name="1.2.1"/>1.2.1

* Er is een probleem waardoor impassen in enkele van de asynchrone communicatiepoort API's bij gebruik binnen de context van ASP.NET opgelost.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0

* Problemen aan te brengen SDK beter bestand is tegen automatische failover onder bepaalde omstandigheden.

### <a name="a-name112112"></a><a name="1.1.2"/>1.1.2

* Oplossing voor een probleem dat af en toe leidt tot een webexcept: De externe naam kan niet worden omgezet.
* De ondersteuning voor het lezen van een getypte document rechtstreeks door toe te voegen nieuwe overloads naar ReadDocumentAsync API toegevoegd.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* LINQ is ondersteuning toegevoegd voor aggregatie-query's (aantal, MIN, MAX, som en gemiddelde).
* Oplossing voor een probleem voor het lekken van geheugen voor de ConnectionPolicy-object dat is veroorzaakt door het gebruik van de gebeurtenis-handler.
* Correctie voor een probleem waarbij de UpsertAttachmentAsync niet werken is wanneer ETag is gebruikt.
* Oplossing voor een probleem waarbij meerdere partitie volgorde door de voortzetting van de query niet werkte bij het sorteren van tekenreeksveld.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Er is ondersteuning toegevoegd voor aggregatie-query's (aantal, MIN, MAX, som en gemiddelde). Zie [ondersteuning voor aggregatie](sql-query-aggregates.md).
* Het minimale doorvoer op gepartitioneerde verzamelingen van 10,100 RU/s 2500 RU/s verlaagd.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

De Azure Cosmos DB .NET Core SDK kunt u bouwen snelle, platformoverschrijdende [ASP.NET Core](https://www.asp.net/core) en [.NET Core](https://www.microsoft.com/net/core#windows) apps uit te voeren op Windows, Mac en Linux. De nieuwste versie van de Azure Cosmos DB .NET Core-SDK is volledig [Xamarin](https://www.xamarin.com) compatibel en worden gebruikt voor het bouwen van toepassingen die zijn gericht op iOS, Android en Mono (Linux).  

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-Preview

De Azure Cosmos DB .NET Core Preview SDK kunt u bouwen snelle, platformoverschrijdende [ASP.NET Core](https://www.asp.net/core) en [.NET Core](https://www.microsoft.com/net/core#windows) apps uit te voeren op Windows, Mac en Linux.

De Azure Cosmos DB .NET Core Preview SDK heeft de functiepariteit met de meest recente versie van de [Azure Cosmos DB .NET SDK](sql-api-sdk-dotnet.md) en biedt ondersteuning voor het volgende:
* Alle [verbindings modi](performance-tips.md#networking): Gateway modus, direct TCP en direct HTTPs.
* Alle [consistentie niveaus](consistency-levels.md): Sterk, sessie, gebonden veroudering en uiteindelijk.
* [Gepartitioneerde verzamelingen](partition-data.md).
* [Databaseaccounts voor meerdere regio's en geo-replicatie](distribute-data-globally.md).

Als u vragen hebt over deze SDK, post naar [stack overflow](https://stackoverflow.com/questions/tagged/azure-documentdb)of een probleem in de [github-opslag plaats](https://github.com/Azure/azure-documentdb-dotnet/issues).

## <a name="release--retirement-dates"></a>Uittredings datums &
Micro soft biedt een kennisgeving van ten minste **twaalf maanden** voor het buiten gebruik stellen van een SDK om de overgang naar een nieuwere/ondersteunde versie te versoepelen.

Nieuwe functies en functionaliteiten en Optima Lise ringen worden alleen toegevoegd aan de huidige SDK. het wordt daarom aangeraden dat u zo snel mogelijk een upgrade naar de nieuwste SDK-versie uitvoert. 

Aanvragen voor het Azure Cosmos DB met behulp van een buiten gebruik gestelde SDK worden geweigerd door de service.

> [!WARNING]
> Alle versies **1. x** van de .net core SDK voor de SQL-API worden op **30 augustus 2020**gestopt.
> 
>
<br/>


| Version | Releasedatum | Vervaldatum |
| --- | --- | --- |
| [2.5.1](#2.5.1) |02, 2019 |--- |
| [2.4.1](#2.4.1) |20 juni 2019 |--- |
| [2.4.0](#2.4.0) |05 mei 2019 |--- |
| [2.3.0](#2.3.0) |April 04, 2019 |--- |
| [2.2.3](#2.2.3) |11 maart 2019 |--- |
| [2.2.2](#2.2.2) |6 februari 2019 |--- |
| [2.2.1](#2.2.1) |24 december 2018 |--- |
| [2.2.0](#2.2.0) |7 december 2018 |--- |
| [2.1.3](#2.1.3) |15 oktober 2018 |--- |
| [2.1.2](#2.1.2) |04 oktober 2018 |--- |
| [2.1.1](#2.1.1) |27 september 2018 |--- |
| [2.1.0](#2.1.0) |21 september 2018 |--- |
| [2.0.0](#2.0.0) |07 september 2018 |--- |
| [1.9.1](#1.9.1) |09 maart 2018 |30 augustus 2020 |
| [1.8.2](#1.8.2) |21 februari 2018 |30 augustus 2020 |
| [1.8.1](#1.8.1) |05 februari 2018 |30 augustus 2020 |
| [1.7.1](#1.7.1) |16 november 2017 |30 augustus 2020 |
| [1.7.0](#1.7.0) |10 november 2017 |30 augustus 2020 |
| [1.6.0](#1.6.0) |17 oktober 2017 |30 augustus 2020 |
| [1.5.1](#1.5.1) |02 oktober 2017 |30 augustus 2020 |
| [1.5.0](#1.5.0) |10 augustus 2017 |30 augustus 2020 | 
| [1.4.1](#1.4.1) |07 augustus 2017 |30 augustus 2020 |
| [1.4.0](#1.4.0) |02 augustus 2017 |30 augustus 2020 |
| [1.3.2](#1.3.2) |12 juni 2017 |30 augustus 2020 |
| [1.3.1](#1.3.1) |23 mei 2017 |30 augustus 2020 |
| [1.3.0](#1.3.0) |10 mei 2017 |30 augustus 2020 |
| [1.2.2](#1.2.2) |19 april 2017 |30 augustus 2020 |
| [1.2.1](#1.2.1) |29 maart 2017 |30 augustus 2020 |
| [1.2.0](#1.2.0) |25 maart 2017 |30 augustus 2020 |
| [1.1.2](#1.1.2) |20 maart 2017 |30 augustus 2020 |
| [1.1.1](#1.1.1) |14 maart 2017 |30 augustus 2020 |
| [1.1.0](#1.1.0) |16 februari 2017 |30 augustus 2020 |
| [1.0.0](#1.0.0) |21 december 2016 |30 augustus 2020 |
| [0.1.0-preview](#0.1.0-preview) |15 november 2016 |En met 31 december 2016 |

## <a name="see-also"></a>Zie ook
Zie voor meer informatie over Cosmos DB, [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) servicepagina.

