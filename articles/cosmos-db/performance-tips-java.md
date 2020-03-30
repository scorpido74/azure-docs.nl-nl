---
title: Azure Cosmos DB prestatietips voor Java
description: Lees opties voor clientconfiguratie om de prestaties van azure cosmos-database te verbeteren
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: 3b7d221c2afc952f40da035c6e2c282b3b932aa5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "69616766"
---
# <a name="performance-tips-for-azure-cosmos-db-and-java"></a>Tips voor betere prestaties van Azure Cosmos DB en Java

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB is een snelle en flexibele gedistribueerde database die naadloos wordt geschaald met gegarandeerde latentie en doorvoer. U hoeft geen grote architectuurwijzigingen aan te brengen of complexe code te schrijven om uw database te schalen met Azure Cosmos DB. Op- en afschalen is net zo eenvoudig als het maken van één API-aanroep. Zie voor meer informatie hoe u [de containerdoorvoer indient](how-to-provision-container-throughput.md) of [hoe u databasedoorvoer indient](how-to-provision-database-throughput.md). Omdat Azure Cosmos DB echter wordt benaderd via netwerkaanroep, zijn er optimalisaties aan de clientzijde die u maken om topprestaties te bereiken bij het gebruik van de [SQL Java SDK.](documentdb-sdk-java.md)

Dus als je vraagt "Hoe kan ik mijn databaseprestaties verbeteren?" overweeg de volgende opties:

## <a name="networking"></a>Netwerken
<a id="direct-connection"></a>

1. **Verbindingsmodus: Direct gebruikenhttps**

    Hoe een client verbinding maakt met Azure Cosmos DB is belangrijk voor de prestaties, in het bijzonder wat betreft de latentie aan de clientzijde. Er is één belangrijke configuratie-instelling beschikbaar voor het configureren van het [clientConnectionPolicy](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) – de [ConnectionMode](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode).  De twee beschikbare verbindingsmodi zijn:

   1. [Gateway (standaard)](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)
   2. [Directhttpshttps](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionmode)

      De gatewaymodus wordt ondersteund op alle SDK-platforms en is de geconfigureerde standaard.  Als uw toepassing wordt uitgevoerd binnen een bedrijfsnetwerk met strikte firewallbeperkingen, is Gateway de beste keuze omdat het de standaard HTTPS-poort en één eindpunt gebruikt. De prestatieafweging is echter dat de Gateway-modus een extra netwerkhop omvat elke keer dat gegevens worden gelezen of geschreven naar Azure Cosmos DB. Hierdoor biedt de DirectHttps-modus betere prestaties door minder netwerkhops. 

      De Java SDK gebruikt HTTPS als transportprotocol. HTTPS maakt gebruik van SSL voor initiële authenticatie en versleuteling van verkeer. Bij gebruik van de Java SDK hoeft alleen HTTPS-poort 443 open te zijn. 

      De ConnectionMode is geconfigureerd tijdens het bouwen van de instantie DocumentClient met de parameter ConnectionPolicy. 

      ```Java
      public ConnectionPolicy getConnectionPolicy() {
        ConnectionPolicy policy = new ConnectionPolicy();
        policy.setConnectionMode(ConnectionMode.DirectHttps);
        policy.setMaxPoolSize(1000);
        return policy;
      }
        
      ConnectionPolicy connectionPolicy = new ConnectionPolicy();
      DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
      ```

      ![Illustratie van het Azure Cosmos DB-verbindingsbeleid](./media/performance-tips-java/connection-policy.png)

   <a id="same-region"></a>
