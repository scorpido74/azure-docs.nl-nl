---
title: Azure Cosmos DB Java Async SDK diagnosticeren en oplossen
description: Gebruik functies zoals logboekregistratie aan de clientzijde en andere hulpprogramma's van derden om Azure Cosmos DB-problemen te identificeren, diagnosticeren en oplossen.
author: moderakh
ms.service: cosmos-db
ms.date: 04/30/2019
ms.author: moderakh
ms.devlang: java
ms.subservice: cosmosdb-sql
ms.topic: troubleshooting
ms.reviewer: sngun
ms.openlocfilehash: 572139743c66546622450cef8f8a0fa264d24779
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "65519983"
---
# <a name="troubleshoot-issues-when-you-use-the-java-async-sdk-with-azure-cosmos-db-sql-api-accounts"></a>Problemen oplossen bij het gebruik van de Java Async SDK met Azure Cosmos DB SQL API-accounts
In dit artikel worden veelvoorkomende problemen, tijdelijke oplossingen, diagnostische stappen en hulpprogramma's behandeld wanneer u de [Java Async SDK](sql-api-sdk-async-java.md) met Azure Cosmos DB SQL API-accounts gebruikt.
De Java Async SDK biedt logische representatie aan de clientzijde om toegang te krijgen tot de SQL-API van Azure Cosmos DB. In dit artikel worden tools en benaderingen beschreven om u te helpen bij eventuele problemen.

Begin met deze lijst:

