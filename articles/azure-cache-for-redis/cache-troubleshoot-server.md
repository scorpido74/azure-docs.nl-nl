---
title: Problemen met Azure Cache voor Redis aan serverzijde oplossen
description: Meer informatie over het oplossen van veelvoorkomende serverproblemen met Azure Cache voor Redis, zoals geheugendruk, hoge CPU, langlopende opdrachten of bandbreedtebeperkingen.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 10/18/2019
ms.openlocfilehash: a68c27de304a0da6470745ee4abf69590d9bf78c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277932"
---
# <a name="troubleshoot-azure-cache-for-redis-server-side-issues"></a>Problemen met Azure Cache voor Redis aan serverzijde oplossen

In deze sectie worden probleemproblemen besproken die optreden vanwege een aandoening op een Azure-cache voor Redis of de virtuele machine(s) die deze host.

- [Geheugendruk op Redis-server](#memory-pressure-on-redis-server)
- [Hoog CPU-gebruik of serverbelasting](#high-cpu-usage-or-server-load)
- [Langlopende opdrachten](#long-running-commands)
- [Bandbreedtebeperking aan serverzijde](#server-side-bandwidth-limitation)

> [!NOTE]
> Verschillende van de stappen voor het oplossen van problemen in deze handleiding bevatten instructies voor het uitvoeren van Redis-opdrachten en het bewaken van verschillende prestatiestatistieken. Zie de artikelen in de sectie [Aanvullende informatie voor](#additional-information) meer informatie en instructies.
>

## <a name="memory-pressure-on-redis-server"></a>Geheugendruk op Redis-server

Geheugendruk aan de serverzijde leidt tot allerlei prestatieproblemen die de verwerking van aanvragen kunnen vertragen. Wanneer de geheugendruk toeslaat, kan het systeem gegevens naar de schijf pagina's. Deze _pagina fout zorgt_ ervoor dat het systeem aanzienlijk vertragen. Er zijn verschillende mogelijke oorzaken van deze geheugendruk:

- De cache is gevuld met gegevens in de buurt van de maximale capaciteit.
- Redis ziet een hoge geheugenfragmentatie. Deze fragmentatie wordt meestal veroorzaakt door het opslaan van grote objecten, omdat Redis is geoptimaliseerd voor kleine objecten.

Redis onthult twee statistieken via de [info-opdracht](https://redis.io/commands/info) die u kunnen helpen dit probleem te identificeren: "used_memory" en "used_memory_rss". U [deze statistieken bekijken](cache-how-to-monitor.md#view-metrics-with-azure-monitor) via de portal.

Er zijn verschillende mogelijke wijzigingen die u aanbrengen om het geheugengebruik gezond te houden:

- [Configureer een geheugenbeleid](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) en stel verlooptijden in op uw sleutels. Dit beleid is mogelijk niet voldoende als u fragmentatie hebt.
- [Configureer een maximaal gereserveerde waarde](cache-configure.md#maxmemory-policy-and-maxmemory-reserved) die groot genoeg is om geheugenfragmentatie te compenseren.
- Breek uw grote objecten in de cache op in kleinere gerelateerde objecten.
- [Maak waarschuwingen](cache-how-to-monitor.md#alerts) over statistieken zoals gebruikt geheugen om vroegtijdig op de hoogte te worden gesteld van mogelijke effecten.
- [Schaal](cache-how-to-scale.md) naar een grotere cachegrootte met meer geheugencapaciteit.

## <a name="high-cpu-usage-or-server-load"></a>Hoog CPU-gebruik of serverbelasting

Een hoge serverbelasting of CPU-gebruik betekent dat de server aanvragen niet tijdig kan verwerken. De server kan traag reageren en kan de aanvraagtarieven niet bijhouden.

[Monitor statistieken](cache-how-to-monitor.md#view-metrics-with-azure-monitor) zoals CPU of serverbelasting. Kijk uit voor pieken in CPU-gebruik die overeenkomen met time-outs.

Er zijn verschillende wijzigingen die u aanbrengen om de hoge serverbelasting te beperken:

- Onderzoek wat [cpu-pieken](#long-running-commands) veroorzaakt, zoals langlopende opdrachten die hieronder worden vermeld of paginafouten vanwege de hoge geheugendruk.
- [Maak waarschuwingen](cache-how-to-monitor.md#alerts) over statistieken zoals CPU of serverbelasting om vroegtijdig op de hoogte te worden gesteld van mogelijke effecten.
- [Schaal](cache-how-to-scale.md) naar een grotere cachegrootte met meer CPU-capaciteit.

## <a name="long-running-commands"></a>Langlopende opdrachten

Sommige Redis-opdrachten zijn duurder om uit te voeren dan andere. De [documentatie van de Opdrachten Redis](https://redis.io/commands) toont de tijdscomplexiteit van elke opdracht. Omdat de opdrachtverwerking van Redis single-threaded is, blokkeert een opdracht die tijd kost om uit te voeren alle anderen die erachter komen. U moet de opdrachten controleren die u aan uw Redis-server uitgeeft om inzicht te krijgen in de effecten van de prestaties. De opdracht [TOETSEN](https://redis.io/commands/keys) wordt bijvoorbeeld vaak gebruikt zonder te weten dat het een O(N)-bewerking is. U toetsen vermijden door [SCAN](https://redis.io/commands/scan) te gebruiken om CPU-pieken te verminderen.

Met de opdracht [SLOWLOG](https://redis.io/commands/slowlog) u dure opdrachten meten die tegen de server worden uitgevoerd.

## <a name="server-side-bandwidth-limitation"></a>Bandbreedtebeperking aan serverzijde

Verschillende cacheformaten hebben verschillende netwerkbandbreedtecapaciteiten. Als de server de beschikbare bandbreedte overschrijdt, worden gegevens niet zo snel naar de client verzonden. Clientaanvragen kunnen een time-out krijgen omdat de server gegevens niet snel genoeg naar de client kan pushen.

De statistieken 'Cache read' en 'Cache Write' kunnen worden gebruikt om te zien hoeveel bandbreedte aan de serverzijde wordt gebruikt. U [deze statistieken bekijken](cache-how-to-monitor.md#view-metrics-with-azure-monitor) in de portal.

Ga als u op zoek naar situaties waarin het gebruik van netwerkbandbreedte dicht bij de maximale capaciteit ligt:

- Het gespreksgedrag van de client wijzigen om de netwerkvraag te verminderen.
- [Maak waarschuwingen](cache-how-to-monitor.md#alerts) over statistieken zoals cachelezen of cacheschrijven om vroeg op de hoogte te worden gesteld van mogelijke effecten.
- [Schaal](cache-how-to-scale.md) naar een grotere cachegrootte met meer netwerkbandbreedtecapaciteit.

## <a name="additional-information"></a>Aanvullende informatie

- [Problemen met Azure Cache voor Redis aan clientzijde oplossen](cache-troubleshoot-client.md)
- [Welke Azure-cache voor Redis-aanbieding en grootte moet ik gebruiken?](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)
- [Hoe kan ik de prestaties van mijn cache benchmarken en testen?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
- [Azure-cache controleren op Redis](cache-how-to-monitor.md)
- [Hoe kan ik Redis-opdrachten uitvoeren?](cache-faq.md#how-can-i-run-redis-commands)