2. **Clients in dezelfde Azure-regio verzamelen voor prestaties**

    Plaats, indien mogelijk, toepassingen die Azure Cosmos DB aanroepen in dezelfde regio als de Azure Cosmos-database. Voor een vergelijking, gesprekken naar Azure Cosmos DB binnen dezelfde regio worden voltooid binnen 1-2 ms, maar de latentie tussen de westkust en de oostkust van de VS is >50 ms. Deze latentie kan waarschijnlijk variëren van aanvraag tot aanvraag, afhankelijk van de route die door de aanvraag wordt genomen wanneer deze van de client naar de Azure-datacentergrens gaat. De laagst mogelijke latentie wordt bereikt door ervoor te zorgen dat de aanroepende toepassing zich binnen dezelfde Azure-regio bevindt als het ingerichte Azure Cosmos DB-eindpunt. Zie [Azure-regio's](https://azure.microsoft.com/regions/#services)voor een lijst met beschikbare regio's .

    ![Illustratie van het Azure Cosmos DB-verbindingsbeleid](./media/performance-tips/same-region.png)
   
## <a name="sdk-usage"></a>SDK-gebruik
1. **Installeer de meest recente SDK**

    De Azure Cosmos DB SDKs worden voortdurend verbeterd om de beste prestaties te bieden. Bekijk de [Azure Cosmos DB SDK-pagina's](documentdb-sdk-java.md) om de meest recente SDK-pagina's te bepalen en verbeteringen te bekijken.
2. **Een singleton Azure Cosmos DB-client gebruiken voor de levensduur van uw toepassing**

    Elk [DocumentClient-exemplaar](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient) is thread-safe en voert efficiënt verbindingsbeheer uit en werkt in cache bij het werken in de directmodus. Om efficiënt verbindingsbeheer en betere prestaties door DocumentClient mogelijk te maken, wordt aanbevolen om één exemplaar van DocumentClient per AppDomain te gebruiken voor de levensduur van de toepassing.

   <a id="max-connection"></a>
3. **MaxPoolSize per host verhogen wanneer u de gatewaymodus gebruikt**

    Azure Cosmos DB-aanvragen worden via HTTPS/REST gedaan wanneer u de gatewaymodus gebruikt en worden onderworpen aan de standaardverbindingslimiet per hostnaam of IP-adres. Mogelijk moet u de MaxPoolSize instellen op een hogere waarde (200-1000), zodat de clientbibliotheek meerdere gelijktijdige verbindingen met Azure Cosmos DB kan gebruiken. In de Java SDK is de standaardwaarde voor [ConnectionPolicy.getMaxPoolSize](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.getmaxpoolsize) 100. Gebruik [setMaxPoolSize]( https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setmaxpoolsize) om de waarde te wijzigen.

4. **Parallelle query's afstemmen voor partitieverzamelingen**

    Azure Cosmos DB SQL Java SDK versie 1.9.0 en hoger ondersteunen parallelle query's, waarmee u een partitieverzameling parallel opvragen. Zie [codevoorbeelden](https://github.com/Azure/azure-documentdb-java/tree/master/documentdb-examples/src/test/java/com/microsoft/azure/documentdb/examples) met betrekking tot het werken met de SDK's voor meer informatie. Parallelle query's zijn ontworpen om de latentie en doorvoer van query's over hun seriële tegenhanger te verbeteren.

    (a) ***Tuning setMaxDegreeOfParallelisme\: *** Parallelle query's werken door meerdere partities parallel op te vragen. Gegevens uit een afzonderlijke partitieverzameling worden echter serieel opgehaald met betrekking tot de query. Dus, gebruik [setMaxDegreeOfParallelism](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxdegreeofparallelism) om het aantal partities dat de maximale kans op het bereiken van de meest performante query heeft ingesteld, op voorwaarde dat alle andere systeemomstandigheden hetzelfde blijven. Als u het aantal partities niet kent, u setMaxDegreeOfParallelisme gebruiken om een hoog getal in te stellen en kiest het systeem het minimum (aantal partities, door de gebruiker geleverde invoer) als de maximale mate van parallellisme. 

    Het is belangrijk op te merken dat parallelle query's de beste voordelen opleveren als de gegevens gelijkmatig over alle partities worden verdeeld met betrekking tot de query. Als de partitieverzameling zodanig is verdeeld dat alle of een meerderheid van de gegevens die door een query worden geretourneerd, is geconcentreerd in een paar partities (één partitie in het ergste geval), dan zou de uitvoering van de query worden knelpunten door deze partities.

    (b) ***TuningsetMaxBufferedItemCount\: *** Parallel query is ontworpen om resultaten vooraf op te halen terwijl de huidige batch resultaten door de client wordt verwerkt. Het vooraf ophalen helpt bij algehele latentieverbetering van een query. setMaxBufferedItemCount beperkt het aantal vooraf opgehaalde resultaten. Door [setMaxBufferedItemCount](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptions.setmaxbuffereditemcount) in te stellen op het verwachte aantal geretourneerde resultaten (of een hoger aantal), kan de query maximaal voordeel ontvangen van vooraf ophalen.

    Pre-fetching werkt op dezelfde manier, ongeacht het MaxDegreeOfParallelisme, en er is een enkele buffer voor de gegevens van alle partities.  

5. **Backoff implementeren met intervallen getRetryAfterInMilliseconds**

    Tijdens prestatietests moet u de belasting verhogen totdat een klein aantal aanvragen wordt beperkt. Als de clienttoepassing wordt beperkt, moet de back-off op gaspedaal voor het door de server opgegeven interval voor het opnieuw proberen van de server worden geactiveerd. Het respecteren van de backoff zorgt ervoor dat u minimaal veel tijd doorbrengt met wachten tussen nieuwe pogingen. Beleidsondersteuning opnieuw proberen is opgenomen in versie 1.8.0 en hoger van de [Java SDK.](documentdb-sdk-java.md) Zie [getRetryAfterInMilliseconds](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception.getretryafterinmilliseconds)voor meer informatie.

6. **Uw client-workload uitschalen**

    Als u test op hoge doorvoerniveaus (>50.000 RU/s), kan de clienttoepassing het knelpunt worden als gevolg van het afdekken van de machine op CPU of netwerkgebruik. Als u dit punt bereikt, u het Azure Cosmos DB-account verder pushen door uw clienttoepassingen op meerdere servers uit te schalen.

7. **Op naam gebaseerde adressering gebruiken**

    Gebruik op naam gebaseerde adressering, waarbij koppelingen de indeling `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`hebben, in plaats van SelfLinks (zelf),\_die de indeling `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` hebben om te voorkomen dat resourceids worden opgehaald van alle resources die worden gebruikt om de koppeling te construeren. Ook, als deze middelen krijgen nagemaakt (eventueel met dezelfde naam), caching deze kan niet helpen.

   <a id="tune-page-size"></a>
8. **De paginagrootte afstemmen voor query's/leesfeeds voor betere prestaties**

    Bij het uitvoeren van een bulkread van documenten met behulp van leesfeedfunctionaliteit (bijvoorbeeld [leesdocumenten)](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclient.readdocuments)of bij het uitgeven van een SQL-query, worden de resultaten gesegmenteerd geretourneerd als de resultaatset te groot is. Standaard worden de resultaten geretourneerd in brokken van 100 items of 1 MB, welke limiet het eerst wordt bereikt.

    Om het aantal netwerkretouren dat nodig is om alle toepasselijke resultaten op te halen te verminderen, u de paginagrootte verhogen met de [x-ms-max-item-count-aanvraagkop](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) tot 1000. In gevallen waarin u slechts een paar resultaten hoeft weer te geven, bijvoorbeeld als uw gebruikersinterface of toepassings-API slechts 10 resultaten per keer retourneert, u ook de paginagrootte verlagen tot 10 om de doorvoer die wordt verbruikt voor lees- en query's te verminderen.

    U het paginaformaat ook instellen met de [methode setPageSize.](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedoptionsbase.setpagesize)

## <a name="indexing-policy"></a>Indexeringsbeleid
 
1. **Ongebruikte paden uitsluiten van indexering voor snellere schrijfbewerkingen**

    Met het indexeringsbeleid van Azure Cosmos DB u opgeven welke documentpaden u wilt opnemen of uitsluiten van indexering door gebruik te maken van indexeringspaden[(setIncludedPaths](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setincludedpaths) en [setExcludedPaths).](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.indexingpolicy.setexcludedpaths) Het gebruik van indexeringspaden kan verbeterde schrijfprestaties en lagere indexopslag bieden voor scenario's waarin de querypatronen vooraf bekend zijn, omdat indexeringskosten direct gecorreleerd zijn met het aantal geïndexeerde unieke paden.  In de volgende code ziet u bijvoorbeeld hoe u een hele sectie van de documenten (a.k.a. een subboom) van indexering met behulp van de "*" wildcard.

    ```Java
    Index numberIndex = Index.Range(DataType.Number);
    numberIndex.set("precision", -1);
    indexes.add(numberIndex);
    includedPath.setIndexes(indexes);
    includedPaths.add(includedPath);
    indexingPolicy.setIncludedPaths(includedPaths);
    collectionDefinition.setIndexingPolicy(indexingPolicy);
    ```

    Zie [Azure Cosmos DB-indexeringsbeleid](indexing-policies.md)voor meer informatie .

## <a name="throughput"></a>Doorvoer
<a id="measure-rus"></a>

1. **Meet en stem af voor lager aanvraageenheden/secondegebruik**

    Azure Cosmos DB biedt een uitgebreide set databasebewerkingen, waaronder relationele en hiërarchische query's met UDF's, opgeslagen procedures en triggers, allemaal die werken op de documenten in een databaseverzameling. De kosten die gepaard gaan met elke bewerking hangen af van de CPU, de IO en het geheugen, vereist om de bewerking uit te voeren. In plaats van na te denken over en het beheer van hardwareresources, u een aanvraageenheid (RU) zien als één maatregel voor de resources die nodig zijn om verschillende databasebewerkingen uit te voeren en een aanvraag voor een toepassing te onderhouden.

    Doorvoer is ingericht op basis van het aantal [aanvraageenheden](request-units.md) dat voor elke container is ingesteld. Het verbruik per aanvraageenheid wordt beoordeeld als een tarief per seconde. Aanvragen die het ingerichte aanvraageenheidstarief voor hun container overschrijden, zijn beperkt totdat de koers onder het ingerichte niveau voor de container zakt. Als uw toepassing een hoger doorvoerniveau vereist, u uw doorvoer verhogen door extra aanvraageenheden in te richten. 

    De complexiteit van een query is van invloed op het aantal aanvraageenheden dat voor een bewerking wordt verbruikt. Het aantal predicaten, de aard van de predicaten, het aantal UDF's en de grootte van de brongegevensset hebben allemaal invloed op de kosten van querybewerkingen.

    Als u de overhead van een bewerking wilt meten (maken, bijwerken of verwijderen), controleert u de [x-ms-request-charge-header](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) (of de gelijkwaardige eigenschap RequestCharge in [ResourceResponse\<T->](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.resourceresponse) of [FeedResponse\<T->](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.feedresponse) om het aantal aanvraageenheden te meten dat door deze bewerkingen wordt verbruikt.

    ```Java
    ResourceResponse<Document> response = client.createDocument(collectionLink, documentDefinition, null, false);

    response.getRequestCharge();
    ```             

    De in deze koptekst geretourneerde aanvraagkosten zijn een fractie van uw ingerichte doorvoer. Als u bijvoorbeeld 2000 RU/s hebt ingericht en als de voorgaande query 1000 1KB-documenten retourneert, zijn de kosten van de bewerking 1000. Als zodanig, binnen een seconde, de server honoreert slechts twee van dergelijke verzoeken voordat de snelheid beperken van latere verzoeken. Zie [Eenheden aanvragen](request-units.md) en de calculator voor de [aanvraageenheid](https://www.documentdb.com/capacityplanner)voor meer informatie.
   <a id="429"></a>
1. **Tariefbeperking/aanvraagtarief te groot**

    Wanneer een client probeert de gereserveerde doorvoer voor een account te overschrijden, is er geen prestatiedegradatie op de server en geen gebruik van doorvoercapaciteit boven het gereserveerde niveau. De server beëindigt de aanvraag preventief met RequestRateTooLarge (HTTP-statuscode 429) en retourneert de [x-ms-retry-after-ms-header](https://docs.microsoft.com/rest/api/cosmos-db/common-cosmosdb-rest-response-headers) die aangeeft hoeveel tijd de gebruiker in milliseconden moet wachten voordat de aanvraag opnieuw wordt geprobeerd.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    De SDK's vangen allemaal impliciet dit antwoord, respecteren de server-opgegeven retry-after header en proberen het verzoek opnieuw. Tenzij uw account gelijktijdig wordt geopend door meerdere clients, zal de volgende poging slagen.

    Als u meer dan één client cumulatief boven het aanvraagpercentage hebt, is het aantal standaardopnieuw proberen dat momenteel intern door de client is ingesteld op 9, mogelijk niet voldoende. in dit geval gooit de client een [DocumentClientException](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.documentclientexception) met statuscode 429 naar de toepassing. Het aantal standaardwijzigingen kan worden gewijzigd met [setRetryOptions](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy.setretryoptions) in de instantie [Verbindingsbeleid.](https://docs.microsoft.com/java/api/com.microsoft.azure.documentdb.connectionpolicy) Standaard wordt de DocumentClientException met statuscode 429 geretourneerd na een cumulatieve wachttijd van 30 seconden als de aanvraag boven het aanvraagpercentage blijft werken. Dit gebeurt zelfs wanneer het aantal huidige opnieuw proberen lager is dan het maximale aantal nieuwe try's, of het nu de standaardwaarde van 9 of een door de gebruiker gedefinieerde waarde is.

    Hoewel het geautomatiseerde gedrag voor het opnieuw proberen helpt om de veerkracht en bruikbaarheid voor de meeste toepassingen te verbeteren, kan het op gespannen voet komen te staan bij het doen van prestatiebenchmarks, vooral bij het meten van latentie.  De door de client waargenomen latentie piekt als het experiment de servergaspedaal raakt en ervoor zorgt dat de client SDK stilletjes opnieuw probeert. Om latentiepieken tijdens prestatie-experimenten te voorkomen, meet u de kosten die door elke bewerking worden geretourneerd en zorgt u ervoor dat aanvragen onder het gereserveerde aanvraagpercentage werken. Zie [Eenheden aanvragen voor](request-units.md)meer informatie .
3. **Ontwerp voor kleinere documenten voor een hogere doorvoer**

    De aanvraagkosten (de kosten voor de verwerking van aanvragen) van een bepaalde bewerking zijn rechtstreeks gecorreleerd met de grootte van het document. Bewerkingen op grote documenten kosten meer dan bewerkingen voor kleine documenten.

## <a name="next-steps"></a>Volgende stappen
Zie [Partitioneren en schalen in Azure Cosmos DB](partition-data.md)voor meer informatie over het ontwerpen van uw toepassing voor schaal en hoge prestaties.
