---
title: Azure Cosmos DB prestatietips voor Async Java
description: Lees opties voor clientconfiguratie om de prestaties van azure cosmos-database te verbeteren
author: SnehaGunda
ms.service: cosmos-db
ms.devlang: java
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: sngun
ms.openlocfilehash: b892b1f4ff73679ab425d0e97f5361e0f3712252
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549192"
---
# <a name="performance-tips-for-azure-cosmos-db-and-async-java"></a>Tips voor betere prestaties van Azure Cosmos DB en Async Java

> [!div class="op_single_selector"]
> * [Async Java](performance-tips-async-java.md)
> * [Java](performance-tips-java.md)
> * [.NET](performance-tips.md)
> 

Azure Cosmos DB is een snelle en flexibele gedistribueerde database die naadloos wordt geschaald met gegarandeerde latentie en doorvoer. U hoeft geen grote architectuurwijzigingen aan te brengen of complexe code te schrijven om uw database te schalen met Azure Cosmos DB. Op- en afschalen is net zo eenvoudig als het aanroepen van één API-aanroep of SDK-methode. Omdat Azure Cosmos DB echter wordt benaderd via netwerkaanroep, zijn er optimalisaties aan de clientzijde die u maken om topprestaties te bereiken bij het gebruik van de [SQL Async Java SDK.](sql-api-sdk-async-java.md)

Dus als je vraagt "Hoe kan ik mijn databaseprestaties verbeteren?" overweeg de volgende opties:

## <a name="networking"></a>Netwerken

* **Verbindingsmodus: Direct-modus gebruiken**
<a id="direct-connection"></a>
    
    Hoe een client verbinding maakt met Azure Cosmos DB heeft belangrijke implicaties voor de prestaties, vooral op het gebied van latentie aan clientzijde. De *ConnectionMode* is een belangrijke configuratie-instelling die beschikbaar is voor het configureren van het *clientverbindingsbeleid.* Voor Async Java SDK zijn de twee beschikbare verbindingsmodi:  
      
    * [Gateway (standaard)](/java/api/com.microsoft.azure.cosmosdb.connectionmode)  
    * [Direct](/java/api/com.microsoft.azure.cosmosdb.connectionmode)

    De gatewaymodus wordt op alle SDK-platforms ondersteund en is standaard de geconfigureerde optie. Als uw toepassingen worden uitgevoerd binnen een bedrijfsnetwerk met strikte firewallbeperkingen, is de gatewaymodus de beste keuze omdat deze de standaard HTTPS-poort en één eindpunt gebruikt. De prestatieafweging is echter dat de Gateway-modus een extra netwerkhop omvat elke keer dat gegevens worden gelezen of geschreven naar Azure Cosmos DB. Hierdoor biedt de Direct-modus betere prestaties door minder netwerkhops.

    De *ConnectionMode* is geconfigureerd tijdens het bouwen van de *instantie DocumentClient* met de parameter *ConnectionPolicy.*
    
    ```java
        public ConnectionPolicy getConnectionPolicy() {
          ConnectionPolicy policy = new ConnectionPolicy();
          policy.setConnectionMode(ConnectionMode.Direct);
          policy.setMaxPoolSize(1000);
          return policy;
        }

        ConnectionPolicy connectionPolicy = new ConnectionPolicy();
        DocumentClient client = new DocumentClient(HOST, MASTER_KEY, connectionPolicy, null);
    ```

