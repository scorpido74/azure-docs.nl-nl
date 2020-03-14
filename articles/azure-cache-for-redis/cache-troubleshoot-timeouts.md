---
title: Problemen met Azure-cache oplossen voor redis-outs
description: Meer informatie over het oplossen van veelvoorkomende time-outproblemen met Azure cache voor redis, zoals redis server patches en stack Exchange. redis time-out-uitzonde ringen.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: 4b8cfed883ffef780de2e82e3f309e97bcb5515c
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/13/2020
ms.locfileid: "79278244"
---
# <a name="troubleshoot-azure-cache-for-redis-timeouts"></a>Problemen met Azure-cache oplossen voor redis-outs

In deze sectie vindt u informatie over het oplossen van problemen met de time-out bij het maken van verbinding met Azure cache voor redis.

- [Redis-server patches](#redis-server-patching)
- [Uitzonde ringen voor stack Exchange. redis-out](#stackexchangeredis-timeout-exceptions)

> [!NOTE]
> Enkele van de stappen voor probleem oplossing in deze hand leiding bevatten instructies voor het uitvoeren van redis-opdrachten en het controleren van verschillende prestatie gegevens. Zie de artikelen in de sectie met [aanvullende informatie](#additional-information) voor meer informatie en instructies.
>

## <a name="redis-server-patching"></a>Redis-server patches

Met Azure cache voor redis worden de server software regel matig bijgewerkt als onderdeel van de beheerde service-functionaliteit. Deze [patching](cache-failover.md) -activiteit vindt grotendeels achter de scène plaats. Tijdens de failovers wanneer er redis-server knooppunten worden bijgewerkt, kunnen redis-clients die zijn verbonden met deze knoop punten, tijdelijke time-outs ondervinden als verbindingen tussen deze knoop punten worden overgeschakeld. Bekijk [hoe een failover van invloed is op mijn client toepassing](cache-failover.md#how-does-a-failover-affect-my-client-application) voor meer informatie over welke neven effecten patches kunnen hebben voor uw toepassing en hoe u de verwerking van patch gebeurtenissen kunt verbeteren.

## <a name="stackexchangeredis-timeout-exceptions"></a>Uitzonde ringen voor stack Exchange. redis-out

Stack Exchange. redis maakt gebruik van een configuratie-instelling met de naam `synctimeout` voor synchrone bewerkingen met een standaard waarde van 1000 MS. Als een synchrone aanroep in deze periode niet is voltooid, genereert de stack Exchange. redis-client een time-outfout die vergelijkbaar is met het volgende voor beeld:

    System.TimeoutException: Timeout performing MGET 2728cc84-58ae-406b-8ec8-3f962419f641, inst: 1,mgr: Inactive, queue: 73, qu=6, qs=67, qc=0, wr=1/1, in=0/0 IOCP: (Busy=6, Free=999, Min=2,Max=1000), WORKER (Busy=7,Free=8184,Min=2,Max=8191)

Dit fout bericht bevat metrische gegevens die u kunnen helpen bij de oorzaak en mogelijke oplossing van het probleem. De volgende tabel bevat details over de metrische gegevens over het fout bericht.

| Metrische fout bericht | Details |
| --- | --- |
| inst |In de laatste keer dat het segment: 0 opdrachten zijn uitgegeven |
| beheer |De socket Manager doet `socket.select`, wat betekent dat het besturings systeem vraagt om een socket te geven die iets te doen heeft. De lezer is niet actief bij het lezen van het netwerk, omdat u niet kunt zien dat er iets te doen is |
| wachtrij |Er zijn 73 totaal aantal bewerkingen in uitvoering |
| qu |6 van de bewerkingen in uitvoering bevinden zich in de niet-verzonden wachtrij en zijn nog niet naar het uitgaande netwerk geschreven |
| qs |67 van de bewerkingen die momenteel worden uitgevoerd, zijn verzonden naar de server, maar er is nog geen antwoord beschikbaar. Het antwoord kan worden `Not yet sent by the server` of `sent by the server but not yet processed by the client.` |
| qc |0 van de bewerkingen die worden uitgevoerd, hebben geen antwoorden gezien, maar zijn nog niet als voltooid gemarkeerd, omdat ze wachten op de voltooiings lus |
| WR |Er is een actieve schrijver (wat betekent dat de 6 niet-verzonden aanvragen niet worden genegeerd) bytes/activewriters |
| in |Er zijn geen actieve lezers en er zijn geen null-bytes beschikbaar om te worden gelezen op het NIC-aantal bytes/activereaders |

U kunt de volgende stappen gebruiken om mogelijke hoofd oorzaken te onderzoeken.

1. Zorg er als best practice voor dat u het volgende patroon gebruikt om verbinding te maken wanneer u de stack Exchange. redis-client gebruikt.

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

    Zie [verbinding maken met de cache met behulp van stack Exchange. redis](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)voor meer informatie.

1. Zorg ervoor dat uw server en de client toepassing zich in dezelfde regio bevinden als Azure. U krijgt bijvoorbeeld time-outs wanneer uw cache zich in VS-Oost bevindt, maar de client is in VS West en de aanvraag wordt niet voltooid binnen het `synctimeout` interval of u krijgt time-outs wanneer u fouten opspoort vanuit de lokale ontwikkel machine. 

    Het is raadzaam om de cache en de client in dezelfde Azure-regio te hebben. Als u een scenario hebt dat meerdere regio aanroepen bevat, moet u het `synctimeout` interval instellen op een waarde die hoger is dan het standaard interval van 1000 MS door een `synctimeout` eigenschap op te nemen in de connection string. In het volgende voor beeld ziet u een fragment van een connection string voor stack Exchange. redis van Azure cache voor redis met een `synctimeout` van 2000 MS.

        synctimeout=2000,cachename.redis.cache.windows.net,abortConnect=false,ssl=true,password=...
1. Zorg ervoor dat u de nieuwste versie van het [stack Exchange. redis NuGet-pakket](https://www.nuget.org/packages/StackExchange.Redis/)gebruikt. Er zijn fouten die voortdurend worden opgelost in de code, zodat deze robuuster is voor time-outs zodat de meest recente versie van belang is.
1. Als uw aanvragen zijn gebonden door beperkingen van de band breedte op de server of client, duurt het langer voordat deze zijn voltooid en kunnen er time-outs optreden. Zie [bandbreedte beperking aan de server zijde](cache-troubleshoot-server.md#server-side-bandwidth-limitation)om te controleren of uw time-out wordt veroorzaakt door netwerk bandbreedte op de server. Zie [bandbreedte beperking aan de client zijde](cache-troubleshoot-client.md#client-side-bandwidth-limitation)om te controleren of uw time-out wordt veroorzaakt door client netwerk bandbreedte.
1. Krijgt u een CPU gebonden op de server of op de client?

   - Controleer of u afhankelijk bent van CPU op de client. Hoge CPU kan ertoe leiden dat de aanvraag niet binnen het `synctimeout` interval wordt verwerkt en een time-out voor de aanvraag veroorzaken. U kunt dit probleem beheren door over te stappen op een grotere client grootte of door de belasting te distribueren.
   - Controleer of u een CPU-binding op de server krijgt door de metrische gegevens voor de CPU- [cache prestaties](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)te bewaken. Aanvragen die binnenkomen in terwijl redis is gebonden aan een CPU, kunnen een time-out veroorzaken voor deze aanvragen. Om dit probleem op te lossen, kunt u de belasting verdelen over meerdere Shards in een Premium-cache of een upgrade uitvoeren naar een grotere grootte of prijs categorie. Zie bandbreedte beperking aan de [server zijde](cache-troubleshoot-server.md#server-side-bandwidth-limitation)voor meer informatie.
1. Nemen de opdrachten veel tijd in beslag voor verwerking op de server? Langlopende opdrachten die veel tijd in beslag nemen voor de verwerking van de redis-server, kunnen time-outs veroorzaken. Zie [langlopende opdrachten](cache-troubleshoot-server.md#long-running-commands)voor meer informatie over langlopende opdrachten. U kunt verbinding maken met uw Azure-cache voor redis-exemplaar met behulp van de redis-cli-client of de [redis-console](cache-configure.md#redis-console). Voer vervolgens de opdracht [SLOWLOG](https://redis.io/commands/slowlog) uit om te zien of er aanvragen langzamer zijn dan verwacht. Redis server en stack Exchange. redis zijn geoptimaliseerd voor veel kleine aanvragen in plaats van minder grote aanvragen. Het splitsen van uw gegevens in kleinere segmenten kan hier meer dingen mee doen.

    Voor informatie over het maken van verbinding met het SSL-eind punt van uw cache met behulp van redis-CLI en stunnel, raadpleegt u het blog bericht met de [aankondiging van de ASP.net-sessie status provider voor redis preview-versie](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).
1. Hoge redis-server belasting kan time-outs veroorzaken. U kunt de belasting van de server bewaken door de gegevens voor de `Redis Server Load` [cache prestaties](cache-how-to-monitor.md#available-metrics-and-reporting-intervals)te bewaken. Een server belasting van 100 (maximum waarde) geeft aan dat de redis-server bezet is, zonder niet-actieve tijd, aanvragen verwerkt. Voer de opdracht SlowLog uit, zoals beschreven in de vorige alinea om te controleren of bepaalde aanvragen alle server mogelijkheden innemen. Zie High CPU Usage/server load (Engelstalig) voor meer informatie.
1. Was er een andere gebeurtenis aan de client zijde die mogelijk een netwerk Blip heeft veroorzaakt? Algemene gebeurtenissen zijn onder andere: het aantal client exemplaren omhoog of omlaag schalen, een nieuwe versie van de client implementeren of automatisch schalen ingeschakeld. In onze tests is geconstateerd dat automatisch schalen of omhoog/omlaag schalen kan ertoe leiden dat uitgaand netwerk connectiviteit enkele seconden verloren gaat. Stack Exchange. redis-code is robuust voor dergelijke gebeurtenissen en maakt opnieuw verbinding. Bij het opnieuw verbinden worden alle aanvragen in de wachtrij een time-out.
1. Was er een grote aanvraag voorafgaand aan een aantal kleine aanvragen aan de cache waarvoor een time-out is opgetreden? De para meter `qs` in het fout bericht vertelt u hoeveel aanvragen van de client naar de server zijn verzonden, maar er is nog geen antwoord verwerkt. Deze waarde kan toenemen omdat stack Exchange. redis een enkele TCP-verbinding gebruikt en slechts één antwoord tegelijk kan lezen. Zelfs als er een time-out optreedt voor de eerste bewerking, stopt het verzenden van meer gegevens naar of van de server. Andere aanvragen worden geblokkeerd tot de grote aanvraag is voltooid en kan time-outs veroorzaken. Eén oplossing is het minimaliseren van de kans op time-outs door ervoor te zorgen dat uw cache groot genoeg is voor uw werk belasting en het splitsen van grote waarden in kleinere segmenten. Een andere mogelijke oplossing is een groep `ConnectionMultiplexer` objecten in uw client te gebruiken en de minst geladen `ConnectionMultiplexer` te kiezen bij het verzenden van een nieuwe aanvraag. Het laden tussen meerdere verbindings objecten moet voor komen dat er een time-out optreedt voor andere aanvragen om ook een time-out op te lossen.
1. Als u `RedisSessionStateProvider`gebruikt, zorg er dan voor dat u de time-out voor opnieuw proberen correct hebt ingesteld. `retryTimeoutInMilliseconds` moet hoger zijn dan `operationTimeoutInMilliseconds`, anders worden er geen nieuwe pogingen gedaan. In het volgende voor beeld `retryTimeoutInMilliseconds` is ingesteld op 3000. Zie [ASP.net-sessie status provider voor Azure cache voor redis](cache-aspnet-session-state-provider.md) en [hoe u de configuratie parameters van de sessie status provider en de uitvoer cache provider gebruikt](https://github.com/Azure/aspnet-redis-providers/wiki/Configuration)voor meer informatie.

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

1. Controleer het geheugen gebruik op de Azure-cache voor de redis-server door `Used Memory RSS` en `Used Memory`te [controleren](cache-how-to-monitor.md#available-metrics-and-reporting-intervals) . Als er een verwijderings beleid aanwezig is, begint redis met het verwijderen van sleutels wanneer `Used_Memory` de cache grootte bereikt. In het ideale geval mag `Used Memory RSS` slechts iets hoger zijn dan `Used memory`. Een groot verschil betekent dat er geheugen fragmentatie (intern of extern) is. Als `Used Memory RSS` kleiner is dan `Used Memory`, betekent dit dat een deel van het cache geheugen is gewisseld door het besturings systeem. Als deze wisseling plaatsvindt, kunt u enkele belang rijke latentiesen verwachten. Omdat redis geen controle heeft over hoe de toewijzingen worden toegewezen aan geheugen pagina's, is een hoog `Used Memory RSS` vaak het resultaat van een piek in het geheugen gebruik. Wanneer redis server geheugen vrijmaakt, neemt de allocator het geheugen in beslag, maar kan het geheugen niet meer aan het systeem worden gekoppeld. Er is mogelijk een verschil tussen de `Used Memory` waarde en het geheugen verbruik, zoals gerapporteerd door het besturings systeem. Er is mogelijk geheugen gebruikt en dit is vrijgegeven door redis, maar het is niet teruggestuurd naar het systeem. U kunt de volgende stappen uitvoeren om geheugen problemen te beperken:

   - Voer een upgrade van de cache uit naar een grotere grootte, zodat u niet ten opzichte van geheugen beperkingen op het systeem.
   - Stel verval tijden in voor de sleutels zodat oudere waarden proactief worden verwijderd.
   - Controleer de metrische gegevens van de `used_memory_rss`-cache. Wanneer deze waarde de grootte van de cache nadert, is het waarschijnlijk dat u de prestatie problemen gaat bekijken. Distribueer de gegevens over meerdere Shards als u een Premium-cache gebruikt, of voer een upgrade uit naar een grotere cache grootte.

   Zie [Memory druk op de redis-server](cache-troubleshoot-server.md#memory-pressure-on-redis-server)voor meer informatie.

## <a name="additional-information"></a>Aanvullende informatie

- [Problemen met Azure Cache voor Redis aan clientzijde oplossen](cache-troubleshoot-client.md)
- [Problemen met Azure Cache voor Redis aan serverzijde oplossen](cache-troubleshoot-server.md)
- [Hoe kan ik Bench Mark en test de prestaties van mijn cache?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Azure-cache bewaken voor redis](cache-how-to-monitor.md)
