---
title: Problemen met gegevensverlies in Azure Cache voor Redis oplossen
description: Meer informatie over het oplossen van problemen met gegevens verlies met Azure cache voor redis, zoals gedeeltelijk verlies van sleutels, verval datum van sleutel of volledig verlies van sleutels.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: d54506b94f076f0a3d967f88bd4e2960a1ca6396
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75530898"
---
# <a name="troubleshoot-data-loss-in-azure-cache-for-redis"></a>Problemen met gegevensverlies in Azure Cache voor Redis oplossen

In dit artikel wordt beschreven hoe u de werkelijke of waargenomen gegevens verliezen die zich kunnen voordoen in azure cache voor redis kunt vaststellen.

> [!NOTE]
> Enkele van de stappen voor probleem oplossing in deze hand leiding bevatten instructies voor het uitvoeren van redis-opdrachten en het controleren van verschillende prestatie gegevens. Zie de artikelen in de sectie met [aanvullende informatie](#additional-information) voor meer informatie en instructies.
>

## <a name="partial-loss-of-keys"></a>Gedeeltelijk verlies van sleutels

Azure cache voor redis verwijdert geen wille keurige sleutels nadat ze zijn opgeslagen in het geheugen. Er worden echter wel sleutels verwijderd als reactie op verloop-of verwijderings beleid en op expliciete opdrachten voor het verwijderen van sleutels. Sleutels die zijn geschreven naar het hoofd knooppunt in een Premium-of Standard Azure-cache voor redis-exemplaar zijn ook mogelijk niet direct beschikbaar voor een replica. Gegevens worden op asynchrone en niet-blokkerende wijze gerepliceerd van de Master naar de replica.

Als u ontdekt dat sleutels zijn verdwenen uit uw cache, controleert u de volgende mogelijke oorzaken:

| Oorzaak | Beschrijving |
|---|---|
| [Verval datum van de sleutel](#key-expiration) | Sleutels worden verwijderd omdat er time-outs zijn ingesteld. |
| [Sleutel verwijdering](#key-eviction) | Sleutels worden verwijderd onder geheugen belasting. |
| [Sleutel verwijderen](#key-deletion) | Sleutels worden verwijderd door expliciete Delete-opdrachten. |
| [Asynchrone replicatie](#async-replication) | Sleutels zijn niet beschikbaar op een replica vanwege vertragingen in de gegevens replicatie. |

### <a name="key-expiration"></a>Verval datum van de sleutel

Azure cache voor redis verwijdert automatisch een sleutel als aan de sleutel een time-out wordt toegewezen en die periode is verstreken. Zie de documentatie van de [verlopende](https://redis.io/commands/expire) opdracht voor meer informatie over de verval datum van de redis-sleutel. Time-outwaarden kunnen ook worden ingesteld met behulp van de [set](https://redis.io/commands/set)-, [SETEX](https://redis.io/commands/setex)-, [GETSET](https://redis.io/commands/getset)-en andere ** \*Store** -opdrachten.

Gebruik de opdracht [info](https://redis.io/commands/info) om statistieken weer te geven over hoeveel sleutels zijn verlopen. In `Stats` de sectie wordt het totale aantal verlopen sleutels weer gegeven. De `Keyspace` sectie bevat meer informatie over het aantal sleutels met time-outs en de gemiddelde time-outwaarde.

```
# Stats

expired_keys:46583

# Keyspace

db0:keys=3450,expires=2,avg_ttl=91861015336
```

U kunt ook diagnostische gegevens voor uw cache bekijken om te zien of er een correlatie is tussen wanneer de sleutel ontbreekt en een piek in verlopen sleutels. Zie de bijlage over het [opsporen van fouten in redis](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) voor meer informatie over het gebruik van de opdracht regel meldingen of **monitor** om dit soort problemen op te lossen.

### <a name="key-eviction"></a>Sleutel verwijdering

Azure cache voor redis vereist geheugen ruimte voor het opslaan van gegevens. De sleutels worden verwijderd om zo nodig het geheugen beschikbaar te maken. Wanneer de **used_memory** -of **used_memory_rss** -waarden in de [info](https://redis.io/commands/info) opdracht de geconfigureerde **Maxmemory** -instelling benaderen, begint Azure cache voor redis het verwijderen van sleutels uit het geheugen op basis van het [cache beleid](https://redis.io/topics/lru-cache).

U kunt het aantal verwijderde sleutels bewaken met behulp van de opdracht [info](https://redis.io/commands/info) :

```
# Stats

evicted_keys:13224
```

U kunt ook diagnostische gegevens voor uw cache bekijken om te zien of er een correlatie is tussen wanneer de sleutel ontbreekt en een piek in Verwijderde sleutels. Zie de bijlage over het [opsporen van fouten in redis](https://gist.github.com/JonCole/4a249477142be839b904f7426ccccf82#appendix) voor meer informatie over het gebruik van de opdracht regel meldingen of **monitor** om dit soort problemen op te lossen.

### <a name="key-deletion"></a>Sleutel verwijderen

Redis-clients kunnen de opdracht [del](https://redis.io/commands/del) of [HDEL](https://redis.io/commands/hdel) uitgeven om sleutels expliciet te verwijderen uit Azure cache voor redis. U kunt het aantal verwijderings bewerkingen bijhouden met behulp van de opdracht [info](https://redis.io/commands/info) . Als **del** -of **HDEL** -opdrachten zijn aangeroepen, worden deze weer gegeven in `Commandstats` de sectie.

```
# Commandstats

cmdstat_del:calls=2,usec=90,usec_per_call=45.00

cmdstat_hdel:calls=1,usec=47,usec_per_call=47.00
```

### <a name="async-replication"></a>Asynchrone replicatie

Alle Azure-caches voor redis-instanties in de laag Standard of Premium worden geconfigureerd met een hoofd knooppunt en ten minste één replica. Gegevens worden asynchroon van het model naar een replica gekopieerd met behulp van een achtergrond proces. De [redis.io](https://redis.io/topics/replication) -website beschrijft hoe redis-gegevens replicatie in het algemeen werkt. Voor scenario's waarbij clients regel matig schrijven naar redis, kan gedeeltelijk gegevens verlies optreden omdat deze replicatie gegarandeerd onmiddellijk is. Als de hoofd server bijvoorbeeld wordt uitgeschakeld *nadat* een client een sleutel naar de replica schrijft, maar *voordat* het achtergrond proces die sleutel naar de replica's kan verzenden, gaat de sleutel verloren wanneer de replica de nieuwe hoofd server overneemt.

## <a name="major-or-complete-loss-of-keys"></a>Groot of volledig verlies van sleutels

Als de meeste of alle sleutels uit uw cache zijn verdwenen, controleert u de volgende mogelijke oorzaken:

| Oorzaak | Beschrijving |
|---|---|
| [Leegmaken van sleutel](#key-flushing) | Sleutels zijn hand matig verwijderd. |
| [Onjuiste database selectie](#incorrect-database-selection) | Azure cache voor redis is ingesteld op het gebruik van een niet-standaard database. |
| [Redis-instantie fout](#redis-instance-failure) | De redis-server is niet beschikbaar. |

### <a name="key-flushing"></a>Leegmaken van sleutel

Clients kunnen de opdracht [FLUSHDB](https://redis.io/commands/flushdb) aanroepen om alle sleutels in *één* data base of [FLUSHALL](https://redis.io/commands/flushall) te verwijderen om alle sleutels te verwijderen uit *alle* data bases in een redis-cache. Als u wilt weten of sleutels zijn leeg gemaakt, gebruikt u de opdracht [info](https://redis.io/commands/info) . De `Commandstats` sectie laat zien of de opdracht **Flush** is aangeroepen:

```
# Commandstats

cmdstat_flushall:calls=2,usec=112,usec_per_call=56.00

cmdstat_flushdb:calls=1,usec=110,usec_per_call=52.00
```

### <a name="incorrect-database-selection"></a>Onjuiste database selectie

Azure cache voor redis maakt standaard gebruik van de **db0** -data base. Als u overschakelt naar een andere data base (bijvoorbeeld **db1**) en probeert sleutels te lezen, worden deze daar niet gevonden door Azure cache voor redis. Elke Data Base is een logische afzonderlijke eenheid en bevat een andere gegevensset. Gebruik de [Select](https://redis.io/commands/select) -opdracht om andere beschik bare data bases te gebruiken en zoek naar sleutels in elk van deze.

### <a name="redis-instance-failure"></a>Redis-instantie fout

Redis is een gegevens archief in het geheugen. Gegevens worden bewaard op de fysieke of virtuele machines die de redis-cache hosten. Een Azure-cache voor het redis-exemplaar in de laag Basic kan slechts op één virtuele machine (VM) worden uitgevoerd. Als deze virtuele machine niet beschikbaar is, gaan alle gegevens die u in de cache hebt opgeslagen verloren. 

Caches in de Standard-en Premium-lagen bieden veel meer tolerantie tegen gegevens verlies met behulp van twee virtuele machines in een gerepliceerde configuratie. Wanneer het hoofd knooppunt in een dergelijke cache mislukt, neemt het replica knooppunt de gegevens automatisch over. Deze Vm's bevinden zich in verschillende domeinen voor fouten en updates, om de kans te verkleinen dat ze niet tegelijkertijd niet meer beschikbaar zijn. Als er sprake is van een ernstige storing in het Data Center, kunnen de Vm's echter nog steeds samen gaan. De gegevens gaan in deze zeldzame gevallen verloren.

Overweeg het gebruik van [redis-gegevens persistentie](https://redis.io/topics/persistence) en [geo-replicatie](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-geo-replication) om de beveiliging van uw gegevens tegen deze infrastructuur fouten te verbeteren.

## <a name="additional-information"></a>Aanvullende informatie

- [Problemen met Azure Cache voor Redis aan serverzijde oplossen](cache-troubleshoot-server.md)
- [Wat is Azure cache voor redis aanbieding en grootte moet ik gebruiken?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Azure-cache bewaken voor redis](cache-how-to-monitor.md)
- [Hoe kan ik redis-opdrachten uitvoeren?](cache-faq.md#how-can-i-run-redis-commands)
