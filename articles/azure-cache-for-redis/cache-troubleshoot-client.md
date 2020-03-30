---
title: Problemen met Azure Cache voor Redis aan clientzijde oplossen
description: Meer informatie over het oplossen van veelvoorkomende client-side problemen met Azure Cache voor Redis, zoals Redis clientgeheugendruk, traffic burst, hoge CPU, beperkte bandbreedte, grote aanvragen of grote responsgrootte.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: troubleshooting
ms.date: 10/18/2019
ms.openlocfilehash: ace953fcb278604cb64eef463753f0f2622d3d24
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277945"
---
# <a name="troubleshoot-azure-cache-for-redis-client-side-issues"></a>Problemen met Azure Cache voor Redis aan clientzijde oplossen

In deze sectie worden probleemproblemen besproken die optreden vanwege een aandoening op de Redis-client die uw toepassing gebruikt.

- [Geheugendruk op Redis-client](#memory-pressure-on-redis-client)
- [Verkeersuitbarsting](#traffic-burst)
- [Hoog cpu-gebruik van client](#high-client-cpu-usage)
- [Bandbreedtebeperking aan de clientzijde](#client-side-bandwidth-limitation)
- [Grote aanvraag- of antwoordgrootte](#large-request-or-response-size)

## <a name="memory-pressure-on-redis-client"></a>Geheugendruk op Redis-client

Geheugendruk op de clientmachine leidt tot allerlei prestatieproblemen die de verwerking van reacties uit de cache kunnen vertragen. Wanneer de geheugendruk toeslaat, kan het systeem gegevens naar de schijf pagina's. Deze _pagina fout zorgt_ ervoor dat het systeem aanzienlijk vertragen.

Ga als het gaat om het detecteren van geheugendruk op de client:

- Controleer het geheugengebruik op de machine om ervoor te zorgen dat het beschikbare geheugen niet overschrijdt.
- Houd de prestatiemeter van `Page Faults/Sec` de klant in de gaten. Tijdens de normale werking hebben de meeste systemen enkele paginafouten. Pieken in paginafouten die overeenkomen met aanvraagtime-outs kunnen geheugendruk aangeven.

Hoge geheugendruk op de client kan op verschillende manieren worden beperkt:

- Graaf in uw geheugengebruikspatronen om het geheugenverbruik op de client te verminderen.
- Upgrade uw client-VM naar een groter formaat met meer geheugen.

## <a name="traffic-burst"></a>Verkeersuitbarsting

Uitbarstingen van verkeer `ThreadPool` in combinatie met slechte instellingen kunnen leiden tot vertragingen in de verwerking van gegevens die al door de Redis Server zijn verzonden, maar nog niet zijn verbruikt aan de clientzijde.

Controleer hoe `ThreadPool` uw statistieken in de loop van de tijd veranderen aan de hand van [een voorbeeld. `ThreadPoolLogger` ](https://github.com/JonCole/SampleCode/blob/master/ThreadPoolMonitor/ThreadPoolLogger.cs) U kunt `TimeoutException` berichten van StackExchange.Redis hieronder gebruiken om verder te onderzoeken:

    System.TimeoutException: Timeout performing EVAL, inst: 8, mgr: Inactive, queue: 0, qu: 0, qs: 0, qc: 0, wr: 0, wq: 0, in: 64221, ar: 0,
    IOCP: (Busy=6,Free=999,Min=2,Max=1000), WORKER: (Busy=7,Free=8184,Min=2,Max=8191)

In de voorgaande uitzondering zijn er verschillende interessante kwesties:

- Merk op `IOCP` dat u `WORKER` in de `Busy` sectie en de `Min` sectie een waarde hebt die groter is dan de waarde. Dit verschil `ThreadPool` betekent dat uw instellingen moeten worden aangepast.
- U `in: 64221`ook zien. Deze waarde geeft aan dat er 64.211 bytes zijn ontvangen op de kernelsocketlaag van de client, maar niet zijn gelezen door de toepassing. Dit verschil betekent meestal dat uw toepassing (bijvoorbeeld StackExchange.Redis) niet zo snel gegevens uit het netwerk leest als de server naar u verzendt.

U [uw `ThreadPool` instellingen zo configureren](cache-faq.md#important-details-about-threadpool-growth) dat uw threadpool snel wordt opschalen onder burst-scenario's.

## <a name="high-client-cpu-usage"></a>Hoog cpu-gebruik van client

Een hoog cpu-gebruik van de client geeft aan dat het systeem het werk dat het is gevraagd, niet kan bijhouden. Hoewel de cache het antwoord snel heeft verzonden, kan het zijn dat de client het antwoord niet tijdig verwerkt.

Monitor het systeembrede CPU-gebruik van de client met behulp van statistieken die beschikbaar zijn in de Azure-portal of via prestatiemeteritems op de machine. Wees voorzichtig niet te controleren *proces* CPU, omdat een enkel proces kan een laag CPU-gebruik hebben, maar het systeem-brede CPU kan hoog zijn. Kijk uit voor pieken in CPU-gebruik die overeenkomen met time-outs. Hoge CPU kan `in: XXX` ook `TimeoutException` leiden tot hoge waarden in foutmeldingen zoals beschreven in de [sectie Traffic burst.](#traffic-burst)

> [!NOTE]
> StackExchange.Redis 1.1.603 en `local-cpu` later `TimeoutException` bevat de statistiek in foutberichten. Zorg ervoor dat u de nieuwste versie van het [StackExchange.Redis NuGet-pakket gebruikt.](https://www.nuget.org/packages/StackExchange.Redis/) Er zijn bugs voortdurend wordt vastgesteld in de code om het robuuster te maken om time-outs, zodat het hebben van de nieuwste versie is belangrijk.
>

Ga als lid van het hoge CPU-gebruik van een client:

- Onderzoek wat cpu-pieken veroorzaakt.
- Upgrade uw client naar een groter VM-formaat met meer CPU-capaciteit.

## <a name="client-side-bandwidth-limitation"></a>Bandbreedtebeperking aan de clientzijde

Afhankelijk van de architectuur van clientmachines kunnen ze beperkingen hebben op hoeveel netwerkbandbreedte ze beschikbaar hebben. Als de client de beschikbare bandbreedte overschrijdt door de netwerkcapaciteit te overbelasten, worden gegevens niet zo snel aan de clientzijde verwerkt als de server deze verzendt. Deze situatie kan leiden tot time-outs.

Controleer hoe uw bandbreedtegebruik in de loop van de tijd verandert met behulp van [een voorbeeld. `BandwidthLogger` ](https://github.com/JonCole/SampleCode/blob/master/BandWidthMonitor/BandwidthLogger.cs) Deze code wordt mogelijk niet uitgevoerd in sommige omgevingen met beperkte machtigingen (zoals Azure-websites).

Als u het verbruik van netwerkbandbreedte wilt beperken of de grootte van de client-VM wilt vergroten tot één met meer netwerkcapaciteit.

## <a name="large-request-or-response-size"></a>Grote aanvraag- of antwoordgrootte

Een grote aanvraag/reactie kan time-outs veroorzaken. Stel bijvoorbeeld dat uw time-outwaarde die is geconfigureerd voor uw client 1 seconde is. Uw toepassing vraagt twee sleutels (bijvoorbeeld 'A' en 'B') tegelijkertijd (met dezelfde fysieke netwerkverbinding). De meeste klanten ondersteunen verzoek "pipelining", waarbij beide verzoeken 'A' en 'B' achter elkaar worden verzonden zonder te wachten op hun antwoorden. De server stuurt de antwoorden terug in dezelfde volgorde. Als de respons 'A' groot is, kan het de meeste time-outs voor latere aanvragen opeten.

In het volgende voorbeeld worden aanvragen 'A' en 'B' snel naar de server verzonden. De server begint snel reacties 'A' en 'B' te sturen. Vanwege de gegevensoverdrachttijden moet de reactie 'B' achter de reactie 'A' wachten, hoewel de server snel reageerde.

    |-------- 1 Second Timeout (A)----------|
    |-Request A-|
         |-------- 1 Second Timeout (B) ----------|
         |-Request B-|
                |- Read Response A --------|
                                           |- Read Response B-| (**TIMEOUT**)

Deze vraag/reactie is moeilijk te meten. U uw clientcode gebruiken om grote verzoeken en reacties bij te houden.

Resoluties voor grote responsgroottes zijn gevarieerd, maar omvatten:

1. Optimaliseer uw toepassing voor een groot aantal kleine waarden, in plaats van een paar grote waarden.
    - De voorkeursoplossing is om uw gegevens op te splitsen in gerelateerde kleinere waarden.
    - Zie de post [Wat is het ideale waardegroottebereik voor redis? Is 100 KB te groot?](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ) voor meer informatie over waarom kleinere waarden worden aanbevolen.
1. De grootte van uw vm vergroten om hogere bandbreedtemogelijkheden te krijgen
    - Meer bandbreedte op uw client of server-VM kan de gegevensoverdrachtstijd verkorten voor grotere reacties.
    - Vergelijk uw huidige netwerkgebruik op beide machines met de grenzen van uw huidige VM-grootte. Meer bandbreedte op alleen de server of alleen op de client is mogelijk niet voldoende.
1. Verhoog het aantal verbindingsobjecten dat uw toepassing gebruikt.
    - Gebruik een round-robin-benadering om aanvragen te doen voor verschillende verbindingsobjecten.

## <a name="additional-information"></a>Aanvullende informatie

- [Problemen met Azure Cache voor Redis aan serverzijde oplossen](cache-troubleshoot-server.md)
- [Hoe kan ik de prestaties van mijn cache benchmarken en testen?](cache-faq.md#how-can-i-benchmark-and-test-the-performance-of-my-cache)
