---
title: Azure Cosmos DB-prestatietips voor .NET
description: Meer informatie over opties voor clientconfiguratie om de prestaties van Azure Cosmos DB te verbeteren.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 01/15/2020
ms.author: sngun
ms.openlocfilehash: 68a9a7d5f90831bb2e0c9284cb71ae4b92edffad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80131405"
---
# <a name="performance-tips-for-azure-cosmos-db-and-net"></a>Tips voor betere prestaties van Azure Cosmos DB en .NET

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB is een snelle en flexibele gedistribueerde database die naadloos wordt geschaald met gegarandeerde latentie en doorvoer. U hoeft geen grote architectuurwijzigingen aan te brengen of complexe code te schrijven om uw database te schalen met Azure Cosmos DB. Op- en afschalen is net zo eenvoudig als het maken van één API-aanroep. Zie voor meer informatie hoe u [de containerdoorvoer indient](how-to-provision-container-throughput.md) of [hoe u databasedoorvoer indient](how-to-provision-database-throughput.md). Maar omdat Azure Cosmos DB wordt benaderd via netwerkoproepen, zijn er optimalisaties aan de clientzijde die u maken om topprestaties te bereiken wanneer u de [SQL .NET SDK gebruikt.](sql-api-sdk-dotnet-standard.md)

Als u de prestaties van uw database probeert te verbeteren, u de volgende opties overwegen:

## <a name="hosting-recommendations"></a>Hostingaanbevelingen

**Gebruik Windows 64-bits voor query-intensieve workloads in plaats van Linux of Windows 32-bits hostverwerking**

We raden windows 64-bits hostverwerking aan voor betere prestaties. De SQL SDK bevat een native ServiceInterop.dll om query's lokaal te ontlopen en te optimaliseren. ServiceInterop.dll wordt alleen ondersteund op het Windows x64-platform. Voor Linux en andere niet-ondersteunde platforms waar ServiceInterop.dll niet beschikbaar is, wordt een extra netwerkoproep naar de gateway gemaakt om de geoptimaliseerde query te krijgen. De volgende typen toepassingen gebruiken standaard 32-bits hostverwerking. Voer de volgende stappen uit op basis van het type toepassing om hostverwerking te wijzigen in 64-bits verwerking:

- Voor uitvoerbare toepassingen u de hostverwerking wijzigen door het [platformdoel](https://docs.microsoft.com/visualstudio/ide/how-to-configure-projects-to-target-platforms?view=vs-2019) in te stellen op **x64** in het venster **Projecteigenschappen** op het tabblad **Build.**

- Voor vstest-gebaseerde testprojecten u de hostverwerking wijzigen door **Standaardprocessorarchitectuur** > **testinstellingen** > **als X64** te selecteren in het menu Visual Studio **Test.**

- Voor lokaal geïmplementeerde ASP.NET webtoepassingen u de hostverwerking wijzigen door **de 64-bits versie van IIS Express gebruiken voor websites en projecten** te selecteren onder **Tools** > **Options** > Projects en**Solutions** > Web**Projects**.

- Voor ASP.NET webtoepassingen die op Azure zijn geïmplementeerd, u de hostverwerking wijzigen door het **64-bits** platform in **toepassingsinstellingen** in de Azure-portal te selecteren.

> [!NOTE] 
> Standaard zijn nieuwe Visual Studio-projecten ingesteld op **elke CPU.** We raden u aan uw project in te stellen op **x64,** zodat het niet overschakelt naar **x86.** Een project dat is ingesteld op **Elke CPU** kan eenvoudig overschakelen naar **x86** als een x86-only afhankelijkheid wordt toegevoegd.<br/>
> ServiceInterop.dll moet zich in de map bevinden waaruit de SDK DLL wordt uitgevoerd. Dit moet alleen een punt van zorg zijn als u dll's handmatig kopieert of aangepaste build-/implementatiesystemen hebt.
    
**Garbage Collection aan de serverzijde inschakelen (GC)**

Het verminderen van de frequentie van het ophalen van afval kan helpen in sommige gevallen. Stel [gcServer](https://msdn.microsoft.com/library/ms229357.aspx) in .NET in op `true`.

**De werklast van uw client uitschalen**

Als u test bij hoge doorvoerniveaus (meer dan 50.000 RU/s), kan de clienttoepassing het knelpunt worden als gevolg van het afdekken van de machine op CPU of netwerkgebruik. Als u dit punt bereikt, u het Azure Cosmos DB-account verder pushen door uw clienttoepassingen op meerdere servers uit te schalen.

> [!NOTE] 
> Een hoog CPU-gebruik kan leiden tot verhoogde latentie en een time-out uitzondering aanvragen.

## <a name="networking"></a>Netwerken
<a id="direct-connection"></a>

**Verbindingsbeleid: Direct Connection-modus gebruiken**

Hoe een client verbinding maakt met Azure Cosmos DB heeft belangrijke prestatieimplicaties, vooral voor waargenomen latentie aan clientzijde. Er zijn twee belangrijke configuratie-instellingen beschikbaar voor het configureren van het clientverbindingsbeleid: de *verbindingsmodus* en het *verbindingsprotocol*.  De twee beschikbare modi zijn:

   * Gateway-modus
      
     De gatewaymodus wordt ondersteund op alle SDK-platforms en is de geconfigureerde standaardvoor de [Microsoft.Azure.DocumentDB SDK](sql-api-sdk-dotnet.md). Als uw toepassing wordt uitgevoerd binnen een bedrijfsnetwerk met strikte firewallbeperkingen, is de gateway-modus de beste keuze omdat deze de standaard HTTPS-poort en één eindpunt gebruikt. De prestatieafweging is echter dat de gateway-modus een extra netwerkhop omvat elke keer dat gegevens worden gelezen of geschreven naar Azure Cosmos DB. De directe modus biedt dus betere prestaties omdat er minder netwerkhops zijn. We raden ook de gateway-verbindingsmodus aan wanneer u toepassingen uitvoert in omgevingen met een beperkt aantal socketverbindingen.

     Wanneer u de SDK in Azure-functies gebruikt, met name in het [verbruiksplan,](../azure-functions/functions-scale.md#consumption-plan)moet u zich bewust zijn van de huidige [beperkingen op verbindingen](../azure-functions/manage-connections.md). In dat geval is de gatewaymodus misschien beter als u ook werkt met andere HTTP-gebaseerde clients binnen uw Azure Functions-toepassing.

   * Directe modus

     De directe modus ondersteunt connectiviteit via het TCP-protocol en is de standaardconnectiviteitsmodus als u de [Microsoft.Azure.Cosmos/.NET V3 SDK gebruikt.](sql-api-sdk-dotnet-standard.md)

In de gatewaymodus gebruikt Azure Cosmos DB poort 443 en poorten 10250, 10255 en 10256 wanneer u de Azure Cosmos DB API voor MongoDB gebruikt. Poort 10250 wordt toegewezen aan een standaard MongoDB-exemplaar zonder geo-replicatie. Poorten 10255 en 10256 worden toegewezen aan het exemplaar MongoDB met georeplicatie.
     
Wanneer u TCP in de directe modus gebruikt, moet u, naast de gatewaypoorten, ervoor zorgen dat het poortbereik tussen 10000 en 20000 is geopend omdat Azure Cosmos DB dynamische TCP-poorten gebruikt. Als deze poorten niet zijn geopend en u TCP probeert te gebruiken, ontvangt u een fout die niet beschikbaar is voor 503 Service. In deze tabel worden de verbindingsmodi weergegeven die beschikbaar zijn voor verschillende API's en de servicepoorten die voor elke API worden gebruikt:

|Verbindingsmodus  |Ondersteund protocol  |Ondersteunde SDKs  |API/Servicepoort  |
|---------|---------|---------|---------|
|Gateway  |   HTTPS    |  Alle SDK's    |   SQL (443), MongoDB (10250, 10255, 10256), Tabel (443), Cassandra (10350), Grafiek (443)    |
|Direct    |     TCP    |  .NET SDK    | Havens in het bereik van 10000 tot en met 20000 |

Azure Cosmos DB biedt een eenvoudig, open RESTful-programmeermodel via HTTPS. Daarnaast biedt het een efficiënt TCP-protocol, dat ook RESTful is in zijn communicatiemodel en beschikbaar is via de .NET client SDK. TCP-protocol maakt gebruik van SSL voor initiële verificatie en versleuteling van verkeer. Gebruik het TCP-protocol waar mogelijk voor de beste prestaties.

Voor SDK V3 configureert u de `CosmosClient` verbindingsmodus `CosmosClientOptions`wanneer u de instantie maakt in . Vergeet niet dat de directe modus de standaardmodus is.

```csharp
var serviceEndpoint = new Uri("https://contoso.documents.net");
var authKey = "your authKey from the Azure portal";
CosmosClient client = new CosmosClient(serviceEndpoint, authKey,
new CosmosClientOptions
{
    ConnectionMode = ConnectionMode.Gateway // ConnectionMode.Direct is the default
});
```

Voor de Microsoft.Azure.DocumentDB SDK configureert u de `DocumentClient` verbindingsmodus `ConnectionPolicy` tijdens het maken van de instantie met behulp van de parameter. Als u de directe modus gebruikt, u de `Protocol` parameter ook instellen met behulp van de `ConnectionPolicy` parameter.

```csharp
var serviceEndpoint = new Uri("https://contoso.documents.net");
var authKey = "your authKey from the Azure portal";
DocumentClient client = new DocumentClient(serviceEndpoint, authKey,
new ConnectionPolicy
{
   ConnectionMode = ConnectionMode.Direct, // ConnectionMode.Gateway is the default
   ConnectionProtocol = Protocol.Tcp
});
```

Omdat TCP alleen in de directe modus wordt ondersteund, wordt het HTTPS-protocol altijd `Protocol` gebruikt `ConnectionPolicy` om met de gateway te communiceren als de waarde in de gateway wordt genegeerd.

![Het azure cosmos DB-verbindingsbeleid](./media/performance-tips/connection-policy.png)

**Call OpenAsync om opstartlatentie te voorkomen op eerste verzoek**

Standaard heeft de eerste aanvraag een hogere latentie omdat de adresrouteringstabel moet worden opgehaald. Wanneer u [SDK V2](sql-api-sdk-dotnet.md)gebruikt, belt u `OpenAsync()` eenmaal tijdens de initialisatie om deze opstartlatentie op het eerste verzoek te voorkomen:

    await client.OpenAsync();

> [!NOTE] 
> `OpenAsync`genereert aanvragen om de adresrouteringstabel voor alle containers in het account te verkrijgen. Voor accounts die veel containers hebben, maar waarvan `OpenAsync` de toepassing toegang heeft tot een subset van hen, zou een onnodige hoeveelheid verkeer genereren, waardoor de initialisatie traag zou zijn. Dus `OpenAsync` het gebruik kan niet nuttig zijn in dit scenario, omdat het vertraagt het opstarten van toepassingen.

   <a id="same-region"></a>
**Voor prestaties u clients in dezelfde Azure-regio samenvoegen**

Plaats, indien mogelijk, alle toepassingen die Azure Cosmos DB aanroepen in dezelfde regio als de Azure Cosmos DB-database. Hier is een vergelijking bij benadering: oproepen naar Azure Cosmos DB binnen dezelfde regio zijn voltooid binnen 1 ms tot 2 ms, maar de latentie tussen de westkust en de oostkust van de VS is meer dan 50 ms. Deze latentie kan variëren van aanvraag tot aanvraag, afhankelijk van de route die door de aanvraag wordt genomen wanneer deze van de client naar de azure-datacentergrens gaat. U de laagst mogelijke latentie krijgen door ervoor te zorgen dat de aanroepende toepassing zich binnen dezelfde Azure-regio bevindt als het ingerichte Azure Cosmos DB-eindpunt. Zie [Azure-regio's](https://azure.microsoft.com/regions/#services)voor een lijst met beschikbare regio's .

![Het azure cosmos](./media/performance-tips/same-region.png) DB-verbindingsbeleid<a id="increase-threads"></a>

**Het aantal threads/taken verhogen**

Omdat oproepen naar Azure Cosmos DB via het netwerk worden gevoerd, moet u mogelijk de mate van parallellisme van uw aanvragen variëren, zodat de clienttoepassing minimale tijd doorbrengt met wachten tussen aanvragen. Als u bijvoorbeeld de parallelbibliotheek voor [.NET-taak](https://msdn.microsoft.com//library/dd460717.aspx)gebruikt, maakt u in de volgorde van honderden taken die lezen uit of schrijven naar Azure Cosmos DB.

**Versneld netwerken inschakelen**
 
 Om latentie en CPU-jitter te verminderen, raden we u aan om versnelde netwerken in te schakelen op virtuele clientmachines. Zie [Een virtuele Windows-machine maken met versnelde netwerken](../virtual-network/create-vm-accelerated-networking-powershell.md) of Een Virtuele [Linux-machine maken met versnelde netwerken.](../virtual-network/create-vm-accelerated-networking-cli.md)

## <a name="sdk-usage"></a>SDK-gebruik
**Installeer de meest recente SDK**

De Azure Cosmos DB SDKs worden voortdurend verbeterd om de beste prestaties te bieden. Bekijk de [Azure Cosmos DB SDK-pagina's](sql-api-sdk-dotnet-standard.md) om de meest recente SDK-pagina's te bepalen en verbeteringen te bekijken.

**Stream-API's gebruiken**

[.NET SDK V3](sql-api-sdk-dotnet-standard.md) bevat stream-API's die gegevens kunnen ontvangen en retourneren zonder serialisatie. 

Toepassingen op het middenniveau die geen reacties rechtstreeks van de SDK verbruiken, maar deze doorgeven aan andere toepassingslagen, kunnen profiteren van de stream-API's. Zie de [artikelbeheervoorbeelden](https://github.com/Azure/azure-cosmos-dotnet-v3/blob/master/Microsoft.Azure.Cosmos.Samples/Usage/ItemManagement) voor voorbeelden van streamhandling.

**Een singleton Azure Cosmos DB-client gebruiken voor de levensduur van uw toepassing**

Elke `DocumentClient` `CosmosClient` instantie is draadveilig en voert efficiënt verbindingsbeheer uit en werkt in cache bij het werken in de directe modus. Om efficiënt verbindingsbeheer en betere SDK-clientprestaties mogelijk te `AppDomain` maken, raden we u aan één exemplaar per voor de levensduur van de toepassing te gebruiken.

   <a id="max-connection"></a>

**Verhoog System.Net MaxConnections per host wanneer u de gatewaymodus gebruikt**

Azure Cosmos DB-aanvragen worden via HTTPS/REST gedaan wanneer u de gatewaymodus gebruikt. Ze zijn onderworpen aan de standaardverbindingslimiet per hostnaam of IP-adres. Mogelijk moet u `MaxConnections` instellen op een hogere waarde (100 tot 1000), zodat de clientbibliotheek meerdere gelijktijdige verbindingen met Azure Cosmos DB kan gebruiken. In .NET SDK 1.8.0 en hoger is de standaardwaarde voor [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit.aspx) 50. Als u de waarde wilt wijzigen, u [Documents.Client.ConnectionPolicy.MaxConnectionLimit](https://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.connectionpolicy.maxconnectionlimit.aspx) instellen op een hogere waarde.

**Parallelle query's afstemmen op partitieverzamelingen**

SQL .NET SDK 1.9.0 en hoger ondersteunen parallelle query's, waarmee u een partitieverzameling parallel opvragen. Zie [codevoorbeelden](https://github.com/Azure/azure-documentdb-dotnet/blob/master/samples/code-samples/Queries/Program.cs) met betrekking tot het werken met de SDK's voor meer informatie. Parallelle query's zijn ontworpen om een betere latentie en doorvoer van query's te bieden dan hun seriële tegenhanger. Parallelle query's bevatten twee parameters die u afstemmen op uw vereisten: 
- `MaxDegreeOfParallelism`hiermee wordt het maximum aantal partities geregeld dat parallel kan worden opgevraagd. 
- `MaxBufferedItemCount`hiermee wordt het aantal vooraf opgehaalde resultaten gecontroleerd.

***Tuning mate van parallellisme***

Parallelle query werkt door meerdere partities parallel op te vragen. Maar gegevens uit een afzonderlijke partitie worden serieel opgehaald met betrekking tot de query. Instellen `MaxDegreeOfParallelism` in SDK `MaxConcurrency` [V2](sql-api-sdk-dotnet.md) of sdk [V3](sql-api-sdk-dotnet-standard.md) op het aantal partities heeft de beste kans om de meest performante query te bereiken, op voorwaarde dat alle andere systeemomstandigheden hetzelfde blijven. Als u het aantal partities niet kent, u de mate van parallellisme instellen op een hoog aantal. Het systeem kiest het minimum (aantal partities, door de gebruiker geleverde input) als de mate van parallellisme.

Houd er rekening mee dat parallelle query's het meeste voordeel opleveren als de gegevens gelijkmatig over alle partities worden verdeeld met betrekking tot de query. Als de partitieverzameling zo is verdeeld dat alle of het grootste deel van de gegevens die door een query worden geretourneerd, is geconcentreerd in een paar partities (één partitie is het ergste geval), zullen deze partities de uitvoering van de query knelpunten.

***MaxBufferedItemCount afstemmen***
    
Parallelle query is ontworpen om resultaten vooraf op te halen terwijl de huidige batch met resultaten wordt verwerkt door de client. Dit vooraf ophalen helpt de algehele latentie van een query te verbeteren. De `MaxBufferedItemCount` parameter beperkt het aantal vooraf opgehaalde resultaten. Stel `MaxBufferedItemCount` in op het verwachte aantal geretourneerde resultaten (of een hoger aantal) zodat de query het maximale voordeel van vooraf ophalen kan ontvangen.

Pre-fetching werkt op dezelfde manier, ongeacht de mate van parallellisme, en er is een enkele buffer voor de gegevens van alle partities.  

**Back-off implementeren met opnieuw gebruikNa intervallen**

Tijdens prestatietests moet u de belasting verhogen totdat een klein aantal aanvragen wordt beperkt. Als aanvragen worden beperkt, moet de clienttoepassing het gaspedaal weer intrappen voor het door de server opgegeven interval voor het opnieuw proberen. Met respect voor de backoff zorgt ervoor dat u een minimale hoeveelheid tijd te wachten tussen nieuwe pogingen. 

Beleidsondersteuning opnieuw proberen is opgenomen in deze SDK's:
- Versie 1.8.0 en hoger van de [.NET SDK voor SQL](sql-api-sdk-dotnet.md) en de [Java SDK voor SQL](sql-api-sdk-java.md)
- Versie 1.9.0 en hoger van de [Node.js SDK voor SQL](sql-api-sdk-node.md) en de [Python SDK voor SQL](sql-api-sdk-python.md)
- Alle ondersteunde versies van de [.NET Core](sql-api-sdk-dotnet-core.md) SDKs 

Zie [RetryAfter voor](https://msdn.microsoft.com/library/microsoft.azure.documents.documentclientexception.retryafter.aspx)meer informatie.
    
In versie 1.19 en hoger van de .NET SDK is er een mechanisme voor het registreren van aanvullende diagnostische informatie en het oplossen van latentieproblemen, zoals in het volgende voorbeeld wordt weergegeven. U de diagnostische tekenreeks registreren voor aanvragen met een hogere leeslatentie. De vastgelegde diagnostische tekenreeks helpt u te begrijpen hoe vaak u 429 fouten voor een bepaald verzoek hebt ontvangen.

```csharp
ResourceResponse<Document> readDocument = await this.readClient.ReadDocumentAsync(oldDocuments[i].SelfLink);
readDocument.RequestDiagnosticsString 
```

**Cache document URI's voor lagere leeslatentie**

Cache document URI's waar mogelijk voor de best gelezen prestaties. U moet logica definiëren om de bron-id in de cache te bewaren wanneer u een bron maakt. Opzoekingen op basis van resource-id's zijn sneller dan op naam gebaseerde lookups, dus het kaanders plaatsen van deze waarden verbetert de prestaties.

   <a id="tune-page-size"></a>
**De paginagrootte afstemmen voor query's/leesfeeds voor betere prestaties**

Wanneer u documenten in bulk leest met leesfeedfunctionaliteit `ReadDocumentFeedAsync`(bijvoorbeeld) of wanneer u een SQL-query uitgeeft, worden de resultaten op een gesegmenteerde manier geretourneerd als de resultaatset te groot is. Standaard worden de resultaten geretourneerd in brokken van 100 items of 1 MB, welke limiet het eerst wordt bereikt.

Om het aantal netwerkretourritten te verminderen dat nodig is om alle toepasselijke resultaten op te halen, u de paginagrootte vergroten door [x-ms-max-item-telling](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) te gebruiken om maar liefst 1.000 kopteksten op te vragen. Wanneer u slechts een paar resultaten hoeft weer te geven, bijvoorbeeld als uw gebruikersinterface of toepassings-API slechts 10 resultaten tegelijk retourneert, u ook de paginagrootte verlagen tot 10 om de doorvoer die wordt verbruikt voor lees- en query's te verminderen.

> [!NOTE] 
> Het `maxItemCount` pand mag niet alleen worden gebruikt voor pagination. Het belangrijkste gebruik is om de prestaties van query's te verbeteren door het maximum aantal items dat op één pagina wordt geretourneerd, te verminderen.  

U ook het paginaformaat instellen met behulp van de beschikbare Azure Cosmos DB SDKs. Met de eigenschap `FeedOptions` [MaxItemCount](/dotnet/api/microsoft.azure.documents.client.feedoptions.maxitemcount?view=azure-dotnet) u het maximum aantal items instellen dat moet worden geretourneerd in de opsommingsbewerking. Wanneer `maxItemCount` de SDK is ingesteld op -1, vindt de SDK automatisch de optimale waarde, afhankelijk van de documentgrootte. Bijvoorbeeld:
    
```csharp
IQueryable<dynamic> authorResults = client.CreateDocumentQuery(documentCollection.SelfLink, "SELECT p.Author FROM Pages p WHERE p.Title = 'About Seattle'", new FeedOptions { MaxItemCount = 1000 });
```
    
Wanneer een query wordt uitgevoerd, worden de resulterende gegevens verzonden binnen een TCP-pakket. Als u een te `maxItemCount`lage waarde opgeeft voor , is het aantal ritten dat nodig is om de gegevens binnen het TCP-pakket te verzenden hoog, wat van invloed is op de prestaties. Dus als u niet zeker weet welke `maxItemCount` waarde u voor de eigenschap moet instellen, u deze het beste instellen op -1 en de SDK de standaardwaarde laten kiezen.

**Het aantal threads/taken verhogen**

Zie [Het aantal threads/taken verhogen](#increase-threads) in het gedeelte Netwerken van dit artikel.

## <a name="indexing-policy"></a>Indexeringsbeleid
 
**Ongebruikte paden uitsluiten van indexering voor snellere schrijfbewerkingen**

Met het Azure Cosmos DB-indexeringsbeleid u ook opgeven welke documentpaden u wilt opnemen in of uitsluiten van indexering met behulp van indexeringspaden (Indexeringsbeleid.IncludedPaths en IndexingPolicy.ExcludedPaths). Indexeringspaden kunnen de schrijfprestaties verbeteren en de indexopslag verminderen voor scenario's waarin de querypatronen vooraf bekend zijn. Dit komt omdat indexeringskosten rechtstreeks correleren met het aantal geïndexeerde unieke paden. Deze code geeft bijvoorbeeld aan hoe u een hele sectie van de documenten (een substructuur) uitsluiten van indexering met behulp van de joker:This code shows how to exclude a entire section of the documents (a subtree) from indexing using the * wildcard:

```csharp
var collection = new DocumentCollection { Id = "excludedPathCollection" };
collection.IndexingPolicy.IncludedPaths.Add(new IncludedPath { Path = "/*" });
collection.IndexingPolicy.ExcludedPaths.Add(new ExcludedPath { Path = "/nonIndexedContent/*");
collection = await client.CreateDocumentCollectionAsync(UriFactory.CreateDatabaseUri("db"), excluded);
```

Zie [Azure Cosmos DB-indexeringsbeleid](index-policy.md)voor meer informatie .

## <a name="throughput"></a>Doorvoer
<a id="measure-rus"></a>

**Meet en stem af voor lager gebruik van aanvraageenheden per seconde**

Azure Cosmos DB biedt een uitgebreide set databasebewerkingen. Deze bewerkingen omvatten relationele en hiërarchische query's met UDF's, opgeslagen procedures en triggers, allemaal die werken op de documenten in een databaseverzameling. De kosten die aan elk van deze bewerkingen zijn gekoppeld, zijn afhankelijk van de CPU, IO en het geheugen die nodig zijn om de bewerking te voltooien. In plaats van na te denken over en het beheer van hardwareresources, u een Aanvraageenheid (RU) zien als één maatregel voor de resources die nodig zijn om verschillende databasebewerkingen uit te voeren en een aanvraag voor een toepassing te onderhouden.

Doorvoer is ingericht op basis van het aantal [aanvraageenheden](request-units.md) dat voor elke container is ingesteld. Het verbruik van de aanvraageenheid wordt beoordeeld als een tarief per seconde. Aanvragen die het ingerichte tarief van de aanvraageenheid voor hun container overschrijden, zijn beperkt totdat de koers onder het ingerichte niveau voor de container zakt. Als uw toepassing een hoger doorvoerniveau vereist, u uw doorvoer verhogen door extra aanvraageenheden in te richten.

De complexiteit van een query is van invloed op het aantal aanvraageenheden dat wordt verbruikt voor een bewerking. Het aantal predicaten, de aard van de predicaten, het aantal UDF's en de grootte van de brongegevensset hebben allemaal invloed op de kosten van querybewerkingen.

Als u de overhead van een bewerking wilt meten (maken, bijwerken of verwijderen), controleert `ResourceResponse\<T>` `FeedResponse\<T>` u de [x-ms-request-charge-header](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (of de gelijkwaardige `RequestCharge` eigenschap in of in de .NET SDK) om het aantal aanvraageenheden te meten dat door de bewerkingen wordt verbruikt:

```csharp
// Measure the performance (Request Units) of writes
ResourceResponse<Document> response = await client.CreateDocumentAsync(collectionSelfLink, myDocument);
Console.WriteLine("Insert of document consumed {0} request units", response.RequestCharge);
// Measure the performance (Request Units) of queries
IDocumentQuery<dynamic> queryable = client.CreateDocumentQuery(collectionSelfLink, queryString).AsDocumentQuery();
while (queryable.HasMoreResults)
    {
        FeedResponse<dynamic> queryResponse = await queryable.ExecuteNextAsync<dynamic>();
        Console.WriteLine("Query batch consumed {0} request units", queryResponse.RequestCharge);
    }
```             

De aanvraagkosten die in deze koptekst worden geretourneerd, zijn een fractie van uw ingerichte doorvoer (dat wil zeggen 2.000 RU's / seconde). Als de vorige query bijvoorbeeld 1.000 documenten van 1 KB retourneert, zijn de kosten van de bewerking 1.000. Dus, binnen een seconde, de server eert slechts twee van dergelijke verzoeken voordat de snelheid beperken van latere verzoeken. Zie [Eenheden aanvragen](request-units.md) en de calculator van de [aanvraageenheid](https://www.documentdb.com/capacityplanner)voor meer informatie.
<a id="429"></a>

**Tariefbeperking/aanvraagtarief te groot**

Wanneer een client probeert de gereserveerde doorvoer voor een account te overschrijden, is er geen prestatiedegradatie op de server en geen gebruik van doorvoercapaciteit boven het gereserveerde niveau. De server beëindigt de aanvraag preventief met RequestRateTooLarge (HTTP-statuscode 429). Het retourneert een [x-ms-retry-after-ms](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) header die de hoeveelheid tijd aangeeft, in milliseconden, dat de gebruiker moet wachten voordat hij het verzoek opnieuw probeert.

    HTTP Status 429,
    Status Line: RequestRateTooLarge
    x-ms-retry-after-ms :100

De SDK's vangen allemaal impliciet dit antwoord, respecteren de server-opgegeven retry-after header en proberen het verzoek opnieuw. Tenzij uw account gelijktijdig wordt geopend door meerdere clients, zal de volgende poging slagen.

Als u meer dan één client cumulatief boven het aanvraagpercentage hebt, is het aantal standaardopnieuw proberen dat momenteel intern door de client is ingesteld op 9, mogelijk niet voldoende. In dit geval gooit de client een DocumentClientException met statuscode 429 naar de toepassing. 

U het aantal standaardwijzigingen `RetryOptions` wijzigen `ConnectionPolicy` door de instantie in te stellen. Standaard wordt de DocumentClientException met statuscode 429 geretourneerd na een cumulatieve wachttijd van 30 seconden als de aanvraag boven het aanvraagpercentage blijft werken. Deze fout wordt zelfs geretourneerd wanneer het aantal nieuwe opnieuw proberen kleiner is dan het maximale aantal nieuwe try's, ongeacht of de huidige waarde de standaardwaarde van 9 of een door de gebruiker gedefinieerde waarde is.

Het geautomatiseerde gedrag voor het opnieuw proberen helpt de veerkracht en bruikbaarheid voor de meeste toepassingen te verbeteren. Maar het is misschien niet het beste gedrag wanneer u prestatiebenchmarks doet, vooral wanneer u latentie meet. De door de client waargenomen latentie piekt als het experiment de servergaspedaal raakt en ervoor zorgt dat de client SDK stilletjes opnieuw probeert. Om latentiepieken tijdens prestatie-experimenten te voorkomen, meet u de kosten die door elke bewerking worden geretourneerd en zorgt u ervoor dat aanvragen onder het gereserveerde aanvraagpercentage werken. Zie [Eenheden aanvragen voor](request-units.md)meer informatie.

**Voor een hogere doorvoer, ontwerp voor kleinere documenten**

De aanvraagkosten (dat wil zeggen de kosten voor het verwerken van aanvragen) van een bepaalde bewerking correleren rechtstreeks met de grootte van het document. Bewerkingen op grote documenten kosten meer dan bewerkingen op kleine documenten.

## <a name="next-steps"></a>Volgende stappen
Zie [Prestatie- en schaaltests met Azure Cosmos DB](performance-testing.md)voor een voorbeeldtoepassing die wordt gebruikt om Azure Cosmos DB te evalueren voor krachtige scenario's op een aantal clientmachines.

Zie [Partitioneren en schalen in Azure Cosmos DB](partition-data.md)voor meer informatie over het ontwerpen van uw toepassing voor schaal en hoge prestaties.
