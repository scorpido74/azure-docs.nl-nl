---
title: Problemen met gegevensverlies in Azure Cache voor Redis oplossen
description: Meer informatie over het oplossen van problemen met gegevensverlies met Azure Cache voor Redis, zoals gedeeltelijk verlies van sleutels, het verlopen van sleutels of het volledig verlies van sleutels.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: d54506b94f076f0a3d967f88bd4e2960a1ca6396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75530898"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Problemen met gegevensverlies in Azure Cache voor Redis oplossen

In dit artikel wordt besproken hoe u werkelijke of waargenomen gegevensverliezen diagnosticeren die zich kunnen voordoen in Azure Cache voor Redis.

> [!NOTE]
> Verschillende van de stappen voor het oplossen van problemen in deze handleiding bevatten instructies voor het uitvoeren van Redis-opdrachten en het bewaken van verschillende prestatiestatistieken. Zie de artikelen in de sectie [Aanvullende informatie voor](#additional-information) meer informatie en instructies.
>

## <a name="partial-loss-of-keys"></a>Gedeeltelijk verlies van sleutels

Azure Cache voor Redis verwijdert niet willekeurig sleutels nadat ze in het geheugen zijn opgeslagen. Het verwijdert echter sleutels in reactie op het vervaldatum- of uitzettingsbeleid en op expliciete opdrachten voor het verwijderen van sleutels. Sleutels die naar het hoofdknooppunt zijn geschreven in een Premium- of Standard Azure-cache voor bijvoorbeeld Redis, zijn mogelijk ook niet meteen beschikbaar op een replica. Gegevens worden op een asynchrone en niet-blokkerende manier gerepliceerd van het model naar de replica.

Als u merkt dat sleutels uit uw cache zijn verdwenen, controleert u de volgende mogelijke oorzaken:

| Oorzaak | Beschrijving |
|---|---|
| [Sleutel verloop](#key-expiration) | Sleutels worden verwijderd vanwege time-outs die erop zijn ingesteld. |
| [Sleuteluitzetting](#key-eviction) | Toetsen worden verwijderd onder geheugendruk. |
| [Sleutelverwijdering](#key-deletion) | Toetsen worden verwijderd door expliciete verwijderingsopdrachten. |
| [Async-replicatie](#async-replication) | Sleutels zijn niet beschikbaar op een replica vanwege vertragingen bij gegevensreplicatie. |

### <a name="key-expiration"></a>Sleutel verloop

Azure Cache voor Redis verwijdert automatisch een sleutel als aan de sleutel een time-out is toegewezen en die periode is verstreken. Zie de [opdrachtdocumentatie VERLOPEN](https://redis.io/commands/expire) voor meer informatie over de vervaldatum van de Redis-toets. Time-outwaarden kunnen ook worden ingesteld met behulp van de opdrachten [SET,](https://redis.io/commands/set) [SETEX,](https://redis.io/commands/setex) [GETSET](https://redis.io/commands/getset)en andere ** \*STORE-opdrachten.**

Als u statistieken wilt krijgen over het aantal sleutels dat is verlopen, gebruikt u de opdracht [INFO.](https://redis.io/commands/info) De `Stats` sectie toont het totale aantal verlopen sleutels. De `Keyspace` sectie geeft meer informatie over het aantal sleutels met time-outs en de gemiddelde time-outwaarde.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

U ook diagnostische statistieken voor uw cache bekijken om te zien of er een correlatie is tussen het moment waarop de sleutel is verdwenen en een piek in verlopen sleutels. Zie de bijlage van Het opsporen van [Redis Keyspace-missers](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) voor informatie over het gebruik van keyspace-meldingen of **MONITOR** om dit soort problemen te debuggen.

### <a name="key-eviction"></a>Sleuteluitzetting

Azure Cache voor Redis vereist geheugenruimte om gegevens op te slaan. Het zuivert sleutels om beschikbaar geheugen vrij te maken wanneer dat nodig is. Wanneer de **used_memory** of **used_memory_rss** waarden in de [opdracht INFO](https://redis.io/commands/info) de instelling voor **geconfigureerde maxmemory** benaderen, start Azure Cache voor Redis met het verwijderen van sleutels uit het geheugen op basis van [cachebeleid.](https://redis.io/topics/lru-cache)

U het aantal uitgezette sleutels controleren met de opdracht [INFO:](https://redis.io/commands/info)

```
# Stats

evicted_keys:13224
```

U ook diagnostische statistieken voor uw cache bekijken om te zien of er een correlatie is tussen het moment waarop de sleutel is verdwenen en een piek in uitgezette sleutels. Zie de bijlage van Het opsporen van [Redis Keyspace-missers](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) voor informatie over het gebruik van keyspace-meldingen of **MONITOR** om dit soort problemen te debuggen.

### <a name="key-deletion"></a>Sleutelverwijdering

Redis-clients kunnen de opdracht [DEL](https://redis.io/commands/del) of [HDEL](https://redis.io/commands/hdel) uitgeven om sleutels expliciet uit Azure Cache voor Redis te verwijderen. U het aantal verwijderingsbewerkingen bijhouden met de opdracht [INFO.](https://redis.io/commands/info) Als **DEL-** of **HDEL-commando's** zijn aangeroepen, `Commandstats` worden ze weergegeven in de sectie.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Async-replicatie

Elke Azure-cache voor Redis-instantie in de standaard- of Premium-laag is geconfigureerd met een hoofdknooppunt en ten minste één replica. Gegevens worden asynchroon gekopieerd van het stramien naar een replica met behulp van een achtergrondproces. De [website van redis.io](https://redis.io/topics/replication) beschrijft hoe Redis datareplicatie in het algemeen werkt. Voor scenario's waarin clients regelmatig naar Redis schrijven, kan gedeeltelijke gegevensverlies optreden omdat deze replicatie gegarandeerd onmiddellijk zal zijn. Als de master bijvoorbeeld naar beneden gaat *nadat* een client er een sleutel naar heeft geschreven, maar *voordat* het achtergrondproces de kans heeft om die sleutel naar de replica te verzenden, gaat de sleutel verloren wanneer de replica het overneemt als de nieuwe master.

## <a name="major-or-complete-loss-of-keys"></a>Groot of volledig verlies van sleutels

Als de meeste of alle toetsen uit uw cache zijn verdwenen, controleert u de volgende mogelijke oorzaken:

| Oorzaak | Beschrijving |
|---|---|
| [Sleutelspoelen](#key-flushing) | Sleutels zijn handmatig verwijderd. |
| [Onjuiste databaseselectie](#incorrect-database-selection) | Azure Cache voor Redis is ingesteld op een niet-standaard database te gebruiken. |
| [Redis-instantiemislukt](#redis-instance-failure) | De Redis-server is niet beschikbaar. |

### <a name="key-flushing"></a>Sleutelspoelen

Clients kunnen de opdracht [FLUSHDB](https://redis.io/commands/flushdb) aanroepen om alle sleutels in *één* database of [FLUSHALL](https://redis.io/commands/flushall) te verwijderen om alle sleutels uit *alle* databases in een Redis-cache te verwijderen. Als u wilt weten of de toetsen zijn doorgespoeld, gebruikt u de opdracht [INFO.](https://redis.io/commands/info) In `Commandstats` de sectie wordt weergegeven of de opdracht **FLUSH** is aangeroepen:

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Onjuiste databaseselectie

Azure Cache voor Redis maakt standaard gebruik van de **db0-database.** Als u overschakelt naar een andere database (bijvoorbeeld **db1)** en probeert er toetsen uit te lezen, vindt Azure Cache voor Redis deze daar niet. Elke database is een logisch aparte eenheid en heeft een andere dataset. Gebruik de opdracht [SELECT](https://redis.io/commands/select) om andere beschikbare databases te gebruiken en naar sleutels in elk van deze databases te zoeken.

### <a name="redis-instance-failure"></a>Redis-instantiemislukt

Redis is een in-memory data store. Gegevens worden bewaard op de fysieke of virtuele machines die de Redis-cache hosten. Een Azure-cache voor Redis-instantie in de basislaag wordt uitgevoerd op slechts één virtuele machine (VM). Als die vm is uitgeschakeld, gaan alle gegevens die u in de cache hebt opgeslagen, verloren. 

Caches in de standaard- en premiumlagen bieden een veel hogere tolerantie tegen gegevensverlies door twee VM's in een gerepliceerde configuratie te gebruiken. Wanneer het hoofdknooppunt in een dergelijke cache mislukt, wordt het replicaknooppunt overgenomen om gegevens automatisch weer te geven. Deze VM's bevinden zich op afzonderlijke domeinen voor fouten en updates, om de kans te minimaliseren dat beide tegelijkertijd niet meer beschikbaar zijn. Als er echter een grote datacenterstoring optreedt, kunnen de VM's nog steeds samen naar beneden gaan. Uw gegevens gaan in deze zeldzame gevallen verloren.

Overweeg [de persistentie van Redis-gegevens](https://redis.io/topics/persistence) en [georeplicatie](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) te gebruiken om de bescherming van uw gegevens tegen deze infrastructuurfouten te verbeteren.

## <a name="additional-information"></a>Aanvullende informatie

- [Problemen met Azure Cache voor Redis aan serverzijde oplossen](cache-troubleshoot-server.md)
- [Welke Azure-cache voor Redis-aanbieding en grootte moet ik gebruiken?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Azure-cache controleren op Redis](cache-how-to-monitor.md)
- [Hoe kan ik Redis-opdrachten uitvoeren?](cache-faq.md#how-can-i-run-redis-commands)
