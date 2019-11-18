---
title: Aanbevolen procedures voor Azure cache voor redis
description: Meer informatie over het gebruik van uw Azure-cache voor redis door deze aanbevolen procedures te volgen.
author: joncole
ms.service: cache
ms.topic: conceptual
ms.date: 06/21/2019
ms.author: joncole
ms.openlocfilehash: 136c29245c63b2f2feed79a10a09fb57a379736f
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122392"
---
# <a name="best-practices-for-azure-cache-for-redis"></a>Aanbevolen procedures voor Azure cache voor redis 
Door deze aanbevolen procedures te volgen, kunt u de prestaties en het rendabele gebruik van uw Azure-cache voor redis-instantie maximaliseren.

## <a name="configuration-and-concepts"></a>Configuratie en concepten
 * **Gebruik de standaard-of Premium-laag voor productie systemen.**  De laag basis is een systeem met één knoop punt zonder gegevens replicatie en geen SLA. Gebruik ook ten minste een C1-cache.  C0-caches zijn bedoeld voor eenvoudige ontwikkel-en test scenario's, omdat ze een gedeelde CPU-kern, weinig geheugen hebben en gevoelig zijn voor problemen met ' ruis in de buur '.

 * **Houd er rekening mee dat redis een in-Memory-gegevens archief is.**  [Dit artikel](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) bevat een overzicht van een aantal scenario's waarin gegevens verlies kan optreden.

 * **Ontwikkel uw systeem zodanig dat het verbindings problemen kan verwerken** [vanwege patches en failover](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

 * **Configureer uw [maxmemory-gereserveerde instelling](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) om de reactie snelheid van het systeem te verbeteren** onder geheugen druk omstandigheden.  Deze instelling is vooral belang rijk voor schrijf zware workloads of als u grotere waarden opslaat (100 KB of meer) in redis. Het is raadzaam om te beginnen met 10% van de grootte van uw cache en vervolgens het percentage te verhogen als u schrijf-zware belasting hebt.

 * **Redis werkt het beste met kleinere waarden**. u kunt dus grotere gegevens in meerdere sleutels afzetten.  In [deze redis-discussie](https://stackoverflow.com/questions/55517224/what-is-the-ideal-value-size-range-for-redis-is-100kb-too-large/)worden enkele overwegingen vermeld die u zorgvuldig moet overwegen.  Lees [dit artikel](cache-troubleshoot-client.md#large-request-or-response-size) voor een voor beeld van een probleem dat kan worden veroorzaakt door grote waarden.

 * **Zoek uw cache-exemplaar en uw toepassing in dezelfde regio.**  Verbinding maken met een cache in een andere regio kan de latentie aanzienlijk verhogen en de betrouw baarheid verminderen.  Terwijl u verbinding kunt maken vanuit Azure, is het niet aanbevolen om *redis als een cache te gebruiken*.  Als u redis als alleen een sleutel/waarde-archief gebruikt, is latentie mogelijk niet het belangrijkste probleem. 

 * **Verbindingen opnieuw gebruiken** : het maken van nieuwe verbindingen is duur en neemt een hogere latentie, waardoor verbindingen zo veel mogelijk worden hergebruikt. Als u ervoor kiest nieuwe verbindingen te maken, moet u ervoor zorgen dat u de oude verbindingen sluit voordat u ze uitgeeft (zelfs in beheerde geheugen talen zoals .NET of Java).

 * **Configureer uw client bibliotheek zodanig dat er een *time-out* voor de verbinding van ten minste 15 seconden wordt gebruikt**, waarbij de systeem tijd nodig is om verbinding te maken, zelfs onder hogere CPU-omstandigheden.  Een time-outwaarde voor een kleine verbinding garandeert niet dat de verbinding in dat tijds bestek tot stand is gebracht.  Als er iets mis gaat (hoge CPU van client, een hoge CPU van een server, enzovoort), wordt de verbindings poging mislukt als er een korte time-outwaarde voor de verbinding is. Dit gedrag zorgt vaak voor een slechtere situatie.  In plaats van hulp te bieden, verergert u het probleem door het systeem te dwingen het proces van opnieuw proberen opnieuw verbinding te maken, wat kan leiden tot een *verbindings > mislukken > probeer het opnieuw* . U wordt aangeraden de verbindingstime-out op 15 seconden of hoger te laten staan. Het is beter om ervoor te zorgen dat de verbindings poging na 15 of 20 seconden goed kan worden uitgevoerd dan alleen om het probleem snel op te doen. Een dergelijke retry-lus kan ertoe leiden dat uw onderbreking langer duurt dan wanneer u het systeem gewoon in eerste instantie neemt.  
     > [!NOTE]
     > Deze richt lijnen zijn specifiek voor de *verbindings poging* en zijn niet gerelateerd aan de tijd die u bereid bent te wachten op een *bewerking* zoals Get of set to complete.
 
 * **Vermijd dure bewerkingen** : sommige redis-bewerkingen, zoals de opdracht [sleutels](https://redis.io/commands/keys) , zijn *erg* duur en moeten worden vermeden.  Zie enkele overwegingen rond [langlopende opdrachten](cache-troubleshoot-server.md#long-running-commands) voor meer informatie

 * **TLS-versleuteling gebruiken** : voor Azure cache voor redis is standaard TLS-versleutelde communicatie vereist.  TLS-versies 1,0, 1,1 en 1,2 worden momenteel ondersteund.  TLS 1,0 en 1,1 bevinden zich echter op een pad naar de hele industrie, dus gebruik TLS 1,2 als dat mogelijk is.  Als uw client bibliotheek of-hulp programma geen TLS ondersteunt, kunnen niet-versleutelde verbindingen worden uitgevoerd [via de Azure Portal](cache-configure.md#access-ports) -of [beheer-api's](https://docs.microsoft.com/rest/api/redis/redis/update).  In dergelijke gevallen is het raadzaam om uw cache-en client toepassing in te zetten in een virtueel netwerk.  Voor meer informatie over welke poorten worden gebruikt voor 
 
## <a name="memory-management"></a>Geheugen beheer
Er zijn verschillende dingen die betrekking hebben op het gebruik van het geheugen in uw redis-server exemplaar dat u mogelijk wilt overwegen.  Hier volgen enkele:

 * **Kies een [verwijderings beleid](https://redis.io/topics/lru-cache) dat geschikt is voor uw toepassing.**  Het standaard beleid voor Azure redis is *vluchtig-LRU*. Dit betekent dat alleen sleutels waarvoor een TTL-waarde is ingesteld, in aanmerking komen voor verwijdering.  Als er geen sleutels zijn met een TTL-waarde, worden de sleutels niet door het systeem verwijderd.  Als u wilt dat het systeem een wille keurige sleutel mag verwijderen als deze onder geheugen druk wordt weer geven, kunt u het *AllKeys-LRU-* beleid overwegen.

 * **Stel een verloop waarde in voor uw sleutels.**  Hiermee worden sleutels proactief verwijderd in plaats van te wachten totdat er geheugen druk is.  Als het verwijderen wordt gestart vanwege geheugen belasting, kan dit een extra belasting voor uw server veroorzaken.  Zie de documentatie voor de opdrachten [Expires](https://redis.io/commands/expire) en [ExpireAt](https://redis.io/commands/expireat) voor meer informatie.
 
## <a name="client-library-specific-guidance"></a>Specifieke richt lijnen voor de client bibliotheek
 * [StackExchange.Redis (.NET)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-stackexchange-redis-md)
 * [Java-welke client moet ik gebruiken?](https://gist.github.com/warrenzhu25/1beb02a09b6afd41dff2c27c53918ce7#file-azure-redis-java-best-practices-md)
 * [Sla (Java)](https://gist.github.com/warrenzhu25/181ccac7fa70411f7eb72aff23aa8a6a#file-azure-redis-lettuce-best-practices-md)
 * [Jedis (Java)](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-java-jedis-md)
 * [Node.js](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-node-js-md)
 * [PHP](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-php-md)
 * [Asp.Net-sessie status provider](https://gist.github.com/JonCole/925630df72be1351b21440625ff2671f#file-redis-bestpractices-session-state-provider-md)


## <a name="when-is-it-safe-to-retry"></a>Wanneer is het veilig om het opnieuw te proberen?
Helaas is er geen eenvoudig antwoord.  Elke toepassing moet bepalen welke bewerkingen opnieuw kunnen worden uitgevoerd en wat niet mogelijk is.  Elke bewerking heeft verschillende vereisten en intersleutel-afhankelijkheden.  Hier volgen enkele dingen die u kunt overwegen:

 * U kunt fouten aan de client zijde verkrijgen, zelfs als redis de opdracht heeft uitgevoerd die u hebt gevraagd om uit te voeren.  Bijvoorbeeld:
     - Time-outs zijn een concept aan de client zijde.  Als de-server is bereikt, voert de server de opdracht uit, zelfs als de client wacht op te geven.  
     - Als er een fout optreedt op de socket verbinding, is het niet mogelijk om te weten of de bewerking daad werkelijk is uitgevoerd op de server.  De verbindings fout kan bijvoorbeeld optreden nadat de aanvraag is verwerkt door de server, maar voordat het antwoord door de client wordt ontvangen.
 *  Hoe reageert mijn toepassing als ik de bewerking per ongeluk twee keer uitgevoerd?  Wat moet ik doen als ik een geheel getal twee keer in plaats van slechts één keer Verhoog?  Schrijft mijn toepassing naar dezelfde sleutel vanaf meerdere locaties?  Wat gebeurt er als mijn pogings logica een waarde overschrijft die is ingesteld door een ander onderdeel van mijn app?

Als u wilt testen hoe uw code werkt onder fout voorwaarden, kunt u overwegen de [herstart-functie](cache-administration.md#reboot)te gebruiken. Zo kunt u zien hoe de verbindings problemen invloed heeft op uw toepassing.

## <a name="performance-testing"></a>Prestaties testen
 * **Begin met `redis-benchmark.exe`** voor mogelijke door Voer/latentie voordat u uw eigen prestatie tests schrijft.  Redis-Bench Mark-documentatie vindt u [hier](https://redis.io/topics/benchmarks).  Houd er rekening mee dat redis-Bench Mark geen ondersteuning biedt voor SSL, dus moet u [de niet-SSL-poort inschakelen via de portal](cache-configure.md#access-ports) voordat u de test uitvoert.  [Een Windows-compatibele versie van redis-benchmark. exe vindt u hier](https://github.com/MSOpenTech/redis/releases)
 * De client-VM die wordt gebruikt voor het testen moet zich **in dezelfde regio** bevinden als uw redis-cache-exemplaar.
 * **We raden u** aan om een DV2-VM-reeks te gebruiken voor uw client, omdat deze betere hardware heeft en de beste resultaten oplevert.
 * Zorg ervoor dat de client-VM die u gebruikt,*ten minste evenveel reken kracht en band breedte* heeft als de cache die wordt getest. 
 * **Schakel VRSS** in op de client computer als u zich in Windows bevindt.  [Zie hier voor meer informatie](https://technet.microsoft.com/library/dn383582(v=ws.11).aspx).  Voor beeld Power shell-script:
     >Power shell-ExecutionPolicy unrestricted Enable-NetAdapterRSS-name (Get-netadapter). Naam 
     
 * **Overweeg het gebruik van redis-exemplaren van een Premium-laag**.  Deze cache grootten hebben een betere netwerk latentie en door Voer omdat ze worden uitgevoerd op betere hardware voor zowel CPU als netwerk.
 
     > [!NOTE]
     > Onze waargenomen prestatie resultaten worden [hier](cache-faq.md#azure-cache-for-redis-performance) voor uw referentie gepubliceerd.   Houd er ook rekening mee dat SSL/TLS enige overhead toevoegt, waardoor u mogelijk verschillende vertragingen en/of door Voer kunt krijgen als u transport versleuteling gebruikt.
 
### <a name="redis-benchmark-examples"></a>Voor beelden van redis-benchmarks
**Installatie vooraf testen**: Hiermee wordt het cache-exemplaar voor bereid met de gegevens die zijn vereist voor de opdrachten voor de latentie en de door Voer van de tests die hieronder worden weer gegeven.
> redis-benchmark. exe-h yourcache.redis.cache.windows.net-a yourAccesskey-t SET-n 10-d 1024 

**Test latentie**: Hiermee worden aanvragen voor ophalen getest met een payload van 1 KB.
> redis-benchmark. exe-h yourcache.redis.cache.windows.net-a yourAccesskey-t GET-d 1024-P 50-c 4

De **door Voer testen:** Dit maakt gebruik van pijp lijn GET-aanvragen met een payload van 1 KB.
> redis-benchmark. exe-h yourcache.redis.cache.windows.net-a yourAccesskey-t GET-n 1000000-d 1024-P 50-c 50