* **Clients in dezelfde Azure-regio verzamelen voor prestaties**<a id="same-region"></a>

    Plaats, indien mogelijk, toepassingen die Azure Cosmos DB aanroepen in dezelfde regio als de Azure Cosmos-database. Voor een vergelijking, gesprekken naar Azure Cosmos DB binnen dezelfde regio worden voltooid binnen 1-2 ms, maar de latentie tussen de westkust en de oostkust van de VS is >50 ms. Deze latentie kan waarschijnlijk variëren van aanvraag tot aanvraag, afhankelijk van de route die door de aanvraag wordt genomen wanneer deze van de client naar de Azure-datacentergrens gaat. De laagst mogelijke latentie wordt bereikt door ervoor te zorgen dat de aanroepende toepassing zich binnen dezelfde Azure-regio bevindt als het ingerichte Azure Cosmos DB-eindpunt. Zie [Azure-regio's](https://azure.microsoft.com/regions/#services)voor een lijst met beschikbare regio's .

    ![Illustratie van het Azure Cosmos DB-verbindingsbeleid](./media/performance-tips/same-region.png)

## <a name="sdk-usage"></a>SDK-gebruik
* **Installeer de meest recente SDK**

    De Azure Cosmos DB SDKs worden voortdurend verbeterd om de beste prestaties te bieden. Bekijk de [Azure Cosmos DB SDK-pagina's](sql-api-sdk-async-java.md) om de meest recente SDK-pagina's te bepalen en verbeteringen te bekijken.

* **Een singleton Azure Cosmos DB-client gebruiken voor de levensduur van uw toepassing**

    Elk AsyncDocumentClient-exemplaar is draadveilig en voert efficiënt verbindingsbeheer en adrescache uit. Om efficiënt verbindingsbeheer en betere prestaties door AsyncDocumentClient mogelijk te maken, wordt aanbevolen om één exemplaar van AsyncDocumentClient per AppDomain te gebruiken voor de levensduur van de toepassing.

   <a id="max-connection"></a>

* **Verbindingsbeleid afstemmen**

    Standaard worden Cosmos DB-aanvragen in de directmodus via TCP ingediend bij het gebruik van de Async Java SDK. Intern maakt de SDK gebruik van een speciale Direct-modusarchitectuur om netwerkbronnen dynamisch te beheren en de beste prestaties te krijgen.

    In de Async Java SDK is de Direct-modus de beste keuze om de databaseprestaties met de meeste workloads te verbeteren. 

    * ***Overzicht van de direct-modus***

        ![Illustratie van de architectuur van de direct-modus](./media/performance-tips-async-java/rntbdtransportclient.png)

        De client-side architectuur die wordt gebruikt in de Direct-modus maakt voorspelbaar netwerkgebruik en multiplexed toegang tot Azure Cosmos DB-replica's mogelijk. Het bovenstaande diagram laat zien hoe de modus Direct clientaanvragen naar replica's leidt in de cosmos DB-backend. De architectuur in de modus Direct kent maximaal 10 **kanalen toe** aan de clientzijde per DB-replica. Een kanaal is een TCP-verbinding voorafgegaan door een aanvraagbuffer, die 30 aanvragen diep is. De kanalen van een replica worden indien nodig dynamisch toegewezen door het **serviceeindpunt van**de replica. Wanneer de gebruiker een aanvraag in de directmodus uitbrengt, leidt de **TransportClient** de aanvraag naar het juiste serviceeindpunt op basis van de partitiesleutel. De **wachtrij aanvragen** buffert aanvragen vóór het serviceeindpunt.

    * ***Opties voor verbindingsbeleid voor de modus Direct***

        Gebruik als eerste stap de volgende aanbevolen configuratie-instellingen hieronder. Neem contact op met het [Azure Cosmos DB-team](mailto:CosmosDBPerformanceSupport@service.microsoft.com) als u problemen ondervindt over dit specifieke onderwerp.

        Als u Azure Cosmos DB gebruikt als referentiedatabase (dat wil zeggen dat de database wordt gebruikt voor veel puntenleesbewerkingen en weinig schrijfbewerkingen), kan het aanvaardbaar zijn om *idleEndpointTimeout* in te stellen op 0 (dat wil zeggen, geen time-out).


        | Configuratieoptie       | Standaard    |
        | :------------------:       | :-----:    |
        | bufferPageSize             | 8192       |
        | Connectiontimeout          | "PT1M"     |
        | idleChannelTimeout         | "PT0S"     |
        | idleEndpointTime-out        | "PT1M10S"  |
        | maxBufferCapaciteit          | 8388608    |
        | maxChannelsPerEndpoint     | 10         |
        | maxRequestsPerChannel      | 30         |
        | receiveHangDetectionTime   | "PT1M5S"   |
        | requestExpiryInterval      | "PT5S"     |
        | requestTime-out             | "PT1M"     |
        | requestTimerResolution     | "PT0.5S"   |
        | sendHangDetectionTime sendHangDetectionTime      | "PT10S"    |
        | afsluitTime-out            | "PT15S"    |

    * ***Programmeertips voor de direct-modus***

        Bekijk het artikel Azure Cosmos DB [Async Java SDK Troubleshooting](troubleshoot-java-async-sdk.md) als basislijn voor het oplossen van Async Java SDK-problemen.

        Enkele belangrijke programmeertips bij gebruik van de Direct-modus:

        + **Gebruik multithreading in uw toepassing voor efficiënte TCP-gegevensoverdracht** - Nadat u een aanvraag hebt ingediend, moet uw toepassing zich abonneren om gegevens over een andere thread te ontvangen. Als u dit niet doet, wordt onbedoeld "half-duplex"-bewerking en worden de daaropvolgende verzoeken geblokkeerd in afwachting van het antwoord van het vorige verzoek.

        + **Compute-intensieve workloads uitvoeren op een speciale thread** - Om vergelijkbare redenen als de vorige tip kunnen bewerkingen zoals complexe gegevensverwerking het best in een afzonderlijke thread worden geplaatst. Een aanvraag die gegevens uit een ander gegevensarchief ophaalt (bijvoorbeeld als de thread azure cosmos DB- en Spark-gegevensopslag tegelijkertijd gebruikt) kan een verhoogde latentie ervaren en het wordt aanbevolen om een extra thread te spawnen die wacht op een reactie van het andere gegevensarchief.

            + Het onderliggende netwerk IO in de Async Java SDK wordt beheerd door Netty, zie deze [tips voor het vermijden van coderingspatronen die Netty IO-threads blokkeren.](troubleshoot-java-async-sdk.md#invalid-coding-pattern-blocking-netty-io-thread)

        + **Gegevensmodellering** - De Azure Cosmos DB SLA gaat ervan uit dat de documentgrootte kleiner is dan 1 KB. Het optimaliseren van uw gegevensmodel en programmeren ten gunste van kleinere documentgrootte zal over het algemeen leiden tot verminderde latentie. Als u opslag en ophalen van documenten van meer dan 1 KB nodig hebt, is de aanbevolen benadering dat documenten worden gekoppeld aan gegevens in Azure Blob Storage.


* **Parallelle query's afstemmen voor partitieverzamelingen**

    Azure Cosmos DB SQL Async Java SDK ondersteunt parallelle query's, waarmee u een gepartitioneerde verzameling parallel opvragen. Zie [codevoorbeelden](https://github.com/Azure/azure-cosmosdb-java/tree/master/examples/src/test/java/com/microsoft/azure/cosmosdb/rx/examples) met betrekking tot het werken met de SDK's voor meer informatie. Parallelle query's zijn ontworpen om de latentie en doorvoer van query's over hun seriële tegenhanger te verbeteren.

    * ***Tuning setMaxDegreeOfParallelisme\:***
    
        Parallelle query's werken door meerdere partities parallel op te vragen. Gegevens uit een afzonderlijke partitieverzameling worden echter serieel opgehaald met betrekking tot de query. Dus, gebruik setMaxDegreeOfParallelism om het aantal partities dat de maximale kans op het bereiken van de meest performante query heeft ingesteld, op voorwaarde dat alle andere systeemomstandigheden hetzelfde blijven. Als u het aantal partities niet kent, u setMaxDegreeOfParallelisme gebruiken om een hoog getal in te stellen en kiest het systeem het minimum (aantal partities, door de gebruiker geleverde invoer) als de maximale mate van parallellisme.

        Het is belangrijk op te merken dat parallelle query's de beste voordelen opleveren als de gegevens gelijkmatig over alle partities worden verdeeld met betrekking tot de query. Als de partitieverzameling zodanig is verdeeld dat alle of een meerderheid van de gegevens die door een query worden geretourneerd, is geconcentreerd in een paar partities (één partitie in het ergste geval), dan zou de uitvoering van de query worden knelpunten door deze partities.

    * ***TuningsetMaxBufferedItemCount\:***
    
        Parallelle query is ontworpen om resultaten vooraf op te halen terwijl de huidige batch met resultaten wordt verwerkt door de client. Het vooraf ophalen helpt bij algehele latentieverbetering van een query. setMaxBufferedItemCount beperkt het aantal vooraf opgehaalde resultaten. Als u het instellen van setMaxBufferedItemCount instelt op het verwachte aantal geretourneerde resultaten (of een hoger aantal) kan de query maximaal voordeel ontvangen van vooraf ophalen.

        Pre-fetching werkt op dezelfde manier, ongeacht het MaxDegreeOfParallelisme, en er is een enkele buffer voor de gegevens van alle partities.

* **Backoff implementeren met intervallen getRetryAfterInMilliseconds**

    Tijdens prestatietests moet u de belasting verhogen totdat een klein aantal aanvragen wordt beperkt. Als de clienttoepassing wordt beperkt, moet deze worden afgebroken voor het door de server opgegeven interval voor het opnieuw proberen. Het respecteren van de backoff zorgt ervoor dat u minimaal veel tijd doorbrengt met wachten tussen nieuwe pogingen.

* **Uw client-workload uitschalen**

    Als u test op hoge doorvoerniveaus (>50.000 RU/s), kan de clienttoepassing het knelpunt worden als gevolg van het afdekken van de machine op CPU of netwerkgebruik. Als u dit punt bereikt, u het Azure Cosmos DB-account verder pushen door uw clienttoepassingen op meerdere servers uit te schalen.

* **Op naam gebaseerde adressering gebruiken**

    Gebruik op naam gebaseerde adressering, waarbij koppelingen de indeling `dbs/MyDatabaseId/colls/MyCollectionId/docs/MyDocumentId`hebben, in plaats van SelfLinks (zelf),\_die de indeling `dbs/<database_rid>/colls/<collection_rid>/docs/<document_rid>` hebben om te voorkomen dat resourceids worden opgehaald van alle resources die worden gebruikt om de koppeling te construeren. Ook, als deze middelen krijgen nagemaakt (eventueel met dezelfde naam), caching ze kan niet helpen.

   <a id="tune-page-size"></a>

* **De paginagrootte afstemmen voor query's/leesfeeds voor betere prestaties**

    Bij het uitvoeren van een bulkread van documenten met behulp van leesfeedfunctionaliteit (bijvoorbeeld leesdocumenten) of bij het uitgeven van een SQL-query, worden de resultaten gesegmenteerd geretourneerd als de resultaatset te groot is. Standaard worden de resultaten geretourneerd in brokken van 100 items of 1 MB, welke limiet het eerst wordt bereikt.

    Om het aantal netwerkretouren dat nodig is om alle toepasselijke resultaten op te halen te verminderen, u de paginagrootte verhogen met de [x-ms-max-item-count-aanvraagkop](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) tot 1000. In gevallen waarin u slechts een paar resultaten hoeft weer te geven, bijvoorbeeld als uw gebruikersinterface of toepassings-API slechts 10 resultaten per keer retourneert, u ook de paginagrootte verlagen tot 10 om de doorvoer die wordt verbruikt voor lees- en query's te verminderen.

    U het paginaformaat ook instellen met de methode setMaxItemCount.

* **Geschikte scheduler gebruiken (Vermijd het stelen van GEBEURTENISlus IO Netty-threads)**

    De Async Java SDK maakt gebruik [van netty](https://netty.io/) voor niet-blokkerende IO. De SDK maakt gebruik van een vast aantal IO netty event loop threads (zoveel CPU-cores die uw machine heeft) voor het uitvoeren van IO-bewerkingen. De door API geretourneerde waarneembare wordt het resultaat op een van de netty-threads van de gedeelde IO-gebeurtenislus uitgevoerd. Dus het is belangrijk om niet te blokkeren de gedeelde IO event loop netty threads. Het uitvoeren van CPU-intensieve werkzaamheden of het blokkeren van de werking op de netty thread van de IO-gebeurtenislus kan een impasse veroorzaken of de SDK-doorvoer aanzienlijk verminderen.

    Bijvoorbeeld de volgende code voert een cpu intensief werk op de gebeurtenis lus IO netty thread:

    ```java
    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribe(
      resourceResponse -> {
        //this is executed on eventloop IO netty thread.
        //the eventloop thread is shared and is meant to return back quickly.
        //
        // DON'T do this on eventloop IO netty thread.
        veryCpuIntensiveWork();
      });
    ```

    Nadat het resultaat is ontvangen als u wilt CPU intensief werk te doen op het resultaat moet u voorkomen dat dit te doen op event loop IO netty thread. U in plaats daarvan uw eigen Scheduler om uw eigen thread voor het uitvoeren van uw werk.

    ```java
    import rx.schedulers;

    Observable<ResourceResponse<Document>> createDocObs = asyncDocumentClient.createDocument(
      collectionLink, document, null, true);

    createDocObs.subscribeOn(Schedulers.computation())
    subscribe(
      resourceResponse -> {
        // this is executed on threads provided by Scheduler.computation()
        // Schedulers.computation() should be used only when:
        //   1. The work is cpu intensive 
        //   2. You are not doing blocking IO, thread sleep, etc. in this thread against other resources.
        veryCpuIntensiveWork();
      });
    ```

    Op basis van het type werk moet u de juiste bestaande RxJava Scheduler gebruiken voor uw werk. Lees [``Schedulers``](http://reactivex.io/RxJava/1.x/javadoc/rx/schedulers/Schedulers.html)hier .

    Kijk voor meer informatie op de [GitHub-pagina](https://github.com/Azure/azure-cosmosdb-java) voor Async Java SDK.

* **Logboekregistratie van Netty uitschakelen**

    Netty library logging is spraakzaam en moet worden uitgeschakeld (het onderdrukken van teken in de configuratie is mogelijk niet genoeg) om extra CPU-kosten te voorkomen. Als u zich niet in de foutopsporingsmodus bevindt, schakelt u de logboekregistratie van Netty helemaal uit. Dus als u log4j gebruikt om de extra ``org.apache.log4j.Category.callAppenders()`` CPU-kosten van netty te verwijderen, voegt u de volgende regel toe aan uw codebase:

    ```java
    org.apache.log4j.Logger.getLogger("io.netty").setLevel(org.apache.log4j.Level.OFF);
    ```

 * **Bronlimiet voor bestanden openen**
 
    Sommige Linux-systemen (zoals Red Hat) hebben een bovengrens voor het aantal open bestanden en dus het totale aantal verbindingen. Voer het volgende uit om de huidige limieten weer te geven:

    ```bash
    ulimit -a
    ```

    Het aantal geopende bestanden (nofile) moet groot genoeg zijn om voldoende ruimte te hebben voor de grootte van uw geconfigureerde verbindingspool en andere geopende bestanden door het besturingssysteem. Het kan worden gewijzigd om een grotere grootte van de verbindingspool toe te staan.

    Open het bestand limits.conf:

    ```bash
    vim /etc/security/limits.conf
    ```
    
    De volgende regels toevoegen/wijzigen:

    ```
    * - nofile 100000
    ```

* **Native TLS/SSL-implementatie gebruiken voor netty**

    Netty kan OpenSSL rechtstreeks gebruiken voor TLS-implementatiestack om betere prestaties te bereiken. Bij afwezigheid van deze configuratie netty zal terugvallen naar standaard TLS-implementatie van Java.

    op Ubuntu:
    ```bash
    sudo apt-get install openssl
    sudo apt-get install libapr1
    ```

    en voeg de volgende afhankelijkheid toe aan uw projectafhankelijkheden:
    ```xml
    <dependency>
      <groupId>io.netty</groupId>
      <artifactId>netty-tcnative</artifactId>
      <version>2.0.20.Final</version>
      <classifier>linux-x86_64</classifier>
    </dependency>
    ```

Voor andere platforms (Red Hat, Windows, Mac, enz.) verwijzen naar deze instructieshttps://netty.io/wiki/forked-tomcat-native.html

## <a name="indexing-policy"></a>Indexeringsbeleid
 
* **Ongebruikte paden uitsluiten van indexering voor snellere schrijfbewerkingen**

    Met het indexeringsbeleid van Azure Cosmos DB u opgeven welke documentpaden u wilt opnemen of uitsluiten van indexering door gebruik te maken van indexeringspaden (setIncludedPaths en setExcludedPaths). Het gebruik van indexeringspaden kan verbeterde schrijfprestaties en lagere indexopslag bieden voor scenario's waarin de querypatronen vooraf bekend zijn, omdat indexeringskosten direct gecorreleerd zijn met het aantal geïndexeerde unieke paden. In de volgende code ziet u bijvoorbeeld hoe u een hele sectie van de documenten (ook wel substructuur genoemd) uitsluiten van indexering met behulp van de wildcard *.

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

* **Meet en stem af voor lager aanvraageenheden/secondegebruik**

    Azure Cosmos DB biedt een uitgebreide set databasebewerkingen, waaronder relationele en hiërarchische query's met UDF's, opgeslagen procedures en triggers, allemaal die werken op de documenten in een databaseverzameling. De kosten die gepaard gaan met elke bewerking hangen af van de CPU, de IO en het geheugen, vereist om de bewerking uit te voeren. In plaats van na te denken over en het beheer van hardwareresources, u een aanvraageenheid (RU) zien als één maatregel voor de resources die nodig zijn om verschillende databasebewerkingen uit te voeren en een aanvraag voor een toepassing te onderhouden.

    Doorvoer is ingericht op basis van het aantal [aanvraageenheden](request-units.md) dat voor elke container is ingesteld. Het verbruik per aanvraageenheid wordt beoordeeld als een tarief per seconde. Aanvragen die het ingerichte aanvraageenheidstarief voor hun container overschrijden, zijn beperkt totdat de koers onder het ingerichte niveau voor de container zakt. Als uw toepassing een hoger doorvoerniveau vereist, u uw doorvoer verhogen door extra aanvraageenheden in te richten.

    De complexiteit van een query is van invloed op het aantal aanvraageenheden dat voor een bewerking wordt verbruikt. Het aantal predicaten, de aard van de predicaten, het aantal UDF's en de grootte van de brongegevensset hebben allemaal invloed op de kosten van querybewerkingen.

    Als u de overhead van een bewerking wilt meten (maken, bijwerken of verwijderen), controleert u de [x-ms-request-charge-header](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) om het aantal aanvraageenheden te meten dat door deze bewerkingen wordt verbruikt. U ook de eigenschap gelijkwaardige RequestCharge bekijken in ResourceResponse\<T-> of FeedResponse\<T->.

    ```Java
    ResourceResponse<Document> response = asyncClient.createDocument(collectionLink, documentDefinition, null,
                                                     false).toBlocking.single();
    response.getRequestCharge();
    ```

    De in deze koptekst geretourneerde aanvraagkosten zijn een fractie van uw ingerichte doorvoer. Als u bijvoorbeeld 2000 RU/s hebt ingericht en als de voorgaande query 1000 1KB-documenten retourneert, zijn de kosten van de bewerking 1000. Als zodanig, binnen een seconde, de server honoreert slechts twee van dergelijke verzoeken voordat de snelheid beperken van latere verzoeken. Zie [Eenheden aanvragen](request-units.md) en de calculator voor de [aanvraageenheid](https://www.documentdb.com/capacityplanner)voor meer informatie.

<a id="429"></a>
* **Tariefbeperking/aanvraagtarief te groot**

    Wanneer een client probeert de gereserveerde doorvoer voor een account te overschrijden, is er geen prestatiedegradatie op de server en geen gebruik van doorvoercapaciteit boven het gereserveerde niveau. De server beëindigt de aanvraag preventief met RequestRateTooLarge (HTTP-statuscode 429) en retourneert de [x-ms-retry-after-ms-header](/rest/api/cosmos-db/common-cosmosdb-rest-request-headers) die aangeeft hoeveel tijd de gebruiker in milliseconden moet wachten voordat de aanvraag opnieuw wordt geprobeerd.

        HTTP Status 429,
        Status Line: RequestRateTooLarge
        x-ms-retry-after-ms :100

    De SDK's vangen allemaal impliciet dit antwoord, respecteren de server-opgegeven retry-after header en proberen het verzoek opnieuw. Tenzij uw account gelijktijdig wordt geopend door meerdere clients, zal de volgende poging slagen.

    Als u meer dan één client cumulatief boven het aanvraagpercentage hebt, is het aantal standaardopnieuw proberen dat momenteel intern door de client is ingesteld op 9, mogelijk niet voldoende. in dit geval gooit de client een DocumentClientException met statuscode 429 naar de toepassing. Het aantal standaardwijzigingen kan worden gewijzigd met setRetryOptions in de instantie Verbindingsbeleid. Standaard wordt de DocumentClientException met statuscode 429 geretourneerd na een cumulatieve wachttijd van 30 seconden als de aanvraag boven het aanvraagpercentage blijft werken. Dit gebeurt zelfs wanneer het aantal huidige opnieuw proberen lager is dan het maximale aantal nieuwe try's, of het nu de standaardwaarde van 9 of een door de gebruiker gedefinieerde waarde is.

    Hoewel het geautomatiseerde gedrag voor het opnieuw proberen helpt om de veerkracht en bruikbaarheid voor de meeste toepassingen te verbeteren, kan het op gespannen voet komen te staan bij het doen van prestatiebenchmarks, vooral bij het meten van latentie. De door de client waargenomen latentie piekt als het experiment de servergaspedaal raakt en ervoor zorgt dat de client SDK stilletjes opnieuw probeert. Om latentiepieken tijdens prestatie-experimenten te voorkomen, meet u de kosten die door elke bewerking worden geretourneerd en zorgt u ervoor dat aanvragen onder het gereserveerde aanvraagpercentage werken. Zie [Eenheden aanvragen voor](request-units.md)meer informatie .

* **Ontwerp voor kleinere documenten voor een hogere doorvoer**

    De aanvraagkosten (de kosten voor de verwerking van aanvragen) van een bepaalde bewerking zijn rechtstreeks gecorreleerd met de grootte van het document. Bewerkingen op grote documenten kosten meer dan bewerkingen voor kleine documenten.

## <a name="next-steps"></a>Volgende stappen

Zie [Partitioneren en schalen in Azure Cosmos DB](partition-data.md)voor meer informatie over het ontwerpen van uw toepassing voor schaal en hoge prestaties.
