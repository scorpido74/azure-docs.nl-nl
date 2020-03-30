---
title: Problemen met time-outs voor Azure Cache voor Redis oplossen
description: Meer informatie over het oplossen van veelvoorkomende time-outproblemen met Azure Cache voor Redis, zoals het patchen van de Redis-server en de time-outuitzonderingen van StackExchange.Redis.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: 4b8cfed883ffef780de2e82e3f309e97bcb5515c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278244"
---
# <a name="troubleshoot-azure-cache-for-redis-timeouts"></a>Problemen met time-outs voor Azure Cache voor Redis oplossen

In deze sectie worden time-outproblemen besproken die optreden bij het maken van verbinding met Azure Cache voor Redis.

- [Redis-server patching](#redis-server-patching)
- [Uitzonderingen op een time-out van StackExchange.Redis](#stackexchangeredis-timeout-exceptions)

> [!NOTE]
> Verschillende van de stappen voor het oplossen van problemen in deze handleiding bevatten instructies voor het uitvoeren van Redis-opdrachten en het bewaken van verschillende prestatiestatistieken. Zie de artikelen in de sectie [Aanvullende informatie voor](#additional-information) meer informatie en instructies.
>

## <a name="redis-server-patching"></a>Redis-server patching

Azure Cache voor Redis werkt zijn serversoftware regelmatig bij als onderdeel van de beheerde servicefunctionaliteit die het biedt. Deze [patchactiviteit](cache-failover.md) vindt grotendeels achter de schermen plaats. Tijdens de failovers wanneer Redis-serverknooppunten worden gepatcht, kunnen Redis-clients die zijn verbonden met deze knooppunten tijdelijke time-outs ervaren omdat verbindingen tussen deze knooppunten worden geschakeld. Zie [Hoe heeft een failover invloed op mijn client applicatie](cache-failover.md#how-does-a-failover-affect-my-client-application) voor meer informatie over welke bijwerkingen patching kan hebben op uw toepassing en hoe u de behandeling van patching gebeurtenissen te verbeteren.

## <a name="stackexchangeredis-timeout-exceptions"></a>Uitzonderingen op een time-out van StackExchange.Redis

StackExchange.Redis gebruikt een `synctimeout` configuratie-instelling met de naam voor synchrone bewerkingen met een standaardwaarde van 1000 ms. Als een synchrone oproep in deze periode niet wordt voltooid, gooit de client StackExchange.Redis een time-outfout die vergelijkbaar is met het volgende voorbeeld:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

Dit foutbericht bevat statistieken die u kunnen wijzen op de oorzaak en mogelijke oplossing van het probleem. In de volgende tabel vindt u informatie over de statistieken van de foutmeldingen.

| Statistiek foutbericht | Details |
| --- | --- |
| Inst |In het laatste segment: er zijn 0 opdrachten uitgegeven |
| Mgr |De socket manager `socket.select`doet , wat betekent dat het vraagt het OS om een socket die iets te doen heeft aan te geven. De lezer is niet actief lezen van het netwerk, omdat het niet denkt dat er iets te doen |
| wachtrij |Er zijn in totaal 73 lopende operaties |
| Qu |6 van de lopende bewerkingen bevinden zich in de wachtrij voor niet-verzonden en zijn nog niet naar het uitgaande netwerk geschreven |
| Qs |67 van de lopende bewerkingen zijn verzonden naar de server, maar een antwoord is nog niet beschikbaar. Het antwoord `Not yet sent by the server` kan worden of`sent by the server but not yet processed by the client.` |
| Qc |0 van de lopende bewerkingen hebben antwoorden gezien, maar zijn nog niet gemarkeerd als voltooid omdat ze wachten op de voltooiingslus |
| Wr |Er is een actieve schrijver (wat betekent dat de 6 niet-verzonden aanvragen niet worden genegeerd) bytes / activewriters |
| in |Er zijn geen actieve lezers en nul bytes zijn beschikbaar om te lezen op de NIC bytes /activereaders |

U de volgende stappen gebruiken om mogelijke oorzaken te onderzoeken.

1. Als een aanbevolen praktijk, zorg ervoor dat u het volgende patroon gebruikt om verbinding te maken bij het gebruik van de StackExchange.Redis client.

    ```csharp
    private static Lazy<ConnectionMultiplexer> lazyConnection = new Lazy<ConnectionMultiplexer>(() =>
    {
        return ConnectionMultiplexer.Connect("cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...");

    });

    public static ConnectionMultiplexer Connection
    {
        get
        {
            return lazyConnection.Value;
        }
    }
    ```

    Zie [Verbinding maken met de cache met StackExchange.Redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)voor meer informatie.

1. Controleer of uw server en de clienttoepassing zich in dezelfde regio in Azure bevinden. U krijgt bijvoorbeeld time-outs wanneer uw cache zich in Oost-VS bevindt, maar de `synctimeout` client zich in West-VS bevindt en het verzoek niet binnen het interval wordt voltooid of u krijgt mogelijk time-outs wanneer u debuggen van uw lokale ontwikkelingsmachine. 

    Het wordt ten zeerste aanbevolen om de cache en in de client in dezelfde Azure-regio te hebben. Als u een scenario hebt dat kruisregioaanroepen bevat, moet u het `synctimeout` interval instellen op een `synctimeout` waarde die hoger is dan het standaard interval van 1000 ms door een eigenschap op te nemen in de verbindingstekenreeks. In het volgende voorbeeld wordt een fragment van een verbindingstekenreeks voor StackExchange.Redis weergegeven die door Azure Cache voor Redis wordt geleverd met een `synctimeout` van 2000 ms.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. Zorg ervoor dat u de nieuwste versie van het [StackExchange.Redis NuGet-pakket gebruikt.](https://www.nuget.org/packages/StackExchange.Redis/) Er zijn bugs voortdurend wordt vastgesteld in de code om het robuuster te maken om time-outs, zodat het hebben van de nieuwste versie is belangrijk.
1. Als uw aanvragen gebonden zijn aan bandbreedtebeperkingen op de server of client, duurt het langer voordat ze zijn voltooid en kunnen ze time-outs veroorzaken. Zie Bandbreedtebeperking aan de server als u wilt zien of uw [time-out](cache-troubleshoot-server.md#server-side-bandwidth-limitation)het gevolg is van netwerkbandbreedte op de server. Zie [Bandbreedtebeperking client-side](cache-troubleshoot-client.md#client-side-bandwidth-limitation)voor meer tijd om te zien of uw time-out het gevolg is van bandbreedte bandbreedte aan de clientzijde.
1. Krijg je CPU gebonden op de server of op de client?

   - Controleer of je wordt gebonden door CPU op uw client. Hoge CPU kan ertoe leiden dat `synctimeout` het verzoek niet binnen de interval wordt verwerkt en kan leiden tot een time-out van een aanvraag. Als u naar een grotere clientgrootte gaat of de belasting distribueert, u dit probleem oplossen.
   - Controleer of de CPU-binding met de server wordt uitgevoerd door de functievan de [CPU-cache-prestatiestatistiek te](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)controleren. Aanvragen die binnenkomen terwijl Redis CPU-gebonden is, kunnen ervoor zorgen dat deze aanvragen een time-out krijgen. Als u aan deze voorwaarde wilt reageren, u de belasting over meerdere shards in een premiumcache verdelen of upgraden naar een groter formaat of prijsniveau. Zie [Bandbreedtebeperking aan de serverzijde](cache-troubleshoot-server.md#server-side-bandwidth-limitation)voor meer informatie.
1. Duurt het lang duren voordat opdrachten op de server worden verwerkt? Langlopende opdrachten die lang duren om te verwerken op de redis-server kunnen time-outs veroorzaken. Zie Langlopende [opdrachten](cache-troubleshoot-server.md#long-running-commands)voor meer informatie over langlopende opdrachten. U verbinding maken met uw Azure-cache voor bijvoorbeeld Redis met de redis-cli-client of de [Redis-console.](cache-configure.md#redis-console) Voer vervolgens de opdracht [SLOWLOG](https://redis.io/commands/slowlog) uit om te zien of er aanvragen langzamer zijn dan verwacht. Redis Server en StackExchange.Redis zijn geoptimaliseerd voor veel kleine aanvragen in plaats van minder grote aanvragen. Het splitsen van uw gegevens in kleinere brokken kan hier dingen verbeteren.

    Zie het blogbericht [ASP.NET Session State Provider voor Redis Preview Release](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)voor informatie over het maken van verbinding met het SSL-eindpunt van uw cache met behulp van redis-cli en stunnel.
1. Hoge Redis-serverbelasting kan time-outs veroorzaken. U de serverbelasting `Redis Server Load` controleren door de [statistiek cacheprestaties te bewaken.](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) Een serverbelasting van 100 (maximale waarde) betekent dat de redis-server bezig is geweest, zonder inactieve tijd, het verwerken van aanvragen. Voer de opdracht SlowLog uit, zoals beschreven in de vorige alinea, om te zien of bepaalde aanvragen alle servermogelijkheden in gebruik nemen. Zie Hoog CPU-gebruik / Serverbelasting voor meer informatie.
1. Was er een andere gebeurtenis aan de client kant die kan hebben veroorzaakt een netwerk blip? Veelvoorkomende gebeurtenissen zijn: het aantal clientexemplaren omhoog of omlaag schalen, een nieuwe versie van de client implementeren of automatisch schalen ingeschakeld. In onze tests hebben we vastgesteld dat automatisch schalen of opschalen/omlaag kan leiden tot uitgaande netwerkconnectiviteit die enkele seconden verloren gaat. StackExchange.Redis code is bestand tegen dergelijke gebeurtenissen en maakt opnieuw verbinding. Tijdens het opnieuw verbinden kunnen aanvragen in de wachtrij een time-out krijgen.
1. Was er een grote aanvraag voorafgaand aan een aantal kleine aanvragen voor de cache die een time-out? De `qs` parameter in het foutbericht geeft aan hoeveel aanvragen van de client naar de server zijn verzonden, maar dat er geen antwoord is verwerkt. Deze waarde kan blijven groeien omdat StackExchange.Redis één TCP-verbinding gebruikt en slechts één reactie tegelijk kan lezen. Hoewel de eerste bewerking een time-out heeft, weerhoudt het niet dat meer gegevens naar of van de server worden verzonden. Andere aanvragen worden geblokkeerd totdat de grote aanvraag is voltooid en kan leiden tot time-outs. Een oplossing is om de kans op time-outs te minimaliseren door ervoor te zorgen dat uw cache groot genoeg is voor uw werkbelasting en grote waarden in kleinere segmenten opsplitst. Een andere mogelijke oplossing is `ConnectionMultiplexer` om een pool van objecten `ConnectionMultiplexer` in uw client te gebruiken en de minst geladen te kiezen bij het verzenden van een nieuwe aanvraag. Het laden van meerdere verbindingsobjecten moet voorkomen dat een enkele time-out ervoor zorgt dat andere aanvragen ook een time-out krijgen.
1. Als u deze `RedisSessionStateProvider`gebruikt, moet u ervoor zorgen dat u de time-out voor het opnieuw proberen correct hebt ingesteld. `retryTimeoutInMilliseconds`moeten hoger `operationTimeoutInMilliseconds`zijn dan , anders geen nieuwe pogingen optreden. In het `retryTimeoutInMilliseconds` volgende voorbeeld wordt ingesteld op 3000. Zie [ASP.NET Sessiestatusprovider voor Azure Cache voor Redis](cache-aspnet-session-state-provider.md) en [Het gebruik van de configuratieparameters van sessiestatusprovider en outputcacheprovider](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration)voor meer informatie.

    ```xml
    <add
      name="AFRedisCacheSessionStateProvider"
      type="Microsoft.Web.Redis.RedisSessionStateProvider"
      host="enbwcache.redis.cache.windows.net"
      port="6380"
      accessKey="…"
      ssl="true"
      databaseId="0"
      applicationName="AFRedisCacheSessionState"
      connectionTimeoutInMilliseconds = "5000"
      operationTimeoutInMilliseconds = "1000"
      retryTimeoutInMilliseconds="3000" />
    ```

1. Controleer het geheugengebruik op de Azure-cache `Used Memory`voor Redis-server door te [controleren](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) `Used Memory RSS` en . Als er een uitzettingsbeleid is ingevoerd, `Used_Memory` begint Redis sleutels uit te zetten wanneer de cachegrootte wordt bereikt. Idealiter `Used Memory RSS` moet slechts iets hoger `Used memory`zijn dan . Een groot verschil betekent dat er geheugenfragmentatie (intern of extern) is. Wanneer `Used Memory RSS` is `Used Memory`minder dan , betekent dit dat een deel van de cache geheugen is verwisseld door het besturingssysteem. Als deze swapping plaatsvindt, u een aantal aanzienlijke latencies verwachten. Omdat Redis geen controle heeft over hoe de toewijzingen ervan `Used Memory RSS` zijn toegewezen aan geheugenpagina's, is hoog vaak het gevolg van een piek in het geheugengebruik. Wanneer de Redis-server geheugen vrijmaakt, neemt de allocator het geheugen in, maar het kan al dan niet het geheugen teruggeven aan het systeem. Er kan een discrepantie `Used Memory` zijn tussen de waarde en het geheugenverbruik zoals gerapporteerd door het besturingssysteem. Geheugen kan zijn gebruikt en vrijgegeven door Redis, maar niet teruggegeven aan het systeem. Om geheugenproblemen te beperken, u de volgende stappen uitvoeren:

   - Upgrade de cache naar een groter formaat, zodat u niet tegen geheugenbeperkingen op het systeem ingaat.
   - Stel verlooptijden in op de toetsen, zodat oudere waarden proactief worden verwijderd.
   - Controleer `used_memory_rss` de cachestatistiek. Wanneer deze waarde de grootte van hun cache nadert, ziet u waarschijnlijk prestatieproblemen. Verdeel de gegevens over meerdere shards als u een premium cache gebruikt of upgrade naar een grotere cachegrootte.

   Zie [Geheugendruk op de Redis-server](cache-troubleshoot-server.md#memory-pressure-on-redis-server)voor meer informatie.

## <a name="additional-information"></a>Aanvullende informatie

- [Problemen met Azure Cache voor Redis aan clientzijde oplossen](cache-troubleshoot-client.md)
- [Problemen met Azure Cache voor Redis aan serverzijde oplossen](cache-troubleshoot-server.md)
- [Hoe kan ik de prestaties van mijn cache benchmarken en testen?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Azure-cache controleren op Redis](cache-how-to-monitor.md)
