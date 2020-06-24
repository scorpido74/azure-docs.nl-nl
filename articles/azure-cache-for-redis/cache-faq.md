---
title: Veelgestelde vragen over Azure Cache voor Redis
description: Meer informatie over de antwoorden op veelgestelde vragen, patronen en aanbevolen procedures voor Azure cache voor redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: fc19f247a9a915bfafde68f652784c42285928ca
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84974922"
---
# <a name="azure-cache-for-redis-faq"></a>Veelgestelde vragen over Azure Cache voor Redis
Meer informatie over de antwoorden op veelgestelde vragen, patronen en aanbevolen procedures voor Azure cache voor redis.

## <a name="what-if-my-question-isnt-answered-here"></a>Wat gebeurt er als mijn vraag hier niet wordt beantwoord?
Als uw vraag hier niet wordt vermeld, laat het ons weten en wij helpen u een antwoord te vinden.

* U kunt een vraag stellen in de opmerkingen aan het einde van deze veelgestelde vragen en samen werken met het Azure cache-team en andere leden van de community over dit artikel.
* Als u een breder publiek wilt bereiken, kunt u een vraag stellen op de [pagina micro soft Q&een vraag voor Azure cache](https://docs.microsoft.com/answers/topics/azure-cache-redis.html) en het Azure cache-team en andere leden van de community te benaderen.
* Als u een functie aanvraag wilt maken, kunt u uw aanvragen en ideeën voor [redis gebruikers Voice verzenden naar Azure cache](https://feedback.azure.com/forums/169382-cache).
* U kunt ook een e-mail verzenden naar ons via de [externe feedback van Azure cache](mailto:azurecache@microsoft.com).

## <a name="azure-cache-for-redis-basics"></a>Azure cache voor redis-basis beginselen
De veelgestelde vragen in deze sectie hebben betrekking op enkele van de basis beginselen van Azure cache voor redis.

* [Wat is Azure Cache voor Redis?](#what-is-azure-cache-for-redis)
* [Hoe kan ik aan de slag met Azure cache voor redis?](#how-can-i-get-started-with-azure-cache-for-redis)

De volgende veelgestelde vragen hebben betrekking op basis concepten en vragen over Azure cache voor redis en worden beantwoord in een van de andere secties met veelgestelde vragen.

* [Wat is Azure cache voor redis aanbieding en grootte moet ik gebruiken?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Welke Azure-cache voor redis-clients kan ik gebruiken?](#what-azure-cache-for-redis-clients-can-i-use)
* [Is er een lokale emulator voor Azure cache voor redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Hoe kan ik de status en prestaties van mijn cache controleren?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Veelgestelde vragen over planning
* [Wat is Azure cache voor redis aanbieding en grootte moet ik gebruiken?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Azure cache voor redis-prestaties](#azure-cache-for-redis-performance)
* [In welke regio moet ik mijn cache vinden?](#in-what-region-should-i-locate-my-cache)
* [Hoe word ik gefactureerd voor Azure-cache voor redis?](#how-am-i-billed-for-azure-cache-for-redis)
* [Kan ik Azure cache gebruiken voor redis met Azure Government Cloud, Azure China Cloud of Microsoft Azure Duitsland?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Veelgestelde vragen over ontwikkel aars
* [Wat doen de configuratie opties voor stack Exchange. redis?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Welke Azure-cache voor redis-clients kan ik gebruiken?](#what-azure-cache-for-redis-clients-can-i-use)
* [Is er een lokale emulator voor Azure cache voor redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Hoe kan ik redis-opdrachten uitvoeren?](#how-can-i-run-redis-commands)
* [Waarom heeft Azure cache voor redis geen verwijzing naar een MSDN Class-bibliotheek, zoals een aantal andere Azure-Services?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Kan ik Azure cache gebruiken voor redis als een PHP-sessie cache?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Wat zijn redis-data bases?](#what-are-redis-databases)

## <a name="security-faqs"></a>Veelgestelde vragen over beveiliging
* [Wanneer moet ik de niet-TLS/SSL-poort inschakelen om verbinding te maken met redis?](#when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>Veelgestelde vragen over productie
* [Wat zijn de aanbevolen procedures voor productie?](#what-are-some-production-best-practices)
* [Wat zijn enkele aandachtspunten bij het gebruik van algemene redis-opdrachten?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Hoe kan ik Bench Mark en test de prestaties van mijn cache?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Belang rijke informatie over de groei van de thread pool](#important-details-about-threadpool-growth)
* [Schakel Server GC in om meer door voer te krijgen op de client wanneer stack Exchange. redis wordt gebruikt](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Prestatie overwegingen rond verbindingen](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Veelgestelde vragen over bewaking en probleem oplossing
In deze sectie vindt u veelgestelde vragen over veelvoorkomende controles en oplossingen voor probleem oplossing. Zie voor meer informatie over het controleren en oplossen van problemen met uw Azure-cache voor redis-instanties [Azure cache bewaken voor redis](cache-how-to-monitor.md) en de verschillende richt lijnen voor probleem oplossing.

* [Hoe kan ik de status en prestaties van mijn cache controleren?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Waarom kan ik time-outs bekijken?](#why-am-i-seeing-timeouts)
* [Waarom is de verbinding tussen mijn client en de cache verbroken?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Veelgestelde vragen over eerdere cache aanbiedingen
* [Welke Azure-cache aanbieding is geschikt voor mij?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-cache-for-redis"></a>Wat is Azure Cache voor Redis?
Azure cache voor redis is gebaseerd op de populaire open-source software [redis](https://redis.io/). Hiermee krijgt u toegang tot een beveiligde, toegewezen Azure-cache voor redis, die wordt beheerd door micro soft en toegankelijk vanuit elke toepassing in Azure. Zie de product pagina [van Azure cache for redis](https://azure.microsoft.com/services/cache/) op Azure.com voor een gedetailleerder overzicht.

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Hoe kan ik aan de slag met Azure cache voor redis?
Er zijn verschillende manieren om aan de slag te gaan met Azure cache voor redis.

* U kunt een van de zelf studies bekijken die beschikbaar zijn voor [.net](cache-dotnet-how-to-use-azure-redis-cache.md), [ASP.net](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md)en [python](cache-python-get-started.md).
* U kunt bekijken [hoe u krachtige apps bouwt met Microsoft Azure cache voor redis](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/).
* U kunt de client documentatie raadplegen voor de clients die overeenkomen met de ontwikkelings taal van uw project om te zien hoe u redis kunt gebruiken. Er zijn veel redis-clients die kunnen worden gebruikt met Azure cache voor redis. Zie voor een lijst met redis-clients [https://redis.io/clients](https://redis.io/clients) .

Als u nog geen Azure-account hebt, kunt u het volgende doen:

* [Gratis een Azure-account openen](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). U ontvangt tegoed dat kan worden gebruikt om betaalde Azure-services te proberen. Zelfs nadat het tegoed is gebruikt, kunt u het account houden en de gratis Azure-services en -functies gebruiken.
* [Uw voordelen als Visual Studio-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Via uw MSDN-abonnement ontvangt u elke maand tegoeden die u voor betaalde Azure-services kunt gebruiken.

<a name="cache-size"></a>

### <a name="what-azure-cache-for-redis-offering-and-size-should-i-use"></a>Wat is Azure cache voor redis aanbieding en grootte moet ik gebruiken?
Elke Azure-cache voor redis biedt verschillende opties voor **grootte**, **band breedte**, **hoge Beschik baarheid**en **Sla** .

Hier volgen enkele aandachtspunten voor het kiezen van een cache aanbieding.

* **Geheugen**: de lagen basis en standaard bieden 250 MB – 53 GB. De Premium-laag biedt tot 1,2 TB (als een cluster) of 120 GB (niet-geclusterd). Zie voor meer informatie [Azure cache for redis prijzen](https://azure.microsoft.com/pricing/details/cache/).
* **Netwerk prestaties**: als u een werk belasting hebt waarvoor hoge door Voer is vereist, biedt de Premium-laag meer band breedte dan Standard of Basic. Daarnaast hebben grotere caches in elke laag meer band breedte vanwege de onderliggende virtuele machine die als host fungeert voor de cache. Zie de [volgende tabel](#cache-performance)voor meer informatie.
* **Door Voer**: de Premium-laag biedt de Maxi maal beschik bare door voer. Als de cache server of client de bandbreedte limiet bereikt, ontvangt u mogelijk time-outs aan de client zijde. Zie de volgende tabel voor meer informatie.
* **Hoge Beschik baarheid/Sla**: Azure cache voor redis garandeert dat een Standard/Premium-cache ten minste 99,9% van de tijd beschikbaar is. Zie voor meer informatie over onze SLA [Azure cache for redis prijzen](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). De SLA heeft alleen betrekking op connectiviteit met de cache-eind punten. De SLA heeft geen betrekking op beveiliging tegen gegevens verlies. We raden u aan de functie voor redis-gegevens persistentie in de Premium-laag te gebruiken om de tolerantie voor gegevens verlies te verg Roten.
* **Redis-gegevens persistentie**: met de Premium-laag kunt u de cache gegevens in een Azure Storage-account behouden. In een Basic-of Standard-cache worden alle gegevens alleen in het geheugen opgeslagen. Onderliggende problemen met de infra structuur kunnen leiden tot mogelijk gegevens verlies. We raden u aan de functie voor redis-gegevens persistentie in de Premium-laag te gebruiken om de tolerantie voor gegevens verlies te verg Roten. Azure cache voor redis biedt redis-persistentie opties voor RDB-en AOF (preview-versie). Zie [persistentie configureren voor een Premium Azure-cache voor redis](cache-how-to-premium-persistence.md)voor meer informatie.
* **Redis-cluster**: als u caches wilt maken die groter zijn dan 120 GB of als u gegevens wilt Shard tussen meerdere redis-knoop punten, kunt u redis clustering gebruiken, die beschikbaar is in de laag Premium. Elk knoop punt bestaat uit een primair/replica-cache paar voor maximale Beschik baarheid. Zie [clustering configureren voor een Premium Azure-cache voor redis](cache-how-to-premium-clustering.md)voor meer informatie.
* **Verbeterde beveiliging en netwerk isolatie**: Azure Virtual Network (VNET)-implementatie biedt verbeterde beveiliging en isolatie voor uw Azure-cache voor redis, evenals subnetten, beleid voor toegangs beheer en andere functies om de toegang verder te beperken. Zie [Virtual Network-ondersteuning configureren voor een Premium Azure-cache voor redis](cache-how-to-premium-vnet.md)voor meer informatie.
* **Redis configureren**: in de standaard-en Premium-laag kunt u redis configureren voor de meldingen van de opdracht regel.
* **Maximum aantal client verbindingen**: de Premium-laag biedt het maximum aantal clients dat verbinding kan maken met redis, met een groter aantal verbindingen voor grotere caches. Bij clustering wordt het aantal beschik bare verbindingen voor een geclusterde cache niet verhoogd. Zie voor meer informatie [Azure cache for redis prijzen](https://azure.microsoft.com/pricing/details/cache/).
* **Toegewezen kern geheugen voor redis-server**: in de Premium-laag zijn alle cache grootten een toegewezen kern voor redis. In de lagen basis en standaard hebben de C1-grootte en het bovenstaande een toegewezen kern voor redis-server.
* **Redis is een enkele thread** , waardoor meer dan twee kernen geen extra voor deel bieden ten opzichte van twee kernen, maar grotere VM-grootten hebben meestal meer band breedte dan kleinere grootten. Als de cache server of-client de bandbreedte limiet bereikt, ontvangt u time-outs aan de client zijde.
* **Prestatie verbeteringen**: caches in de Premium-laag worden geïmplementeerd op hardware met snellere processors, waardoor betere prestaties in vergelijking met de basis-of standaard-laag worden verbeterd. Premium-laag caches hebben een hogere door Voer en een lagere latentie.

<a name="cache-performance"></a>

### <a name="azure-cache-for-redis-performance"></a>Azure cache voor redis-prestaties
De volgende tabel bevat de maximum waarden voor de band breedte tijdens het testen van verschillende groottes van de standaard-en Premium-caches met behulp `redis-benchmark.exe` van een IaaS-VM op basis van de Azure-cache voor redis-eind punt. Voor TLS-door Voer wordt redis-Bench Mark gebruikt met stunnel om verbinding te maken met het Azure-cache geheugen voor redis-eind punten.

>[!NOTE] 
>Deze waarden zijn niet gegarandeerd en er is geen SLA voor deze getallen, maar het moet gebruikelijk zijn. U moet de test uw eigen toepassing laden om de juiste cache grootte voor uw toepassing te bepalen.
>Deze nummers kunnen veranderen wanneer er periodiek nieuwere resultaten worden geplaatst.
>

In deze tabel kunnen we de volgende conclusies tekenen:

* De door Voer voor de caches die dezelfde grootte hebben, is hoger in de laag Premium, vergeleken met de laag standaard. Met een cache van 6 GB is de door Voer van P1 bijvoorbeeld 180.000 aanvragen per seconde (RPS) in vergelijking met 100.000 RPS voor C3.
* Met redis clustering neemt de door Voer lineair toe als u het aantal Shards (knoop punten) in het cluster verhoogt. Als u bijvoorbeeld een P4-cluster van 10 Shards maakt, is de beschik bare door Voer 400.000 * 10 = 4.000.000 RPS.
* De door Voer voor grotere sleutel grootten is hoger in de Premium-laag, vergeleken met de Standard-laag.

| Prijscategorie | Grootte | CPU-kernen | Beschik bare band breedte | 1-grootte van KB-waarde | 1-grootte van KB-waarde |
| --- | --- | --- | --- | --- | --- |
| **Standaard cache grootten** | | |**Megabits per seconde (MB/s)/mega bytes per seconde (MB/s)** |**Aantal aanvragen per seconde (RPS) niet-SSL** |**SSL-aanvragen per seconde (RPS)** |
| C0 | 250 MB | Gedeeld | 100/12,5  |  15.000 |   7\.500 |
| C1 |   1 GB | 1      | 500/62,5  |  38.000 |  20.720 |
| C2 | 2,5 GB | 2      | 500/62,5  |  41.000 |  37.000 |
| Stand |   6 GB | 4      | 1000/125  | 100.000 |  90,000 |
| C4 |  13 GB | 2      | 500/62,5  |  60.000 |  55.000 |
| C5 |  26 GB | 4      | 1.000/125 | 102.000 |  93.000 |
| C6 |  53 GB | 8      | 2.000/250 | 126.000 | 120.000 |
| **Premium-cache grootten** | |**CPU-kernen per Shard** | **Megabits per seconde (MB/s)/mega bytes per seconde (MB/s)** |**Aanvragen per seconde (RPS) niet-SSL, per Shard** |**SSL-aanvragen per seconde (RPS) per Shard** |
| P1 |   6 GB |  2 | 1.500/187,5 | 180.000 | 172.000 |
| P2 |  13 GB |  4 | 3.000/375   | 350.000 | 341.000 |
| P3 |  26 GB |  4 | 3.000/375   | 350.000 | 341.000 |
| P4 |  53 GB |  8 | 6.000/750   | 400,000 | 373.000 |
| P5 | 120 GB | 20 | 6.000/750   | 400,000 | 373.000 |

Voor instructies voor het instellen van stunnel of het downloaden van de redis `redis-benchmark.exe` -hulpprogram ma's, zoals, raadpleegt u de sectie [Hoe kan ik redis-opdrachten uitvoeren?](#cache-commands)

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>In welke regio moet ik mijn cache vinden?
Voor de beste prestaties en de laagste latentie zoekt u uw Azure-cache voor redis in dezelfde regio als uw cache-client toepassing.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Hoe word ik gefactureerd voor Azure-cache voor redis?
Azure cache voor redis-prijzen is [hier](https://azure.microsoft.com/pricing/details/cache/). Op de pagina met prijzen worden prijzen weer gegeven als uurtarief. Caches worden per minuut in rekening gebracht vanaf het moment dat de cache wordt gemaakt tot het moment dat een cache wordt verwijderd. Er is geen optie voor het stoppen of onderbreken van de facturering van een cache.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Kan ik Azure cache gebruiken voor redis met Azure Government Cloud, Azure China Cloud of Microsoft Azure Duitsland?
Ja, Azure cache voor redis is beschikbaar in Azure Government Cloud, Azure China 21Vianet-Cloud en Microsoft Azure Duitsland. De Url's voor toegang tot en beheer van Azure cache voor redis verschillen in de clouds in vergelijking met de open bare Azure-Cloud.

| Cloud   | DNS-achtervoegsel voor redis            |
|---------|---------------------------------|
| Public  | *. redis.cache.windows.net       |
| US Gov  | *. redis.cache.usgovcloudapi.net |
| Duitsland | *. redis.cache.cloudapi.de       |
| China   | *. redis.cache.chinacloudapi.cn  |

Zie de volgende koppelingen voor meer informatie over overwegingen bij het gebruik van Azure cache voor redis met andere Clouds.

- [Azure Government-data bases-Azure cache voor redis](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [Azure China 21Vianet Cloud-Azure cache voor redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Duitsland](https://azure.microsoft.com/overview/clouds/germany/)

Zie [verbinding maken met andere Clouds-Azure cache voor redis Power shell](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds)voor meer informatie over het gebruik van Azure cache voor redis met Power shell in azure Government Cloud, Azure China 21Vianet-cloud en Microsoft Azure Duitsland.

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>Wat doen de configuratie opties voor stack Exchange. redis?
Stack Exchange. redis heeft veel opties. In deze sectie vindt u enkele van de algemene instellingen. Zie [stack Exchange. redis-configuratie](https://stackexchange.github.io/StackExchange.Redis/Configuration)voor meer gedetailleerde informatie over de opties voor stack Exchange. redis.

| ConfigurationOptions | Beschrijving | Aanbeveling |
| --- | --- | --- |
| AbortOnConnectFail |Als deze eigenschap is ingesteld op True, wordt er geen verbinding gemaakt na een netwerk fout. |Stel deze waarde in op False en laat stack Exchange. redis automatisch opnieuw verbinding maken. |
| ConnectRetry |Het aantal keren dat verbindings pogingen moeten worden herhaald tijdens de eerste verbinding. |Raadpleeg de volgende opmerkingen voor hulp. |
| ConnectTimeout |Time-out in MS voor verbindings bewerkingen. |Raadpleeg de volgende opmerkingen voor hulp. |

Meestal zijn de standaard waarden van de client voldoende. U kunt de opties aanpassen op basis van uw werk belasting.

* **Nieuwe pogingen**
  * De algemene richt lijnen voor ConnectRetry en ConnectTimeout zijn snel en opnieuw proberen. Deze richt lijnen zijn gebaseerd op uw werk belasting en hoe lang het duurt voordat uw client een redis-opdracht verzendt en een antwoord ontvangt.
  * Laat stack Exchange. redis automatisch opnieuw verbinding maken in plaats van de verbindings status te controleren en zelf verbinding te maken. **Vermijd het gebruik van de eigenschap ConnectionMultiplexer. IsConnected**.
  * Snowballing: soms is het mogelijk dat u een probleem ondervindt waarbij u het opnieuw probeert en de nieuwe pogingen Snowball en nooit herstellen. Als snowballing zich voordoet, kunt u het beste een exponentiële uitstel-algoritme gebruiken, zoals beschreven in [algemene richt lijnen voor opnieuw proberen](../best-practices-retry-general.md) die worden gepubliceerd door de micro soft-patronen & practices-groep.
  
* **Time-outwaarden**
  * Denk aan uw werk belasting en stel de waarden dienovereenkomstig in. Als u grote waarden opslaat, stelt u de time-out in op een hogere waarde.
  * Stel deze waarde `AbortOnConnectFail` in op False en laat stack Exchange. redis opnieuw verbinding maken.
  * Gebruik één ConnectionMultiplexer-exemplaar voor de toepassing. U kunt een LazyConnection gebruiken om één exemplaar te maken dat wordt geretourneerd door een eigenschap Connection, zoals wordt weer gegeven in [verbinding maken met de cache met behulp van de klasse ConnectionMultiplexer](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache).
  * Stel de `ConnectionMultiplexer.ClientName` eigenschap in op een unieke naam voor het app-exemplaar voor diagnostische doel einden.
  * Gebruik meerdere `ConnectionMultiplexer` instanties voor aangepaste werk belastingen.
      * U kunt dit model volgen als uw toepassing variërend wordt geladen. Bijvoorbeeld:
      * U kunt één multiplexer voor het omgaan met grote sleutels.
      * U kunt één multiplexer voor het omgaan met kleine sleutels hebben.
      * U kunt verschillende waarden instellen voor verbindingstime-outs en pogings logica voor elke ConnectionMultiplexer die u gebruikt.
      * Stel de `ClientName` eigenschap in op elke multiplexer om te helpen met diagnostische gegevens.
      * Deze richt lijnen kunnen leiden tot meer gestroomlijnde latentie per `ConnectionMultiplexer` .

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Welke Azure-cache voor redis-clients kan ik gebruiken?
Een van de fantastische dingen over redis is dat er veel clients zijn die veel verschillende ontwikkelings talen ondersteunen. Zie [redis-clients](https://redis.io/clients)voor een actuele lijst met clients. Zie [Azure cache gebruiken voor redis](cache-dotnet-how-to-use-azure-redis-cache.md) en de artikelen in de inhouds opgave voor meer zelf studies over verschillende talen en clients.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Is er een lokale emulator voor Azure cache voor redis?
Er is geen lokale emulator voor Azure cache voor redis, maar u kunt de MSOpenTech-versie van redis-server.exe uitvoeren vanaf de [opdracht regel Programma's redis](https://github.com/MSOpenTech/redis/releases/) op uw lokale computer en er verbinding mee maken om een vergelijk bare ervaring te krijgen met een lokale cache-emulator, zoals wordt weer gegeven in het volgende voor beeld:

    private static Lazy<ConnectionMultiplexer>
          lazyConnection = new Lazy<ConnectionMultiplexer>
        (() =>
        {
            // Connect to a locally running instance of Redis to simulate a local cache emulator experience.
            return ConnectionMultiplexer.Connect("127.0.0.1:6379");
        });

        public static ConnectionMultiplexer Connection
        {
            get
            {
                return lazyConnection.Value;
            }
        }


U kunt eventueel een bestand [redis. conf](https://redis.io/topics/config) configureren dat meer nauw keurig overeenkomt met de [standaard cache-instellingen](cache-configure.md#default-redis-server-configuration) voor uw online Azure-cache voor redis, indien gewenst.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Hoe kan ik redis-opdrachten uitvoeren?
U kunt elk van de opdrachten die worden weer gegeven op [redis opdrachten](https://redis.io/commands#) gebruiken, met uitzonde ring van de opdrachten die worden weer gegeven op [redis-opdrachten die niet worden ondersteund in azure cache voor redis](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis). U hebt verschillende opties om redis-opdrachten uit te voeren.

* Als u een Standard-of Premium-cache hebt, kunt u redis-opdrachten uitvoeren met behulp van de [redis-console](cache-configure.md#redis-console). De redis-console biedt een veilige manier om redis-opdrachten uit te voeren in de Azure Portal.
* U kunt ook de redis-opdracht regel Programma's gebruiken. Als u deze wilt gebruiken, voert u de volgende stappen uit:
* Down load de [redis-opdracht regel Programma's](https://github.com/MSOpenTech/redis/releases/).
* Verbinding maken met de cache met behulp van `redis-cli.exe` . Pass het cache-eind punt met behulp van de-h-switch en de sleutel met-a zoals wordt weer gegeven in het volgende voor beeld:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> De opdracht regel Programma's voor redis werken niet met de TLS-poort, maar u kunt een hulp programma gebruiken `stunnel` om de hulpprogram ma's veilig te verbinden met de TLS-poort door de instructies te volgen in het [opdracht regel programma redis gebruiken met Azure cache for redis](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-redis-cli-tool) .
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Waarom heeft Azure cache voor redis geen verwijzing naar een MSDN Class-bibliotheek, zoals een aantal andere Azure-Services?
Microsoft Azure Cache voor Redis is gebaseerd op de populaire open source Azure Cache voor Redis. Het kan worden gebruikt door een groot aantal [redis-clients](https://redis.io/clients) voor veel programmeer talen. Elke client heeft zijn eigen API die aanroepen naar de Azure-cache voor redis-exemplaar met behulp van [redis-opdrachten](https://redis.io/commands).

Omdat elke client verschilt, is er niet één gecentraliseerde klassen verwijzing op MSDN en houdt elke client zijn eigen referentie documentatie bij. Naast de referentie documentatie zijn er verschillende zelf studies die laten zien hoe u aan de slag gaat met Azure cache voor redis met behulp van verschillende talen en cache-clients. Voor toegang tot deze zelf studies raadpleegt u [Azure cache gebruiken voor redis](cache-dotnet-how-to-use-azure-redis-cache.md) en de artikelen in de inhouds opgave op hetzelfde niveau.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Kan ik Azure cache gebruiken voor redis als een PHP-sessie cache?
Ja, als u Azure cache voor redis wilt gebruiken als een PHP-sessie cache, geeft u de connection string op voor uw Azure-cache voor redis-instantie in `session.save_path` .

> [!IMPORTANT]
> Wanneer u Azure cache gebruikt voor redis als een PHP-sessie cache, moet u de URL coderen die wordt gebruikt om verbinding te maken met de cache, zoals wordt weer gegeven in het volgende voor beeld:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Als de sleutel geen URL-code ring heeft, ontvangt u mogelijk een uitzonde ring met een bericht zoals:`Failed to parse session.save_path`
>
>

Zie [php session handler](https://github.com/phpredis/phpredis#php-session-handler)voor meer informatie over het gebruik van Azure cache voor redis als een PHP-sessie cache met de PhpRedis-client.

### <a name="what-are-redis-databases"></a>Wat zijn redis-data bases?

Redis-data bases vormen alleen een logische schei ding van gegevens binnen hetzelfde redis-exemplaar. Het cache geheugen wordt gedeeld tussen alle data bases en het werkelijke geheugen gebruik van een bepaalde data base is afhankelijk van de sleutels/waarden die zijn opgeslagen in die data base. Een C6-cache heeft bijvoorbeeld 53 GB geheugen en een P5 heeft 120 GB. U kunt ervoor kiezen om alle 53 GB/120 GB in één Data Base te plaatsen of u kunt deze verdelen over meerdere data bases. 

> [!NOTE]
> Wanneer u een Premium Azure-cache gebruikt voor redis waarbij Clustering is ingeschakeld, is alleen data base 0 beschikbaar. Deze beperking is een intrinsieke redis-beperking en is niet specifiek voor Azure-cache voor redis. Zie voor meer informatie [moet ik wijzigingen aanbrengen in mijn client toepassing voor het gebruik van clusters?](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-tlsssl-port-for-connecting-to-redis"></a>Wanneer moet ik de niet-TLS/SSL-poort inschakelen om verbinding te maken met redis?
Redis server biedt geen systeem eigen ondersteuning voor TLS, maar Azure cache voor redis wel. Als u verbinding maakt met Azure cache voor redis en uw client TLS ondersteunt, zoals stack Exchange. redis, moet u TLS gebruiken.

>[!NOTE]
>De niet-TLS-poort is standaard uitgeschakeld voor nieuwe Azure-cache voor redis-exemplaren. Als uw client geen TLS ondersteunt, moet u de niet-TLS-poort inschakelen door de instructies in de sectie [toegangs poorten](cache-configure.md#access-ports) van het artikel [een cache in azure-cache configureren voor redis](cache-configure.md) te volgen.
>
>

Redis-hulpprogram ma's zoals `redis-cli` niet werken met de TLS-poort, maar u kunt een hulp programma gebruiken `stunnel` om de hulpprogram ma's veilig te verbinden met de TLS-poort door de instructies in de aankondiging van de [ASP.net-sessie status provider voor redis preview release](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx) -blog post te volgen.

Voor instructies over het downloaden van de redis-hulpprogram ma's, zie de sectie [Hoe kan ik redis-opdrachten uitvoeren?](#cache-commands)

### <a name="what-are-some-production-best-practices"></a>Wat zijn de aanbevolen procedures voor productie?
* [Best practices voor stack Exchange. redis](#stackexchangeredis-best-practices)
* [Configuratie en concepten](#configuration-and-concepts)
* [Prestaties testen](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>Best practices voor stack Exchange. redis
* Stel `AbortConnect` deze waarde in op False en laat de ConnectionMultiplexer automatisch opnieuw verbinding maken. [Zie hier voor meer informatie](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* De ConnectionMultiplexer opnieuw gebruiken: Maak geen nieuwe voor elke aanvraag. Het `Lazy<ConnectionMultiplexer>` patroon dat [hier wordt weer gegeven](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) , wordt aanbevolen.
* Redis werkt het beste met kleinere waarden. u kunt dus grotere gegevens in meerdere sleutels afzetten. In [deze redis-discussie](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ)wordt 100 kB als groot beschouwd. Lees [dit artikel](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) voor een voor beeld van een probleem dat kan worden veroorzaakt door grote waarden.
* Configureer de [instellingen van uw thread pool](#important-details-about-threadpool-growth) om time-outs te voor komen.
* Gebruik ten minste de standaard connectTimeout van 5 seconden. Dit interval geeft stack Exchange. redis voldoende tijd om de verbinding te herstellen in het geval van een netwerk Blip.
* Houd rekening met de prestatie kosten die zijn gekoppeld aan de verschillende bewerkingen die u uitvoert. De `KEYS` opdracht is bijvoorbeeld een O (n) bewerking en moet worden vermeden. De [redis.io-site](https://redis.io/commands/) bevat details over de tijd complexiteit voor elke bewerking die wordt ondersteund. Klik op elke opdracht om de complexiteit van elke bewerking weer te geven.

#### <a name="configuration-and-concepts"></a>Configuratie en concepten
* Gebruik de standaard-of Premium-laag voor productie systemen. De laag Basic is een systeem met één knooppunt zonder gegevensreplicatie en zonder SLA. Gebruik minimaal een C1-cache. C0-caches worden doorgaans gebruikt voor eenvoudige ontwikkel-en test scenario's.
* Houd er rekening mee dat redis een **in-Memory-** gegevens archief is. Lees [dit artikel](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) zodat u op de hoogte bent van scenario's waarin gegevens verlies zich kan voordoen.
* Ontwikkel uw systeem zodat het verbinding problemen kan verwerken [vanwege patches en failover](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Prestaties testen
* Begin met `redis-benchmark.exe` om een mogelijke door voer te krijgen voordat u uw eigen prestatie tests schrijft. Omdat `redis-benchmark` TLS niet wordt ondersteund, moet u [de niet-TLS-poort inschakelen via de Azure Portal](cache-configure.md#access-ports) voordat u de test uitvoert. Zie [Hoe kan ik Bench Mark en de prestaties van mijn cache testen](#how-can-i-benchmark-and-test-the-performance-of-my-cache) voor voor beelden.
* De client-VM die wordt gebruikt voor het testen moet zich in dezelfde regio bevinden als uw Azure-cache voor redis-exemplaar.
* We raden u aan om een dv2-VM-reeks te gebruiken voor uw client, omdat deze betere hardware heeft en de beste resultaten moeten bieden.
* Zorg ervoor dat uw client-VM die u kiest, ten minste zoveel computer-en bandbreedte capaciteit heeft als de cache die u wilt testen.
* Schakel VRSS in op de client computer als u zich in Windows bevindt. [Zie hier voor meer informatie](https://technet.microsoft.com/library/dn383582.aspx).
* Redis-exemplaren van de Premium-laag hebben een betere netwerk latentie en door Voer omdat ze worden uitgevoerd op betere hardware voor zowel CPU als netwerk.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Wat zijn enkele aandachtspunten bij het gebruik van algemene redis-opdrachten?

* Vermijd het gebruik van bepaalde redis-opdrachten die veel tijd in beslag nemen, tenzij u de impact van deze opdrachten volledig begrijpt. Voer bijvoorbeeld niet de opdracht [sleutels](https://redis.io/commands/keys) in productie uit. Afhankelijk van het aantal sleutels kan het lang duren om te retour neren. Redis is een server met één thread en verwerkt opdrachten een voor een. Als u andere opdrachten hebt uitgegeven na sleutels, worden ze niet verwerkt totdat redis de opdracht sleutels verwerkt. De [redis.io-site](https://redis.io/commands/) bevat details over de tijd complexiteit voor elke bewerking die wordt ondersteund. Klik op elke opdracht om de complexiteit van elke bewerking weer te geven.
* Sleutel grootten: moet ik kleine sleutel/waarden of grote sleutel/waarden gebruiken? Dit is afhankelijk van het scenario. Als voor uw scenario grotere sleutels zijn vereist, kunt u de ConnectionTimeout aanpassen, vervolgens waarden opnieuw proberen en de logica voor opnieuw proberen aan te passen. In een redis-server perspectief bieden kleinere waarden betere prestaties.
* Dit betekent niet dat u geen grotere waarden in redis kunt opslaan. u moet rekening houden met de volgende overwegingen. De latentie is hoger. Als u een set gegevens hebt die groter is en een kleiner aantal, kunt u meerdere ConnectionMultiplexer-instanties gebruiken, die allemaal zijn geconfigureerd met een andere set time-out-en nieuwe waarden, zoals wordt beschreven in de vorige sectie [de configuratie opties voor de stack Exchange. redis](#cache-configuration) .

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Hoe kan ik Bench Mark en test de prestaties van mijn cache?
* [Schakel de diagnostische gegevens van de cache in](cache-how-to-monitor.md#enable-cache-diagnostics), zodat u de status van de cache kunt [bewaken](cache-how-to-monitor.md). U kunt de metrische gegevens weer geven in de Azure Portal en u kunt ze ook [downloaden en bekijken](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) met de hulpprogram ma's van uw keuze.
* U kunt redis-benchmark.exe gebruiken om test uw redis-server te laden.
* Zorg ervoor dat de belasting test-client en de Azure-cache voor redis zich in dezelfde regio bevinden.
* Gebruik redis-cli.exe en bewaak de cache met behulp van de opdracht INFO.
* Als uw belasting een hoge geheugen fragmentatie veroorzaakt, moet u omhoog schalen naar een grotere cache grootte.
* Voor instructies over het downloaden van de redis-hulpprogram ma's, zie de sectie [Hoe kan ik redis-opdrachten uitvoeren?](#cache-commands)

De volgende opdrachten bieden een voor beeld van het gebruik van redis-benchmark.exe. Voor nauw keurige resultaten voert u deze opdrachten uit vanaf een virtuele machine in dezelfde regio als uw cache.

* SET-aanvragen met pipeline testen met behulp van een nettolading van 1 KB

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Test aanvragen voor het ophalen van pijp lijnen met een payload van 1 KB.
  Opmerking: Voer de SET-test hierboven eerst uit om de cache in te vullen

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Belang rijke informatie over de groei van de thread pool
De CLR-thread pool heeft twee soorten threads: "worker" en "I/O-voltooiings poort" (IOCP).

* Worker-threads worden gebruikt voor zaken zoals het verwerken van de `Task.Run(…)` -of- `ThreadPool.QueueUserWorkItem(…)` methoden. Deze threads worden ook gebruikt door verschillende onderdelen in de CLR wanneer het werk moet plaatsvinden op een achtergrond thread.
* IOCP-threads worden gebruikt wanneer asynchrone IO plaatsvindt, bijvoorbeeld bij het lezen van het netwerk.

De thread pool biedt nieuwe werkthreads of I/O-voltooiings threads op aanvraag (zonder enige beperking) totdat de instelling ' minimum ' voor elk type thread wordt bereikt. Standaard is het minimum aantal threads ingesteld op het aantal processors op een systeem.

Zodra het aantal bestaande (bezette) threads het ' minimale ' aantal threads aankomt, wordt door de thread pool de snelheid beperkt waarmee nieuwe threads worden geinjecteerd op één thread per 500 milliseconden. Als uw systeem bijvoorbeeld een burst van een IOCP-thread krijgt, wordt dat proces snel uitgevoerd. Als de burst van work echter meer is dan de geconfigureerde minimum instelling, zal er enige vertraging optreden bij het verwerken van een deel van het werk wanneer de thread pool wacht om een van twee dingen te doen.

1. Een bestaande thread wordt gratis om het werk te verwerken.
2. Er wordt geen bestaande thread gratis voor 500 MS, dus er wordt een nieuwe thread gemaakt.

In principe betekent dit dat wanneer het aantal bezette threads groter is dan de minimale threads, u waarschijnlijk een vertraging van 500 MS betaalt voordat het netwerk verkeer door de toepassing wordt verwerkt. Het is ook belang rijk te weten dat wanneer een bestaande thread langer dan 15 seconden inactief blijft (op basis van wat ik weet), deze wordt opgeschoond en deze groei en krimping kan worden herhaald.

Als we een voor beeld van een fout bericht van stack Exchange. redis (build 1.0.450 of hoger) bekijken, ziet u dat er nu thread pool-statistieken worden afgedrukt (Zie de details van IOCP en WORKer hieronder).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

In het vorige voor beeld ziet u dat er voor IOCP-thread zes bezette threads zijn en het systeem zo is geconfigureerd dat vier minimale threads zijn toegestaan. In dit geval zou de client waarschijnlijk 2 500-MS vertragingen hebben gezien, omdat 6 > 4.

Houd er rekening mee dat stack Exchange. redis time-outs kan opraken als de groei van IOCP-of WORKer-threads wordt beperkt.

### <a name="recommendation"></a>Aanbeveling

Op basis van deze informatie raden we u ten zeerste aan dat klanten de minimale configuratie waarde voor IOCP-en WORKer-threads instellen op iets groter dan de standaard waarde. We bieden geen ondersteuning voor het formaat van één grootte, zoals de juiste waarde voor de ene toepassing waarschijnlijk te hoog of laag is voor een andere toepassing. Deze instelling kan ook van invloed zijn op de prestaties van andere onderdelen van gecompliceerde toepassingen, zodat elke klant deze instelling moet aanpassen aan hun specifieke behoeften. Een goede start locatie is 200 of 300 en vervolgens naar behoefte testen en verfijnen.

Deze instelling configureren:

* U wordt aangeraden deze instelling programmatisch te wijzigen met behulp van de methode [thread pool. SetMinThreads (...)](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) in `global.asax.cs` . Bijvoorbeeld:

```cs
private readonly int minThreads = 200;
void Application_Start(object sender, EventArgs e)
{
    // Code that runs on application startup
    AreaRegistration.RegisterAllAreas();
    RouteConfig.RegisterRoutes(RouteTable.Routes);
    BundleConfig.RegisterBundles(BundleTable.Bundles);
    ThreadPool.SetMinThreads(minThreads, minThreads);
}
```

  > [!NOTE]
  > De waarde die wordt opgegeven met deze methode is een algemene instelling die van invloed is op het hele AppDomain. Als u bijvoorbeeld een computer met vier kernen hebt en *minWorkerThreads* en *minIoThreads* wilt instellen op 50 per CPU tijdens runtime, gebruikt u **thread pool. SetMinThreads (200, 200)**.

* Het is ook mogelijk om de instelling minimale threads op te geven met behulp van de [configuratie-instelling *MinIoThreads* of *minWorkerThreads* ](https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx) onder het `<processModel>` configuratie-element in `Machine.config` , meestal bevindt zich op `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\` . **Het instellen van het aantal minimale threads op deze manier wordt doorgaans niet aanbevolen, omdat het een instelling voor het hele systeem is.**

  > [!NOTE]
  > De waarde die is opgegeven in dit configuratie-element is een instelling *per kern* . Als u bijvoorbeeld een computer met vier kernen hebt en u wilt dat uw *minIoThreads* -instelling 200 tijdens runtime, gebruikt u `<processModel minIoThreads="50"/>` .
  >

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Schakel Server GC in om meer door voer te krijgen op de client wanneer stack Exchange. redis wordt gebruikt
Als server GC wordt ingeschakeld, kan de client worden geoptimaliseerd en kunnen de prestaties en door voer worden verbeterd wanneer stack Exchange. redis wordt gebruikt. Raadpleeg de volgende artikelen voor meer informatie over server GC en hoe u deze kunt inschakelen:

* [Server GC inschakelen](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Basis principes van garbagecollection](/dotnet/standard/garbage-collection/fundamentals)
* [Garbagecollection en prestaties](/dotnet/standard/garbage-collection/performance)


### <a name="performance-considerations-around-connections"></a>Prestatie overwegingen rond verbindingen

Elke prijs categorie heeft verschillende limieten voor client verbindingen, geheugen en band breedte. Hoewel elke grootte van de cache een bepaald aantal *verbindingen toestaat,* is er aan elke verbinding met redis overhead gekoppeld. Een voor beeld van een dergelijke overhead is het CPU-en geheugen gebruik als gevolg van TLS/SSL-versleuteling. Voor de maximum verbindings limiet voor een bepaalde cache grootte wordt uitgegaan van een licht geladen cache. Als de belasting van de verbindings overhead *plus* de belasting van client bewerkingen de capaciteit voor het systeem overschrijdt, kan de cache capaciteits problemen ondervinden, zelfs als u de verbindings limiet voor de huidige cache grootte niet overschrijdt.

Zie [Azure cache for redis prijzen](https://azure.microsoft.com/pricing/details/cache/)voor meer informatie over de verschillende verbindings limieten voor elke laag. Zie [standaard redis-server configuratie](cache-configure.md#default-redis-server-configuration)voor meer informatie over verbindingen en andere standaard configuraties.

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Hoe kan ik de status en prestaties van mijn cache controleren?
Microsoft Azure cache voor redis-instanties kunnen worden bewaakt in de [Azure Portal](https://portal.azure.com). U kunt metrische gegevens weer geven, metrische grafieken vastmaken aan het start Board, het datum-en tijds bereik van bewakings grafieken aanpassen, metrische gegevens aan de grafieken toevoegen en verwijderen, en waarschuwingen instellen wanneer aan bepaalde voor waarden wordt voldaan. Zie [Azure-cache bewaken voor redis](cache-how-to-monitor.md)voor meer informatie.

Het menu Azure cache for redis **resource** bevat ook verschillende hulpprogram ma's voor het controleren en oplossen van problemen met uw caches.

* **Problemen vaststellen en oplossen** biedt informatie over veelvoorkomende problemen en strategieën voor het oplossen ervan.
* De **resource status** houdt uw resource in de gaten en vertelt u of deze wordt uitgevoerd zoals verwacht. Zie [overzicht van Azure resource Health](../resource-health/resource-health-overview.md)voor meer informatie over de Azure resource Health-Service.
* **Nieuwe ondersteunings aanvraag** biedt opties voor het openen van een ondersteunings aanvraag voor uw cache.

Met deze hulpprogram ma's kunt u de status van uw Azure-cache bewaken voor redis-instanties en helpt u bij het beheren van uw cache toepassingen. Zie voor meer informatie de sectie ' ondersteuning voor het oplossen van problemen &-instellingen ' van het [configureren van Azure cache voor redis](cache-configure.md).

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Waarom kan ik time-outs bekijken?
Time-outs doen zich voor op de client die u gebruikt om te praten met redis. Wanneer een opdracht wordt verzonden naar de redis-server, wordt de opdracht in de wachtrij geplaatst en wordt de redis-server uiteindelijk de opdracht verzameld en wordt deze uitgevoerd. De client kan echter een time-out hebben tijdens dit proces en als er een uitzonde ring optreedt op de aanroepende zijde. Zie [problemen met](cache-troubleshoot-client.md) de time-out op de client en [stack Exchange. redis](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions)voor meer informatie over het oplossen van problemen met time-outs.

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Waarom is de verbinding tussen mijn client en de cache verbroken?
Hier volgen enkele veelvoorkomende redenen voor het verbreken van een cache.

* Oorzaken van de client
  * De client toepassing is opnieuw geïmplementeerd.
  * De client toepassing heeft een schaal bewerking uitgevoerd.
    * In het geval van Cloud Services of Web Apps kan dit worden veroorzaakt door automatisch schalen.
  * De laag van het netwerk aan de client zijde is gewijzigd.
  * Er zijn tijdelijke fouten opgetreden in de client of in de netwerk knooppunten tussen de client en de server.
  * De drempel waarden voor de band breedte zijn bereikt.
  * Het duurt te lang om aan de CPU gebonden bewerkingen te volt ooien.
* Oorzaken die zich aan de server zijde bevindt
  * Op de standaard cache aanbieding heeft de service Azure cache for redis een failover geïnitieerd van het primaire knoop punt naar het secundaire knoop punt.
  * Het exemplaar waarop de cache is geïmplementeerd, is door Azure bijgewerkt
    * Dit kan zijn voor redis-server updates of algemeen VM-onderhoud.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Welke Azure-cache aanbieding is geschikt voor mij?
> [!IMPORTANT]
> Na de [aankondiging](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)van vorig jaar zijn de Azure Managed cache service-en Azure in-Role cache-Services **buiten gebruik gesteld** op 30 november 2016. Onze aanbeveling is [Azure cache te gebruiken voor redis](https://azure.microsoft.com/services/cache/). Zie [migreren van Managed cache service naar Azure cache voor redis](cache-migrate-to-redis.md)voor informatie over het migreren van.
>
>

### <a name="azure-cache-for-redis"></a>Azure Cache voor Redis
Azure cache voor redis is algemeen beschikbaar in grootten tot 120 GB en heeft een SLA voor de beschik baarheid van 99,9%. De nieuwe [Premium-laag](cache-premium-tier-intro.md) biedt een grootte van maxi maal 1,2 TB en ondersteuning voor clustering, VNET en persistentie, met een SLA van 99,9%.

Azure cache voor redis biedt klanten de mogelijkheid om een beveiligde, toegewezen Azure-cache voor redis te gebruiken, die wordt beheerd door micro soft. Met deze aanbieding kunt u gebruikmaken van de uitgebreide functies en ecosystemen die worden geboden door redis, en betrouw bare hosting en controle van micro soft.

In tegens telling tot traditionele caches die alleen omgaan met sleutel-waardeparen, is redis populair voor de zeer hoogwaardige gegevens typen. Redis biedt ook ondersteuning voor het uitvoeren van atomische bewerkingen op deze typen, zoals het toevoegen aan een teken reeks. de waarde in een hash verhogen; pushen naar een lijst; Computing set, samen voeging en verschil; of het lid met de hoogste rang schikking in een gesorteerde set ophalen. Andere functies zijn onder meer ondersteuning voor trans acties, pub/sub, Lua scripting, sleutels met beperkte time-to-Live en configuratie-instellingen om ervoor te zorgen dat redis meer lijkt op een traditionele cache.

Een ander belang rijk aspect voor redis succes is het gezonde, levendige open source-ecosysteem dat erop is gebouwd. Dit wordt weer gegeven in de verschillende set redis-clients die beschikbaar zijn in meerdere talen. Met dit ecosysteem en een breed scala aan clients kan Azure cache voor redis worden gebruikt door bijna elke werk belasting die u in azure zou bouwen.

Voor meer informatie over het aan de slag gaan met Azure cache voor redis raadpleegt u [Azure cache gebruiken voor redis](cache-dotnet-how-to-use-azure-redis-cache.md) en [Azure cache voor redis-documentatie](index.yml).

### <a name="managed-cache-service"></a>Managed cache service
[Managed cache service is buiten gebruik gesteld op 30 november 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Zie [gearchiveerde Managed cache service documentatie](/previous-versions/azure/azure-services/dn386094(v=azure.100))voor informatie over het weer geven van gearchiveerde documentatie.

### <a name="in-role-cache"></a>In-Role Cache
[In-Role Cache is buiten gebruik gesteld op 30 november 2016.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Zie [gearchiveerde in-Role cache documentatie](/previous-versions/azure/azure-services/dn386103(v=azure.100))voor informatie over het weer geven van gearchiveerde documentatie.

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