* Neem een kijkje op de [algemene problemen en oplossingen] sectie in dit artikel.
* Kijk naar de SDK, die beschikbaar is [open source op GitHub](https://github.com/Azure/azure-cosmosdb-java). Het heeft een [afdeling problemen](https://github.com/Azure/azure-cosmosdb-java/issues) die actief wordt gecontroleerd. Controleer of een soortgelijk probleem met een tijdelijke oplossing al is ingediend.
* Bekijk de [prestatietips](performance-tips-async-java.md)en volg de voorgestelde praktijken.
* Lees de rest van dit artikel, als je geen oplossing hebt gevonden. Dien vervolgens een [GitHub-probleem in.](https://github.com/Azure/azure-cosmosdb-java/issues)

## <a name="common-issues-and-workarounds"></a><a name="common-issues-workarounds"></a>Veelvoorkomende problemen en tijdelijke oplossingen

### <a name="network-issues-netty-read-timeout-failure-low-throughput-high-latency"></a>Netwerkproblemen, Netty-uitleestime-out, lage doorvoer, hoge latentie

#### <a name="general-suggestions"></a>Algemene suggesties
* Controleer of de app wordt uitgevoerd in dezelfde regio als uw Azure Cosmos DB-account. 
* Controleer het CPU-gebruik op de host waar de app wordt uitgevoerd. Als het CPU-gebruik 90 procent of meer is, voert u uw app uit op een host met een hogere configuratie. Of u de belasting op meer machines verdelen.

#### <a name="connection-throttling"></a>Verbindingsbeperking
Verbindingsbeperking kan plaatsvinden vanwege een [verbindingslimiet op een hostmachine] of uitputting van [de Azure SNAT-poort .]

##### <a name="connection-limit-on-a-host-machine"></a><a name="connection-limit-on-host"></a>Verbindingslimiet voor een hostmachine
Sommige Linux-systemen, zoals Red Hat, hebben een bovengrens voor het totale aantal geopende bestanden. Sockets in Linux worden geïmplementeerd als bestanden, dus dit aantal beperkt ook het totale aantal verbindingen.
Voer de volgende opdracht uit.

```bash
ulimit -a
```
Het aantal maximaal toegestane geopende bestanden, die worden geïdentificeerd als 'nofile', moet ten minste het dubbele zijn van de grootte van uw verbindingspool. Zie [Prestatietips voor](performance-tips-async-java.md)meer informatie.

##### <a name="azure-snat-pat-port-exhaustion"></a><a name="snat"></a>Azure SNAT (PAT) poort uitputting

Als uw app is geïmplementeerd op Azure Virtual Machines zonder openbaar IP-adres, worden standaard [Azure SNAT-poorten](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports) verbindingen gemaakt met een eindpunt buiten uw vm. Het aantal verbindingen dat van de VM naar het Azure Cosmos DB-eindpunt is toegestaan, wordt beperkt door de [Azure SNAT-configuratie](https://docs.microsoft.com/azure/load-balancer/load-balancer-outbound-connections#preallocatedports).

 Azure SNAT-poorten worden alleen gebruikt wanneer uw VM een privé-IP-adres heeft en een proces van de VM probeert verbinding te maken met een openbaar IP-adres. Er zijn twee tijdelijke oplossingen om Azure SNAT-beperking te voorkomen:

* Voeg uw Azure Cosmos DB-serviceeindpunt toe aan het subnet van uw virtuele Azure-machines-netwerk. Zie [Eindpunten voor Azure Virtual Network-service voor](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)meer informatie. 

    Wanneer het serviceeindpunt is ingeschakeld, worden de aanvragen niet langer verzonden van een openbaar IP naar Azure Cosmos DB. In plaats daarvan worden het virtuele netwerk en subnetidentiteit verzonden. Deze wijziging kan leiden tot firewall drops als alleen openbare IP's zijn toegestaan. Als u een firewall gebruikt, voegt u bij het inschakelen van het serviceeindpunt een subnet toe aan de firewall met behulp van [Virtuele Netwerk ACL's](https://docs.microsoft.com/azure/virtual-network/virtual-networks-acl).
* Wijs een openbaar IP toe aan uw Azure VM.

##### <a name="cant-reach-the-service---firewall"></a><a name="cant-connect"></a>Kan de Service niet bereiken - firewall
``ConnectTimeoutException``geeft aan dat de SDK de service niet kan bereiken.
Mogelijk krijgt u een storing die vergelijkbaar is met de volgende als bij het gebruik van de directe modus:
```
GoneException{error=null, resourceAddress='https://cdb-ms-prod-westus-fd4.documents.azure.com:14940/apps/e41242a5-2d71-5acb-2e00-5e5f744b12de/services/d8aa21a5-340b-21d4-b1a2-4a5333e7ed8a/partitions/ed028254-b613-4c2a-bf3c-14bd5eb64500/replicas/131298754052060051p//', statusCode=410, message=Message: The requested resource is no longer available at the server., getCauseInfo=[class: class io.netty.channel.ConnectTimeoutException, message: connection timed out: cdb-ms-prod-westus-fd4.documents.azure.com/101.13.12.5:14940]
```

Als u een firewall hebt die op uw app-machine wordt uitgevoerd, opent u het poortbereik van 10.000 tot 20.000 die door de directe modus worden gebruikt.
Volg ook de [verbindingslimiet op een hostmachine.](#connection-limit-on-host)

#### <a name="http-proxy"></a>HTTP-proxy

Als u een HTTP-proxy gebruikt, controleert u of deze `ConnectionPolicy`het aantal verbindingen kan ondersteunen dat is geconfigureerd in de SDK.
Anders heb je te maken met verbindingsproblemen.

#### <a name="invalid-coding-pattern-blocking-netty-io-thread"></a>Ongeldig coderingspatroon: Netty IO-thread blokkeren

De SDK gebruikt de [Netty](https://netty.io/) IO-bibliotheek om te communiceren met Azure Cosmos DB. De SDK heeft Async API's en maakt gebruik van niet-blokkerende IO API's van Netty. Het IO-werk van de SDK wordt uitgevoerd op IO Netty-threads. Het aantal IO Netty-threads is geconfigureerd als het aantal CPU-cores van de app-machine. 

De Netty IO threads zijn bedoeld om alleen te worden gebruikt voor niet-blokkerende Netty IO werk. De SDK retourneert het API-aanroepresultaat op een van de Netty IO-threads naar de code van de app. Als de app een langdurige bewerking uitvoert nadat deze resultaten op de Netty-thread heeft ontvangen, heeft de SDK mogelijk niet genoeg IO-threads om zijn interne IO-werk uit te voeren. Een dergelijke app-codering kan leiden tot `io.netty.handler.timeout.ReadTimeoutException` een lage doorvoer, hoge latentie en fouten. De tijdelijke oplossing is om de draad te schakelen wanneer u weet dat de bewerking tijd kost.

Kijk bijvoorbeeld eens naar het volgende codefragment. U langdurig werk uitvoeren dat meer dan een paar milliseconden duurt op de Netty-thread. Als dat zo is, u uiteindelijk in een toestand waar geen Netty IO-thread aanwezig is om IO-werk te verwerken. Als gevolg hiervan krijgt u een Fout readtimeoutuitzondering.
```java
@Test
public void badCodeWithReadTimeoutException() throws Exception {
    int requestTimeoutInSeconds = 10;

    ConnectionPolicy policy = new ConnectionPolicy();
    policy.setRequestTimeoutInMillis(requestTimeoutInSeconds * 1000);

    AsyncDocumentClient testClient = new AsyncDocumentClient.Builder()
            .withServiceEndpoint(TestConfigurations.HOST)
            .withMasterKeyOrResourceToken(TestConfigurations.MASTER_KEY)
            .withConnectionPolicy(policy)
            .build();

    int numberOfCpuCores = Runtime.getRuntime().availableProcessors();
    int numberOfConcurrentWork = numberOfCpuCores + 1;
    CountDownLatch latch = new CountDownLatch(numberOfConcurrentWork);
    AtomicInteger failureCount = new AtomicInteger();

    for (int i = 0; i < numberOfConcurrentWork; i++) {
        Document docDefinition = getDocumentDefinition();
        Observable<ResourceResponse<Document>> createObservable = testClient
                .createDocument(getCollectionLink(), docDefinition, null, false);
        createObservable.subscribe(r -> {
                    try {
                        // Time-consuming work is, for example,
                        // writing to a file, computationally heavy work, or just sleep.
                        // Basically, it's anything that takes more than a few milliseconds.
                        // Doing such operations on the IO Netty thread
                        // without a proper scheduler will cause problems.
                        // The subscriber will get a ReadTimeoutException failure.
                        TimeUnit.SECONDS.sleep(2 * requestTimeoutInSeconds);
                    } catch (Exception e) {
                    }
                },

                exception -> {
                    //It will be io.netty.handler.timeout.ReadTimeoutException.
                    exception.printStackTrace();
                    failureCount.incrementAndGet();
                    latch.countDown();
                },
                () -> {
                    latch.countDown();
                });
    }

    latch.await();
    assertThat(failureCount.get()).isGreaterThan(0);
}
```
   De tijdelijke oplossing is het wijzigen van de thread waarop u werk uitvoert dat tijd kost. Definieer een singleton-exemplaar van de planner voor uw app.
   ```java
// Have a singleton instance of an executor and a scheduler.
ExecutorService ex  = Executors.newFixedThreadPool(30);
Scheduler customScheduler = rx.schedulers.Schedulers.from(ex);
   ```
   Mogelijk moet u werk doen dat tijd kost, bijvoorbeeld rekenkundig zwaar werk of het blokkeren van IO. Schakel in dit geval de thread over `customScheduler` naar `.observeOn(customScheduler)` een werknemer die door u wordt geleverd met behulp van de API.
```java
Observable<ResourceResponse<Document>> createObservable = client
        .createDocument(getCollectionLink(), docDefinition, null, false);

createObservable
        .observeOn(customScheduler) // Switches the thread.
        .subscribe(
            // ...
        );
```
Door `observeOn(customScheduler)`gebruik te maken, laat u de Netty IO-thread los en schakelt u over naar uw eigen aangepaste thread die door de aangepaste planner wordt geleverd. Deze wijziging lost het probleem op. Je krijgt geen `io.netty.handler.timeout.ReadTimeoutException` mislukking meer.

### <a name="connection-pool-exhausted-issue"></a>Verbindingspool uitgeput probleem

`PoolExhaustedException`is een client-side mislukking. Deze fout geeft aan dat de werkbelasting van uw app hoger is dan wat de SDK-verbindingsgroep kan weergeven. Verhoog de grootte van de verbindingsgroep of verdeel de belasting over meerdere apps.

### <a name="request-rate-too-large"></a>Aanvraagtarief te groot
Deze fout is een server-side storing. Dit geeft aan dat u uw ingerichte doorvoer hebt verbruikt. Probeer het later opnieuw. Als u deze fout vaak krijgt, moet u rekening houden met een verhoging van de doorvoer van verzamelingen.

### <a name="failure-connecting-to-azure-cosmos-db-emulator"></a>Geen verbinding maken met Azure Cosmos DB-emulator

Het HTTPS-certificaat azure cosmos DB-emulator is zelf ondertekend. Als de SDK met de emulator wil werken, importeert u het emulatorcertificaat naar een Java TrustStore. Zie [Azure Cosmos DB-emulatorcertificaten exporteren](local-emulator-export-ssl-certificates.md)voor meer informatie .

### <a name="dependency-conflict-issues"></a>Conflictproblemen met afhankelijkheid

```console
Exception in thread "main" java.lang.NoSuchMethodError: rx.Observable.toSingle()Lrx/Single;
```

De bovenstaande uitzondering suggereert dat u afhankelijk bent van een oudere versie van RxJava lib (bijvoorbeeld 1.2.2). Onze SDK is gebaseerd op RxJava 1.3.8 die API's niet beschikbaar in eerdere versie van RxJava heeft. 

De tijdelijke oplossing voor dergelijke issuses is om te bepalen welke andere afhankelijkheid brengt in RxJava-1.2.2 en de transitieve afhankelijkheid van RxJava-1.2.2 uit te sluiten, en laat CosmosDB SDK brengen de nieuwere versie.

Als u wilt bepalen welke bibliotheek RxJava-1.2.2 invoert, voert u de volgende opdracht uit naast uw projectpom.xml-bestand:
```bash
mvn dependency:tree
```
Zie voor meer informatie de [maven afhankelijkheidsboomgids](https://maven.apache.org/plugins/maven-dependency-plugin/examples/resolving-conflicts-using-the-dependency-tree.html).

Zodra u rxJava-1.2.2 identificeert, is er een transitieve afhankelijkheid van welke andere afhankelijkheid van uw project, u de afhankelijkheid van die lib in uw pom-bestand wijzigen en rxJava-transitieve afhankelijkheid uitsluiten:

```xml
<dependency>
  <groupId>${groupid-of-lib-which-brings-in-rxjava1.2.2}</groupId>
  <artifactId>${artifactId-of-lib-which-brings-in-rxjava1.2.2}</artifactId>
  <version>${version-of-lib-which-brings-in-rxjava1.2.2}</version>
  <exclusions>
    <exclusion>
      <groupId>io.reactivex</groupId>
      <artifactId>rxjava</artifactId>
    </exclusion>
  </exclusions>
</dependency>
```

Zie voor meer informatie de [gids voor tijdelijke afhankelijkheid uitsluiten](https://maven.apache.org/guides/introduction/introduction-to-optional-and-excludes-dependencies.html).


## <a name="enable-client-sdk-logging"></a><a name="enable-client-sice-logging"></a>SDK-logboekregistratie voor client inschakelen

De Java Async SDK gebruikt SLF4j als de logging gevel die het inloggen op populaire logging frameworks zoals log4j en logback ondersteunt.

Als u bijvoorbeeld log4j als logboekframework wilt gebruiken, voegt u de volgende libs toe in uw Java-klassepad.

```xml
<dependency>
  <groupId>org.slf4j</groupId>
  <artifactId>slf4j-log4j12</artifactId>
  <version>${slf4j.version}</version>
</dependency>
<dependency>
  <groupId>log4j</groupId>
  <artifactId>log4j</artifactId>
  <version>${log4j.version}</version>
</dependency>
```

Voeg ook een log4j config toe.
```
# this is a sample log4j configuration

# Set root logger level to DEBUG and its only appender to A1.
log4j.rootLogger=INFO, A1

log4j.category.com.microsoft.azure.cosmosdb=DEBUG
#log4j.category.io.netty=INFO
#log4j.category.io.reactivex=INFO
# A1 is set to be a ConsoleAppender.
log4j.appender.A1=org.apache.log4j.ConsoleAppender

# A1 uses PatternLayout.
log4j.appender.A1.layout=org.apache.log4j.PatternLayout
log4j.appender.A1.layout.ConversionPattern=%d %5X{pid} [%t] %-5p %c - %m%n
```

Zie voor meer informatie de [registratiehandleiding voor sfl4j.](https://www.slf4j.org/manual.html)

## <a name="os-network-statistics"></a><a name="netstats"></a>OS-netwerkstatistieken
Voer de opdracht netstat uit om een idee te `ESTABLISHED` `CLOSE_WAIT`krijgen van het aantal verbindingen in staten zoals en .

Op Linux kun je de volgende opdracht uitvoeren.
```bash
netstat -nap
```
Filter het resultaat alleen op verbindingen met het Azure Cosmos DB-eindpunt.

Het aantal verbindingen met het Azure Cosmos `ESTABLISHED` DB-eindpunt in de status mag niet groter zijn dan de grootte van de geconfigureerde verbindingsgroep.

Veel verbindingen met het Azure Cosmos DB-eindpunt zijn mogelijk in de `CLOSE_WAIT` status. Er zijn er misschien meer dan 1000. Een getal dat hoog is, geeft aan dat verbindingen snel worden hersteld en afgebroken. Deze situatie kan problemen veroorzaken. Zie de sectie [Veelvoorkomende problemen en oplossingen] voor meer informatie.

 <!--Anchors-->
[Veelvoorkomende problemen en tijdelijke oplossingen]: #common-issues-workarounds
[Enable client SDK logging]: #enable-client-sice-logging
[Verbindingslimiet voor een hostmachine]: #connection-limit-on-host
[Azure SNAT (PAT) poort uitputting]: #snat


