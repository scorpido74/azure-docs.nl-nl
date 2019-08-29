---
title: 'Azure Cosmos DB: SQL .NET API, SDK & resources'
description: Meer informatie over de SQL .NET API en SDK, inclusief release datums, pensioen datums en wijzigingen die zijn aangebracht tussen elke versie van de Azure Cosmos DB .NET SDK.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/09/2018
ms.author: sngun
ms.openlocfilehash: 431ede41e115c82c877041da4a0eb93eacf83cbd
ms.sourcegitcommit: d200cd7f4de113291fbd57e573ada042a393e545
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/29/2019
ms.locfileid: "70142667"
---
# <a name="azure-cosmos-db-net-sdk-for-sql-api-download-and-release-notes"></a>Azure Cosmos DB .NET SDK voor SQL-API: Down load en release-opmerkingen
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET](sql-api-sdk-dotnet-standard.md)
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

| |  |
|---|---|
|**SDK downloaden**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB/)|
|**API-documentatie**|[.NET API-referentiedocumentatie](/dotnet/api/overview/azure/cosmosdb?view=azure-dotnet)|
|**Voorbeelden**|[.NET-codevoorbeelden](https://github.com/Azure/azure-cosmos-dotnet-v2/tree/master/samples)|
|**Aan de slag**|[Aan de slag met de Azure Cosmos DB .NET SDK](sql-api-get-started.md)|
|**Zelfstudie-web-app**|[Ontwikkeling van webtoepassingen met Azure Cosmos DB](sql-api-dotnet-application.md)|
|**Huidige ondersteunde framework**|[Microsoft .NET Framework 4.5](https://www.microsoft.com/download/details.aspx?id=30653)|

## <a name="release-notes"></a>Releaseopmerkingen

> [!NOTE]
> Als u .NET Framework gebruikt, raadpleegt u de meest recente versie 3. x van de [.NET SDK](sql-api-sdk-dotnet-standard.md), die de .net-standaard richt. 

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

* Verbeteringen voor diagnostische tracering

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

### <a name="a-name12201220"></a><a name="1.22.0"/>1.22.0

* Toegevoegde ConsistencyLevel eigenschap FeedOptions.
* Toegevoegde JsonSerializerSettings RequestOptions en FeedOptions.
* Toegevoegde EnableReadRequestsFallback naar ConnectionPolicy.

### <a name="a-name12111211"></a><a name="1.21.1"/>1.21.1

* Vaste KeyNotFoundException voor cross-partitie OrderBy-query's in de hoek gevallen.
* Er is een fout opgelost waarbij het kenmerk JsonProperty in de component SELECT voor LINQ-query's niet wordt nageleefd.

### <a name="a-name12021202"></a><a name="1.20.2"/>1.20.2

* Er is een probleem opgelost waarbij bepaalde race voorwaarden worden bereikt. Dit resulteert in een regel matig ' micro soft. Azure. Documents. NotFoundException: De Lees sessie is niet beschikbaar voor de invoer sessie token fouten wanneer consistentie niveau van de sessie wordt gebruikt.

### <a name="a-name12011201"></a><a name="1.20.1"/>1.20.1

* Regressie opgelost waarbij FeedOptions.MaxItemCount = -1 heeft veroorzaakt een System.ArithmeticException: het formaat van pagina negatief is.
* Een nieuwe functie ToString() aan QueryMetrics toegevoegd.
* Beschikbaar gemaakte partitiestatistieken voor het lezen van verzamelingen.
* PartitionKey-eigenschap toegevoegd aan ChangeFeedOptions.
* Kleine oplossingen voor problemen.

### <a name="a-name11911191"></a><a name="1.19.1"/>1.19.1

* Voegt de mogelijkheid om op te geven unieke indexen voor de documenten met behulp van de eigenschap UniqueKeyPolicy op de documentcollection maakt.
* Een opgelost waar zijn de aangepaste instellingen voor JsonSerializer niet wordt herkend voor sommige query's en het uitvoeren van een opgeslagen procedure.

### <a name="a-name11901190"></a><a name="1.19.0"/>1.19.0

* Wijziging van de Azure DocumentDB in Azure Cosmos DB in de API-verwijzing huisstijl documentatie, informatie over de metagegevens in assembly's en het NuGet-pakket. 
* Diagnostische gegevens en latentie van de reactie op aanvragen die worden verzonden met directe connectiviteitsmodus weergeven. De namen van eigenschappen zijn RequestDiagnosticsString en RequestLatency op ResourceResponse klasse.
* Deze SDK-versie vereist de meest recente versie van Azure Cosmos DB Emulator beschikbaar voor downloaden van https://aka.ms/cosmosdb-emulator. 

### <a name="a-name11811181"></a><a name="1.18.1"/>1.18.1 

* Interne wijzigingen voor micro soft-vrienden.

### <a name="a-name11801180"></a><a name="1.18.0"/>1.18.0 

* Verschillende betrouwbaarheidsoplossingen en verbeteringen toegevoegd.

### <a name="a-name11701170"></a><a name="1.17.0"/>1.17.0 

* Ondersteuning toegevoegd voor PartitionKeyRangeId als een FeedOption voor het vaststellen van de resultaten van de query op een specifieke partitie sleutelbereik-waarde. 
* Ondersteuning toegevoegd voor StartTime als een ChangeFeedOption te bekijken om de wijzigingen na deze periode.

### <a name="a-name11611161"></a><a name="1.16.1"/>1.16.1
* Een probleem opgelost in de klasse JsonSerializable dat leiden een uitzondering voor stack overflow tot kan.

### <a name="a-name11601160"></a><a name="1.16.0"/>1.16.0
*   Er is een probleem opgelost waarbij de toepassing opnieuw moet worden gecompileerd vanwege de introductie van JsonSerializerSettings als een optionele para meter in de DocumentClient-constructor.
* De DocumentClient-constructor heeft verouderd dat vereiste JsonSerializerSettings als de laatste para meter voor het toestaan van standaard waarden van Connection Policy-en ConsistencyLevel-para meters bij het door geven van de JsonSerializerSettings-para meter.

### <a name="a-name11501150"></a><a name="1.15.0"/>1.15.0
*   Er is ondersteuning toegevoegd voor het opgeven van aangepaste JsonSerializerSettings tijdens het instantiëren van [DocumentClient](/dotnet/api/microsoft.azure.documents.client.documentclient?view=azure-dotnet).

### <a name="a-name11411141"></a><a name="1.14.1"/>1.14.1
*   Er is een probleem opgelost dat betrokken is bij x64-machines die geen ondersteuning bieden voor de SSE4-instructie en een SEHException genereren bij het uitvoeren van Azure Cosmos DB SQL-query's.

### <a name="a-name11401140"></a><a name="1.14.0"/>1.14.0
*   Er is ondersteuning toegevoegd voor een nieuwe consistentieniveau ConsistentPrefix genoemd.
*   Er is ondersteuning toegevoegd voor query metrische gegevens voor afzonderlijke partities.
*   Er is ondersteuning toegevoegd voor het beperken van de grootte van het vervolgtoken voor query's.
*   Ondersteuning toegevoegd voor meer gedetailleerde tracering van mislukte aanvragen.
*   Sommige prestatieverbeteringen doorgevoerd in de SDK.

### <a name="a-name11341134"></a><a name="1.13.4"/>1.13.4
* Functioneel hetzelfde als 1.13.3. Er zijn enkele interne wijzigingen aangebracht.

### <a name="a-name11331133"></a><a name="1.13.3"/>1.13.3
* Functioneel hetzelfde als 1.13.2. Er zijn enkele interne wijzigingen aangebracht.

### <a name="a-name11321132"></a><a name="1.13.2"/>1.13.2
* Er is een probleem dat de opgegeven FeedOptions voor statistische query's PartitionKey-waarde genegeerd opgelost.
* Een probleem opgelost in transparante verwerking van partitie management tijdens halverwege vlucht over meerdere partities Order By queryuitvoering.

### <a name="a-name11311131"></a><a name="1.13.1"/>1.13.1
* Er is een probleem waardoor impassen in enkele van de asynchrone communicatiepoort API's bij gebruik binnen de context van ASP.NET opgelost.

### <a name="a-name11301130"></a><a name="1.13.0"/>1.13.0
* Problemen aan te brengen SDK beter bestand is tegen automatische failover onder bepaalde omstandigheden.

### <a name="a-name11221122"></a><a name="1.12.2"/>1.12.2
* Oplossing voor een probleem dat af en toe leidt tot een webexcept: De externe naam kan niet worden omgezet.
* De ondersteuning voor het lezen van een getypte document rechtstreeks door toe te voegen nieuwe overloads naar ReadDocumentAsync API toegevoegd.

### <a name="a-name11211121"></a><a name="1.12.1"/>1.12.1
* LINQ is ondersteuning toegevoegd voor aggregatie-query's (aantal, MIN, MAX, som en gemiddelde).
* Oplossing voor een probleem voor het lekken van geheugen voor de ConnectionPolicy-object dat is veroorzaakt door het gebruik van de gebeurtenis-handler.
* Correctie voor een probleem waarbij de UpsertAttachmentAsync niet werken is wanneer ETag is gebruikt.
* Oplossing voor een probleem waarbij meerdere partitie volgorde door de voortzetting van de query niet werkte bij het sorteren van tekenreeksveld.

### <a name="a-name11201120"></a><a name="1.12.0"/>1.12.0
* Er is ondersteuning toegevoegd voor aggregatie-query's (aantal, MIN, MAX, som en gemiddelde). Zie [ondersteuning voor aggregatie](sql-query-aggregates.md).
* Het minimale doorvoer op gepartitioneerde verzamelingen van 10,100 RU/s 2500 RU/s verlaagd.

### <a name="a-name11141114"></a><a name="1.11.4"/>1.11.4
* Oplossing voor een probleem waarbij sommige van de Kruis partitie query's zijn mislukt in het 32-bits hostproces.
* Oplossing voor een probleem waarbij de sessie container niet is bijgewerkt met het token voor mislukte aanvragen in de gateway modus.
* Oplossing voor een probleem waarbij een query met UDF-aanroepen in projectie in sommige gevallen mislukt.
* Prestatie verbeteringen aan client zijde voor het verg Roten van de lees-en schrijf doorvoer van de aanvragen.

### <a name="a-name11131113"></a><a name="1.11.3"/>1.11.3
* Oplossing voor een probleem waarbij de sessie container niet is bijgewerkt met het token voor mislukte aanvragen.
* Er is ondersteuning toegevoegd voor de SDK voor gebruik in een 32-bits hostproces. Als u kruis partitie query's gebruikt, wordt de verwerking van 64-bits host aanbevolen voor betere prestaties.
* Verbeterde prestaties voor scenario's met betrekking tot query's met een groot aantal partitie sleutel waarden in een IN-expressie.
* Er zijn verschillende resource quotum statistieken in de ResourceResponse voor het lezen van documenten verzameld wanneer de optie voor PopulateQuotaInfo-aanvragen is ingesteld.

### <a name="a-name11111111"></a><a name="1.11.1"/>1.11.1
* Kleine prestatie oplossing voor de CreateDocumentCollectionIfNotExistsAsync-API geïntroduceerd in 1.11.0.
* Prestatie oplossing in de SDK voor scenario's waarbij een hoge mate van gelijktijdige aanvragen is betrokken.

### <a name="a-name11101110"></a><a name="1.11.0"/>1.11.0
* Ondersteuning voor nieuwe klassen en methoden voor het verwerken van de [wijzigings feed](change-feed.md) van documenten in een verzameling.
* Ondersteuning voor de voortzetting van query's tussen partities en enkele verbeteringen in de prestaties van query's op meerdere partities.
* Toevoeging van CreateDatabaseIfNotExistsAsync-en CreateDocumentCollectionIfNotExistsAsync-methoden.
* LINQ-ondersteuning voor systeem functies: IsDefined, IsNull en IsPrimitive.
* Corrigeer de automatische binplacing van micro soft. Azure. Documents. ServiceInterop. dll en DocumentDB. Spatial. SQL. dll-assembly's in de map bin van de toepassing wanneer u het Nuget-pakket gebruikt met projecten die project. json-hulp programma hebben.
* Ondersteuning voor het verzenden van ETW-traceringen aan de client zijde die nuttig kunnen zijn in scenario's voor fout opsporing.

### <a name="a-name11001100"></a><a name="1.10.0"/>1.10.0
* Ondersteuning voor directe connectiviteit toegevoegd voor gepartitioneerde verzamelingen.
* Verbeterde prestaties voor het consistentie niveau van de gebonden veroudering.
* Toegevoegde veelhoek en LineString DataTypes tijdens het instellen van de verzameling indexeringsbeleid voor geofencing ruimtelijke query's.
* De LINQ-ondersteuning voor StringEnumConverter, IsoDateTimeConverter en UnixDateTimeConverter is toegevoegd tijdens het vertalen van predikaten.
* Verschillende SDK-fout oplossingen.

### <a name="a-name195195"></a><a name="1.9.5"/>1.9.5
* Er is een probleem opgelost dat de volgende NotFoundException veroorzaakt: De Lees sessie is niet beschikbaar voor het token voor de invoer sessie. Deze uitzonde ring is in sommige gevallen opgetreden bij het uitvoeren van een query voor de Lees regio van een geografisch gedistribueerd account.
* De eigenschap ResponseStream is beschikbaar in de klasse ResourceResponse, waarmee direct toegang tot de onderliggende stroom mogelijk is vanuit een antwoord.

### <a name="a-name194194"></a><a name="1.9.4"/>1.9.4
* De ResourceResponse-, FeedResponse-, StoredProcedureResponse-en MediaResponse-klassen zijn aangepast om de bijbehorende open bare interface te implementeren, zodat deze kunnen worden gesimuleerd voor test gerichte implementatie (TDD).
* Er is een probleem opgelost dat de header van een ongeldige partitie sleutel heeft veroorzaakt bij het gebruik van een aangepast JsonSerializerSettings-object voor het serialiseren van gegevens.

### <a name="a-name193193"></a><a name="1.9.3"/>1.9.3
* Er is een probleem opgelost dat ertoe leidde dat langlopende query's mislukken met fout: Het autorisatie token is niet geldig op de huidige tijd.
* Er is een probleem opgelost waarbij de oorspronkelijke SqlParameterCollection is verwijderd uit de bovenste/volg orde van query's op meerdere partities.

### <a name="a-name192192"></a><a name="1.9.2"/>1.9.2
* Er is ondersteuning toegevoegd voor parallelle query's voor gepartitioneerde verzamelingen.
* Er is ondersteuning toegevoegd voor cross-Partition BY-en TOP-query's voor gepartitioneerde verzamelingen.
* De ontbrekende verwijzingen naar DocumentDB. Spatial. SQL. dll en micro soft. Azure. Documents. ServiceInterop. dll die vereist zijn voor het verwijzen naar een Azure Cosmos DB project met een verwijzing naar het Azure Cosmos DB Nuget-pakket zijn opgelost.
* De mogelijkheid om para meters van verschillende typen te gebruiken is opgelost bij het gebruik van door de gebruiker gedefinieerde functies in LINQ. 
* Er is een fout opgelost voor globaal gerepliceerde accounts waar Upsert-aanroepen werden gericht op het lezen van locaties in plaats van schrijf locaties.
* Er zijn methoden toegevoegd aan de IDocumentClient-interface die ontbreekt: 
  * Methode UpsertAttachmentAsync waarbij mediaStream en opties worden gebruikt als para meters
  * Methode CreateAttachmentAsync die opties als para meter gebruikt
  * Methode CreateOfferQuery die querySpec als para meter gebruikt.
* Niet-verzegelde open bare klassen die worden weer gegeven in de IDocumentClient-interface.

### <a name="a-name180180"></a><a name="1.8.0"/>1.8.0
* De ondersteuning voor databaseaccounts voor meerdere regio's toegevoegd.
* Er is ondersteuning toegevoegd voor het opnieuw uitvoeren van vertraagde aanvragen.  Gebruiker kan het aantal nieuwe pogingen en de maximale wacht tijd aanpassen door de eigenschap Connection Policy. RetryOptions te configureren.
* Er is een nieuwe IDocumentClient-interface toegevoegd waarmee de hand tekeningen van alle DocumentClient-eigenschappen en-methoden worden gedefinieerd.  Als onderdeel van deze wijziging zijn ook extensie methoden gewijzigd waarmee de methoden IQueryable en IOrderedQueryable worden gemaakt voor de klasse DocumentClient zelf.
* De configuratie optie is toegevoegd om de Service Point. ConnectionLimit in te stellen voor een opgegeven Azure Cosmos DB-eind punt-URI.  Gebruik Connection Policy. MaxConnectionLimit om de standaard waarde te wijzigen, die is ingesteld op 50.
* Afgeschafte IPartitionResolver en de implementatie ervan.  Ondersteuning voor IPartitionResolver is nu verouderd. Het is raadzaam gepartitioneerde verzamelingen te gebruiken voor hogere opslag en door voer.

### <a name="a-name171171"></a><a name="1.7.1"/>1.7.1
* Er is een overbelasting toegevoegd aan de op URI gebaseerde ExecuteStoredProcedureAsync-methode waarbij RequestOptions als para meter wordt gebruikt.

### <a name="a-name170170"></a><a name="1.7.0"/>1.7.0
* Toegevoegde tijd naar live (TTL) ondersteuning voor documenten.

### <a name="a-name163163"></a><a name="1.6.3"/>1.6.3
* Er is een probleem opgelost in de Nuget-verpakking van de .NET SDK om deze te verpakken als onderdeel van een Azure-Cloud service oplossing.

### <a name="a-name162162"></a><a name="1.6.2"/>1.6.2
* Geïmplementeerd [gepartitioneerde verzamelingen](partition-data.md) en [niveau van de gebruiker gedefinieerde prestaties](performance-levels.md). 

### <a name="a-name153153"></a><a name="1.5.3"/>1.5.3
* **[Fixed]** Query's uitvoeren Azure Cosmos DB eind punt genereert: 'System.Net.Http.HttpRequestException: Fout bij het kopiëren van inhoud naar een stream.

### <a name="a-name152152"></a><a name="1.5.2"/>1.5.2
* Uitgebreide LINQ-ondersteuning, inclusief nieuwe Opera tors voor paginering, voorwaardelijke expressies en bereik vergelijking.
  * Operator nemen om het SELECTIEve gedrag in LINQ in te scha kelen
  * De CompareTo-operator voor het inschakelen van vergelijkingen van het teken reeks bereik
  * Voorwaardelijke (?) en Coalesce-Opera tors (?)
* **[Fixed]** ArgumentOutOfRangeException bij het combi neren van model projectie met waar-in in een LINQ-query. [#81](https://github.com/Azure/azure-documentdb-dotnet/issues/81)

### <a name="a-name151151"></a><a name="1.5.1"/>1.5.1
* **[Fixed]** Als Select is niet de laatste expressie, wordt door de LINQ-provider aangenomen dat er geen projectie is en is SELECT * onjuist gereproduceerd.  [#58](https://github.com/Azure/azure-documentdb-dotnet/issues/58)

### <a name="a-name150150"></a><a name="1.5.0"/>1.5.0
* Geïmplementeerde Upsert, toegevoegde UpsertXXXAsync-methoden
* Prestatie verbeteringen voor alle aanvragen
* Ondersteuning van LINQ-provider voor de methoden Conditional, Coalesce en CompareTo voor teken reeksen
* **[Fixed]** LINQ-provider--> implementeren bevat de methode voor het genereren van dezelfde SQL zoals op IEnumerable en matrix
* **[Fixed]** BackoffRetryUtility maakt opnieuw gebruik van dezelfde HttpRequestMessage in plaats van een nieuw item maken bij nieuwe poging
* **[Verouderd]** UriFactory. CreateCollection--> moet nu UriFactory. CreateDocumentCollection gebruiken

### <a name="a-name141141"></a><a name="1.4.1"/>1.4.1
* **[Fixed]** Lokalisatie problemen bij het gebruik van niet-en cultuur gegevens, zoals nl-NL, enzovoort. 

### <a name="a-name140140"></a><a name="1.4.0"/>1.4.0
* Op ID'S gebaseerde route ring toegevoegd
  * Nieuwe UriFactory-helper om te helpen bij het bouwen van op id's gebaseerde resource koppelingen
  * Nieuwe Overloads op DocumentClient om URI te nemen
* IsValid () en IsValidDetailed () zijn toegevoegd in LINQ voor georuimtelijk
* Ondersteuning van LINQ-provider verbeterd:
  * **Math** -ABS, BOOGCOS, ASIN, BOOGTAN, plafond, COS, EXP, Floor, log, LOG10, Pow, Round, Sign, Sin, wortel, Tan, afkappen
  * **String** -concat, contains, EndsWith, IndexOf, Count, ToLower, TrimStart, replace, reverse, TrimEnd, StartsWith, subtekenreeks, toupper
  * **Matrix** -concat, bevat, aantal
  * **In** -operator

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Er is ondersteuning toegevoegd voor het wijzigen van het indexerings beleid.
  * Nieuwe methode ReplaceDocumentCollectionAsync in DocumentClient
  * De nieuwe eigenschap IndexTransformationProgress in\<ResourceResponse T > voor het bijhouden van het procentuele verloop van de wijzigingen in het index beleid
  * DocumentCollection. IndexingPolicy is nu onveranderbaar
* Er is ondersteuning toegevoegd voor ruimtelijke indexering en query.
  * Nieuwe micro soft. Azure. Documents. ruimtelijke naam ruimte voor het serialiseren/deserialiseren van ruimtelijke typen zoals Point en veelhoek
  * Nieuwe SpatialIndex-klasse voor het indexeren van geojson-gegevens die zijn opgeslagen in Cosmos DB
* **[Fixed]** Er is een onjuiste SQL-query gegenereerd op basis van een LINQ-expressie [#38](https://github.com/Azure/azure-documentdb-net/issues/38).

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Er is een afhankelijkheid toegevoegd op Newton soft. json v 5.0.7.
* Wijzigingen aangebracht in de ondersteunings volgorde door:
  
  * Ondersteuning van LINQ-provider voor OrderBy () of OrderByDescending ()
  * IndexingPolicy om order by te ondersteunen 
    
    **Mogelijke breuk wijziging** 
    
    Als u bestaande code hebt waarmee verzamelingen worden ingericht met een aangepast indexerings beleid, moet uw bestaande code worden bijgewerkt ter ondersteuning van de nieuwe IndexingPolicy-klasse. Als u geen aangepast indexerings beleid hebt, heeft deze wijziging geen invloed op u.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* Er is ondersteuning toegevoegd voor het partitioneren van gegevens met behulp van de nieuwe HashPartitionResolver-en RangePartitionResolver-klassen en de IPartitionResolver.
* Data contract-serialisatie toegevoegd.
* Toegevoegde GUID-ondersteuning in de LINQ-provider.
* UDF-ondersteuning is toegevoegd in LINQ.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA-SDK

## <a name="release--retirement-dates"></a>Uittredings datums &
Micro soft biedt een kennisgeving van ten minste **twaalf maanden** voor het buiten gebruik stellen van een SDK om de overgang naar een nieuwere/ondersteunde versie te versoepelen.

Nieuwe functies en functionaliteiten en Optima Lise ringen worden alleen toegevoegd aan de huidige SDK. het wordt daarom aangeraden dat u zo snel mogelijk een upgrade naar de nieuwste SDK-versie uitvoert. 

Aanvragen voor het Azure Cosmos DB met behulp van een buiten gebruik gestelde SDK worden geweigerd door de service.

> [!WARNING]
> Alle versies **1. x** van de .NET SDK voor SQL API worden op **30 augustus 2020**ingetrokken.
> 
>
<br/>

| Version | Releasedatum | Vervaldatum |
| --- | --- | --- |
| [2.5.1](#2.5.1) |02, 2019 |--- |
| [2.4.1](#2.4.1) |20 juni 2019 |--- |
| [2.4.0](#2.4.0) |05 mei 2019 |--- |
| [2.3.0](#2.3.0) |April 04, 2019 |--- |
| [2.2.3](#2.2.3) |11 februari 2019 |--- |
| [2.2.2](#2.2.2) |6 februari 2019 |--- |
| [2.2.1](#2.2.1) |24 december 2018 |--- |
| [2.2.0](#2.2.0) |7 december 2018 |--- |
| [2.1.3](#2.1.3) |15 oktober 2018 |--- |
| [2.1.2](#2.1.2) |04 oktober 2018 |--- |
| [2.1.1](#2.1.1) |27 september 2018 |--- |
| [2.1.0](#2.1.0) |21 september 2018 |--- |
| [2.0.0](#2.0.0) |07 september 2018 |--- |
| [1.22.0](#1.22.0) |19 april 2018 | 30 augustus 2020 |
| [1.21.1](#1.20.1) |09 maart 2018 |30 augustus 2020 |
| [1.20.2](#1.20.1) |21 februari 2018 |30 augustus 2020 |
| [1.20.1](#1.20.1) |05 februari 2018 |30 augustus 2020 |
| [1.19.1](#1.19.1) |16 november 2017 |30 augustus 2020 |
| [1.19.0](#1.19.0) |10 november 2017 |30 augustus 2020 |
| [1.18.1](#1.18.1) |November 07, 2017 |30 augustus 2020 |
| [1.18.0](#1.18.0) |17 oktober 2017 |30 augustus 2020 |
| [1.17.0](#1.17.0) |10 augustus 2017 |30 augustus 2020 |
| [1.16.1](#1.16.1) |07 augustus 2017 |30 augustus 2020 |
| [1.16.0](#1.16.0) |02 augustus 2017 |30 augustus 2020 |
| [1.15.0](#1.15.0) |30 juni 2017 |30 augustus 2020 |
| [1.14.1](#1.14.1) |23 mei 2017 |30 augustus 2020 |
| [1.14.0](#1.14.0) |10 mei 2017 |30 augustus 2020 |
| [1.13.4](#1.13.4) |09 mei 2017 |30 augustus 2020 |
| [1.13.3](#1.13.3) |06 mei 2017 |30 augustus 2020 |
| [1.13.2](#1.13.2) |19 april 2017 |30 augustus 2020 |
| [1.13.1](#1.13.1) |29 maart 2017 |30 augustus 2020 |
| [1.13.0](#1.13.0) |24 maart 2017 |30 augustus 2020 |
| [1.12.2](#1.12.2) |20 maart 2017 |30 augustus 2020 |
| [1.12.1](#1.12.1) |14 maart 2017 |30 augustus 2020 |
| [1.12.0](#1.12.0) |15 februari 2017 |30 augustus 2020 |
| [1.11.4](#1.11.4) |6 februari 2017 |30 augustus 2020 |
| [1.11.3](#1.11.3) |26 januari 2017 |30 augustus 2020 |
| [1.11.1](#1.11.1) |21 december 2016 |30 augustus 2020 |
| [1.11.0](#1.11.0) |08 december 2016 |30 augustus 2020 |
| [1.10.0](#1.10.0) |27 september 2016 |30 augustus 2020 |
| [1.9.5](#1.9.5) |1 september 2016 |30 augustus 2020 |
| [1.9.4](#1.9.4) |24 augustus 2016 |30 augustus 2020 |
| [1.9.3](#1.9.3) |15 augustus 2016 |30 augustus 2020 |
| [1.9.2](#1.9.2) |23 juli 2016 |30 augustus 2020 |
| [1.8.0](#1.8.0) |14 juni 2016 |30 augustus 2020 |
| [1.7.1](#1.7.1) |06 mei 2016 |30 augustus 2020 |
| [1.7.0](#1.7.0) |26 april 2016 |30 augustus 2020 |
| [1.6.3](#1.6.3) |08 april 2016 |30 augustus 2020 |
| [1.6.2](#1.6.2) |29 maart 2016 |30 augustus 2020 |
| [1.5.3](#1.5.3) |19 februari 2016 |30 augustus 2020 |
| [1.5.2](#1.5.2) |14 december 2015 |30 augustus 2020 |
| [1.5.1](#1.5.1) |23 november 2015 |30 augustus 2020 |
| [1.5.0](#1.5.0) |05 oktober 2015 |30 augustus 2020 |
| [1.4.1](#1.4.1) |25 augustus 2015 |30 augustus 2020 |
| [1.4.0](#1.4.0) |13 augustus 2015 |30 augustus 2020 |
| [1.3.0](#1.3.0) |05 augustus 2015 |30 augustus 2020 |
| [1.2.0](#1.2.0) |06 juli 2015 |30 augustus 2020 |
| [1.1.0](#1.1.0) |30 april 2015 |30 augustus 2020 |
| [1.0.0](#1.0.0) |08 april 2015 | 30 augustus 2020 |


## <a name="faq"></a>Veelgestelde vragen
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Zie ook
Zie voor meer informatie over Cosmos DB, [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) servicepagina. 

