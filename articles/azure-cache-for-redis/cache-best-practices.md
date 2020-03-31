---
title: Aanbevolen procedures voor Azure Cache voor Redis
description: Lees hoe u uw Azure-cache effectief gebruiken voor Redis door deze aanbevolen procedures te volgen.
author: joncole
ms.service: cache
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: joncole
ms.openlocfilehash: 71056fd04069b861b37a595b1a4f2a8bba4a01ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75689976"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Aanbevolen procedures voor Azure Cache voor Redis 
Door deze aanbevolen procedures te volgen, u helpen bij het maximaliseren van de prestaties en het kosteneffectieve gebruik van uw Azure-cache voor bijvoorbeeld Redis.

## <a name="configuration-and-concepts"></a>Configuratie en concepten
 * **Gebruik standaard- of premiumlaag voor productiesystemen.**  De basislaag is een enkel knooppuntsysteem zonder gegevensreplicatie en zonder SLA. Gebruik minimaal een C1-cache.  C0-caches zijn bedoeld voor eenvoudige dev/ testscenario's omdat ze een gedeelde CPU-kern hebben, weinig geheugen en gevoelig zijn voor "luidruchtige buren"-problemen.

 * **Vergeet niet dat Redis een gegevensarchief in het geheugen is.**  [In dit artikel](cache-troubleshoot-data-loss.md) worden enkele scenario's beschreven waarin gegevensverlies kan optreden.

 * **Ontwikkel uw systeem zodanig dat het kan omgaan met verbinding blips** [als gevolg van patchen en failover](cache-failover.md).

 * **Configureer uw [instelling die is gereserveerd voor maximaal geheugen](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) om de reactiesnelheid van het systeem** onder geheugendrukomstandigheden te verbeteren.  Een voldoende reserveringsinstelling is vooral belangrijk voor schrijfzware workloads of als u grotere waarden (100 KB of meer) opslaat in Redis. U moet beginnen met 10% van de grootte van uw cache en dit percentage verhogen als u schrijf-zware lasten hebt.

 * **Redis werkt het beste met kleinere waarden,** dus overweeg het hakken van grotere gegevens in meerdere sleutels.  In [deze Redis discussie](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/), worden enkele overwegingen vermeld die u zorgvuldig moet overwegen.  Lees [dit artikel](cache-troubleshoot-client.md#large-request-or-response-size) voor een voorbeeld probleem dat kan worden veroorzaakt door grote waarden.

 * **Zoek uw cache-exemplaar en uw toepassing in dezelfde regio.**  Als er verbinding wordt gemaakt met een cache in een andere regio, kan dit leiden tot een aanzienlijk hogere latentie en kan dit ten koste gaan van de betrouwbaarheid.  Hoewel u verbinding maken van buiten Azure, wordt dit niet *aanbevolen, vooral wanneer u Redis als cache gebruikt.*  Als u Redis gebruikt als slechts een sleutel/waardeopslag, is latentie mogelijk niet de belangrijkste zorg. 

 * **Verbindingen opnieuw gebruiken.**  Het maken van nieuwe verbindingen is duur en verhoogt de latentie, zodat verbindingen zoveel mogelijk worden hergebruikt. Als u ervoor kiest om nieuwe verbindingen te maken, moet u de oude verbindingen sluiten voordat u ze vrijgeeft (zelfs in beheerde geheugentalen zoals .NET of Java).

 * **Configureer uw clientbibliotheek om een *connect time-out* van ten minste 15 seconden te gebruiken,** waardoor het systeem zelfs onder hogere CPU-omstandigheden de tijd heeft om verbinding te maken.  Een kleine time-outwaarde voor verbindingen garandeert niet dat de verbinding in dat tijdsbestek tot stand komt.  Als er iets misgaat (hoge client CPU, hoge server CPU, enzovoort), dan is een korte verbinding time-out waarde zal leiden tot de verbinding poging om te mislukken. Dit gedrag maakt een slechte situatie vaak erger.  In plaats van te helpen, kortere time-outs verergeren het probleem door het systeem te dwingen om het proces van het proberen om opnieuw verbinding te maken, wat kan leiden tot een *verbinding-> mislukken -> opnieuw proberen* lus. We raden u over het algemeen aan om uw time-out van de verbinding op 15 seconden of hoger te laten. Het is beter om uw verbinding poging te laten slagen na 15 of 20 seconden dan om het niet snel alleen om opnieuw te proberen. Zo'n nieuwe lus kan ervoor zorgen dat uw uitval langer duurt dan wanneer u het systeem in eerste instantie gewoon langer laat duren.  
     > [!NOTE]
     > Deze richtlijnen zijn specifiek voor de *verbindingspoging* en hebben geen betrekking op de tijd die u bereid bent te wachten tot een *bewerking* als GET of SET is voltooid.
 
 * **Vermijd dure bewerkingen** - Sommige Redis-bewerkingen, zoals de [opdracht KEYS,](https://redis.io/commands/keys) zijn *erg* duur en moeten worden vermeden.  Zie voor meer informatie enkele overwegingen rond [langlopende opdrachten](cache-troubleshoot-server.md#long-running-commands)

 * **Tls-versleuteling gebruiken** - Voor Azure Cache voor Redis is tls-versleutelde communicatie standaard vereist.  TLS-versies 1.0, 1.1 en 1.2 worden momenteel ondersteund.  TLS 1.0 en 1.1 zijn echter op weg naar afschaffing industriebreed, dus gebruik TLS 1.2 indien mogelijk.  Als uw clientbibliotheek of -tool TLS niet ondersteunt, kan het inschakelen van onversleutelde verbindingen worden gedaan [via de Azure-portal](cache-configure.md#access-ports) of [beheer-API's.](https://docs.microsoft.com/rest/api/redis/redis/update)  In dergelijke gevallen waarin versleutelde verbindingen niet mogelijk zijn, wordt aanbevolen om uw cache en clienttoepassing in een virtueel netwerk te plaatsen.  Zie deze [tabel](cache-how-to-premium-vnet.md#outbound-port-requirements)voor meer informatie over welke poorten worden gebruikt in het scenario voor virtuele netwerkcache.
 
## <a name="memory-management"></a>Geheugenbeheer
Er zijn verschillende dingen met betrekking tot geheugengebruik binnen uw Redis-serverinstantie die u mogelijk wilt overwegen.  Hier zijn een paar:

 * **Kies een [uitzettingsbeleid](https://redis.io/topics/lru-cache) dat werkt voor uw toepassing.**  Het standaardbeleid voor Azure Redis is *vluchtig-lru*, wat betekent dat alleen sleutels met een TTL-waardeset in aanmerking komen voor uitzetting.  Als er geen sleutels een TTL-waarde hebben, wordt er geen sleutels verwijderd door het systeem.  Als u wilt dat het systeem om een sleutel te worden uitgezet als onder geheugendruk, dan u overwegen de *allkeys-lru* beleid.

 * **Stel een vervaldatum in op uw sleutels.**  Een expiratie verwijdert sleutels proactief in plaats van te wachten tot er geheugendruk is.  Wanneer uitzetting werkt vanwege de geheugendruk, kan dit extra belasting veroorzaken op uw server.  Zie de documentatie voor de opdrachten [VERLOPEN](https://redis.io/commands/expire) en [VERLOPENAT](https://redis.io/commands/expireat) voor meer informatie.
 
## <a name="client-library-specific-guidance"></a>Specifieke richtlijnen voor clientbibliotheek
 * [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java - Welke client moet ik gebruiken?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Sla (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [Php](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Asp.Net Sessie-statusprovider](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Wanneer is het veilig om het opnieuw te proberen?
Helaas is er geen eenvoudig antwoord.  Elke toepassing moet beslissen welke bewerkingen opnieuw kunnen worden geprobeerd en welke niet.  Elke bewerking heeft verschillende vereisten en afhankelijkheden tussen de toetsen.  Hier zijn een aantal dingen die je zou kunnen overwegen:

 * U fouten aan de clientzijde krijgen, ook al heeft Redis de opdracht die u hebt gevraagd om uit te voeren uitgevoerd.  Bijvoorbeeld:
     - Time-outs zijn een klant-side concept.  Als de bewerking de server heeft bereikt, voert de server de opdracht uit, zelfs als de client het wachten opgeeft.  
     - Wanneer er een fout optreedt op de socketverbinding, is het niet mogelijk om te weten of de bewerking daadwerkelijk op de server is uitgevoerd.  De verbindingsfout kan bijvoorbeeld optreden nadat de server de aanvraag heeft verwerkt, maar voordat de client het antwoord ontvangt.
 *  Hoe reageert mijn toepassing als ik per ongeluk twee keer dezelfde bewerking uitvoert?  Wat als ik bijvoorbeeld een geheel getal twee keer in plaats van één keer toename?  Is mijn aanvraag schrijven naar dezelfde sleutel van meerdere plaatsen?  Wat gebeurt er als mijn logica opnieuw proberen een waarde overschrijft die is ingesteld door een ander deel van mijn app?

Als u wilt testen hoe uw code werkt onder foutvoorwaarden, u overwegen de [functie Opnieuw opstarten](cache-administration.md#reboot)te gebruiken. Met rebooten u zien welke invloed is op de verbindingsblips op uw toepassing.

## <a name="performance-testing"></a>Prestaties testen
 * **Begin met `redis-benchmark.exe` het gebruik van** een gevoel voor mogelijke doorvoer / latentie voor het schrijven van uw eigen perf tests.  Redis-benchmark documentatie is hier te [vinden.](https://redis.io/topics/benchmarks)  Houd er rekening mee dat redis-benchmark geen SSL ondersteunt, dus u moet [de niet-SSL-poort via de Portal inschakelen](cache-configure.md#access-ports) voordat u de test uitvoert.  [Een windows compatibele versie van redis-benchmark.exe is hier te vinden](https://github.com/MSOpenTech/redis/releases)
 * De client-VM die wordt gebruikt voor het testen moet **zich in dezelfde regio bevinden** als de instantie van de Redis-cache.
 * **We raden u aan dv2 VM-serie** voor uw klant te gebruiken, omdat ze betere hardware hebben en de beste resultaten zullen geven.
 * Zorg ervoor dat de client-VM die u gebruikt **minstens evenveel rekenkracht en bandbreedte* heeft als de cache die wordt getest. 
 * **Schakel VRSS** in op de clientmachine als u windows gebruikt.  [Zie hier voor meer informatie](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx).  Voorbeeld powershell script:
     >PowerShell -ExecutionPolicy Unrestricted enable-netadapterRSS -name (get-netadapter). Naam 
     
 * **Overweeg de exemplaren van De Premium-laag-Roodis te gebruiken.**  Deze cacheformaten hebben een betere netwerklatentie en doorvoer omdat ze werken op betere hardware voor zowel CPU als netwerk.
 
     > [!NOTE]
     > Onze waargenomen prestatieresultaten worden [hier gepubliceerd](cache-faq.md#azure-cache-for-redis-performance) ter referentie.   Houd er ook rekening mee dat SSL/TLS wat overhead toevoegt, zodat u mogelijk andere latencies en/of doorvoer krijgt als u transportversleuteling gebruikt.
 
### <a name="redis-benchmark-examples"></a>Voorbeelden van Redis-Benchmark
**Pre-test setup**: Bereid de cache-instantie voor met gegevens die nodig zijn voor de onderstaande latentie- en doorvoertestopdrachten.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t SET -n 10 -d 1024 

**Latentie testen:** Test GET-aanvragen met een 1k-payload.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -d 1024 -P 50 -c 4

**Ga als het gaat om een testdoorvoer:** Pipelined GET verzoeken met 1k payload.
> redis-benchmark.exe -h yourcache.redis.cache.windows.net -a yourAccesskey -t GET -n 1000000 -d 1024 -P 50 -c 50
