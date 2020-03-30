---
title: Veelgestelde vragen over Azure Cache voor Redis
description: Meer informatie over de antwoorden op veelgestelde vragen, patronen en aanbevolen procedures voor Azure Cache voor Redis
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/29/2019
ms.openlocfilehash: ddf7999153e9d9722e627d148b116750fe3aaecf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278712"
---
# <a name="azure-cache-for-redis-faq"></a>Veelgestelde vragen over Azure Cache voor Redis
Lees de antwoorden op veelgestelde vragen, patronen en aanbevolen procedures voor Azure Cache voor Redis.

## <a name="what-if-my-question-isnt-answered-here"></a>Wat als mijn vraag hier niet wordt beantwoord?
Als uw vraag hier niet wordt vermeld, laat het ons weten en wij helpen u een antwoord te vinden.

* U een vraag plaatsen in de opmerkingen aan het einde van deze veelgestelde vragen en contact opnemen met het Azure Cache-team en andere communityleden over dit artikel.
* Als u een breder publiek wilt bereiken, u een vraag plaatsen op het [MSDN-forum voor Azure-cache](https://social.msdn.microsoft.com/forums/azure/home?forum=azurecache) en contact opnemen met het Azure-cacheteam en andere leden van de community.
* Als u een functieaanvraag wilt indienen, u uw aanvragen en ideeën indienen bij [Azure Cache voor Redis User Voice.](https://feedback.azure.com/forums/169382-cache)
* U ons ook een e-mail sturen via [externe feedback van Azure Cache.](mailto:azurecache@microsoft.com)

## <a name="azure-cache-for-redis-basics"></a>Azure-cache voor de basisbeginselen van Redis
De veelgestelde vragen in deze sectie hebben betrekking op enkele basisprincipes van Azure Cache voor Redis.

* [Wat is Azure Cache voor Redis?](#what-is-azure-cache-for-redis)
* [Hoe kan ik aan de slag met Azure Cache voor Redis?](#how-can-i-get-started-with-azure-cache-for-redis)

De volgende veelgestelde vragen hebben betrekking op basisconcepten en vragen over Azure Cache voor Redis en worden beantwoord in een van de andere FAQ-secties.

* [Welke Azure-cache voor Redis-aanbieding en grootte moet ik gebruiken?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Welke Azure-cache voor Redis-clients kan ik gebruiken?](#what-azure-cache-for-redis-clients-can-i-use)
* [Is er een lokale emulator voor Azure Cache voor Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Hoe controleer ik de status en prestaties van mijn cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)

## <a name="planning-faqs"></a>Veelgestelde vragen plannen
* [Welke Azure-cache voor Redis-aanbieding en grootte moet ik gebruiken?](#what-azure-cache-for-redis-offering-and-size-should-i-use)
* [Azure-cache voor de prestaties van Redis](#azure-cache-for-redis-performance)
* [In welke regio moet ik mijn cache zoeken?](#in-what-region-should-i-locate-my-cache)
* [Hoe worden er kosten in rekening gebracht voor Azure Cache voor Redis?](#how-am-i-billed-for-azure-cache-for-redis)
* [Kan ik Azure Cache voor Redis gebruiken met Azure Government Cloud, Azure China Cloud of Microsoft Azure Germany?](#can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany)

## <a name="development-faqs"></a>Veelgestelde vragen over ontwikkeling
* [Wat doen de configuratieopties van StackExchange.Redis?](#what-do-the-stackexchangeredis-configuration-options-do)
* [Welke Azure-cache voor Redis-clients kan ik gebruiken?](#what-azure-cache-for-redis-clients-can-i-use)
* [Is er een lokale emulator voor Azure Cache voor Redis?](#is-there-a-local-emulator-for-azure-cache-for-redis)
* [Hoe kan ik Redis-opdrachten uitvoeren?](#how-can-i-run-redis-commands)
* [Waarom heeft Azure Cache voor Redis geen MSDN-klassebibliotheekverwijzing zoals sommige andere Azure-services?](#why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services)
* [Kan ik Azure Cache voor Redis gebruiken als PHP-sessiecache?](#can-i-use-azure-cache-for-redis-as-a-php-session-cache)
* [Wat zijn Redis databases?](#what-are-redis-databases)

## <a name="security-faqs"></a>Veelgestelde vragen over beveiliging
* [Wanneer moet ik de niet-SSL-poort inschakelen om verbinding te maken met Redis?](#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis)

## <a name="production-faqs"></a>Veelgestelde vragen over de productie
* [Wat zijn enkele best practices voor de productie?](#what-are-some-production-best-practices)
* [Wat zijn enkele van de overwegingen bij het gebruik van gemeenschappelijke Redis commando's?](#what-are-some-of-the-considerations-when-using-common-redis-commands)
* [Hoe kan ik de prestaties van mijn cache benchmarken en testen?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* [Belangrijke details over de groei van ThreadPool](#important-details-about-threadpool-growth)
* [Server GC inschakelen om meer doorvoer op de client te krijgen bij het gebruik van StackExchange.Redis](#enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis)
* [Prestatieoverwegingen rond verbindingen](#performance-considerations-around-connections)

## <a name="monitoring-and-troubleshooting-faqs"></a>Veelgestelde vragen over het bewaken en oplossen van problemen
De veelgestelde vragen in deze sectie hebben betrekking op veelvoorkomende vragen over het bewaken en oplossen van problemen. Zie [Azure Cache voor Redis](cache-how-to-monitor.md) en de verschillende probleemoplossingshandleidingen controleren voor meer informatie over het bewaken en oplossen van problemen met uw Azure-cache voor Redis.

* [Hoe controleer ik de status en prestaties van mijn cache?](#how-do-i-monitor-the-health-and-performance-of-my-cache)
* [Waarom zie ik time-outs?](#why-am-i-seeing-timeouts)
* [Waarom is de verbinding van mijn client losgekoppeld van de cache?](#why-was-my-client-disconnected-from-the-cache)

## <a name="prior-cache-offering-faqs"></a>Eerdere cache biedt veelgestelde vragen
* [Welke Azure Cache-aanbieding is geschikt voor mij?](#which-azure-cache-offering-is-right-for-me)

### <a name="what-is-azure-cache-for-redis"></a>Wat is Azure Cache voor Redis?
Azure Cache voor Redis is gebaseerd op de populaire open-source software [Redis.](https://redis.io/) Het geeft u toegang tot een beveiligde, speciale Azure-cache voor Redis, beheerd door Microsoft en toegankelijk vanuit elke toepassing binnen Azure. Zie de productpagina [Azure Cache for Redis](https://azure.microsoft.com/services/cache/) op Azure.com voor een gedetailleerder overzicht.

### <a name="how-can-i-get-started-with-azure-cache-for-redis"></a>Hoe kan ik aan de slag met Azure Cache voor Redis?
Er zijn verschillende manieren waarop u aan de slag met Azure Cache voor Redis.

* U kijken op een van onze tutorials beschikbaar voor [.NET,](cache-dotnet-how-to-use-azure-redis-cache.md) [ASP.NET](cache-web-app-howto.md), [Java](cache-java-get-started.md), [Node.js](cache-nodejs-get-started.md), en [Python](cache-python-get-started.md).
* U bekijken [hoe u krachtige apps bouwen met Microsoft Azure-cache voor Redis.](https://azure.microsoft.com/documentation/videos/how-to-build-high-performance-apps-using-microsoft-azure-cache/)
* U de clientdocumentatie voor de clients bekijken die overeenkomen met de ontwikkelingstaal van uw project om te zien hoe u Redis gebruiken. Er zijn veel Redis-clients die kunnen worden gebruikt met Azure Cache voor Redis. Zie [https://redis.io/clients](https://redis.io/clients)voor een lijst met Redis-clients .

Als u nog geen Azure-account hebt, u het als:

* [Gratis een Azure-account openen](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=redis_cache_hero). U ontvangt tegoed dat kan worden gebruikt om betaalde Azure-services te proberen. Zelfs nadat het tegoed is gebruikt, kunt u het account houden en de gratis Azure-services en -functies gebruiken.
* [Uw voordelen als Visual Studio-abonnee activeren](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=redis_cache_hero). Via uw MSDN-abonnement ontvangt u elke maand tegoeden die u voor betaalde Azure-services kunt gebruiken.

<a name="cache-size"></a>

### <a name="what-azure-cache-for-redis-offering-and-size-should-i-use"></a>Welke Azure-cache voor Redis-aanbieding en grootte moet ik gebruiken?
Elke Azure-cache voor Redis-aanbieding biedt verschillende niveaus van **grootte,** **bandbreedte,** **hoge beschikbaarheid**en **SLA-opties.**

De volgende overwegingen zijn overwegingen voor het kiezen van een cache-aanbod.

* **Geheugen**: De basic- en standaardniveaus bieden 250 MB – 53 GB. De Premium-laag biedt maximaal 1,2 TB (als cluster) of 120 GB (niet-geclusterd). Zie [Azure Cache for Redis Pricing voor](https://azure.microsoft.com/pricing/details/cache/)meer informatie .
* **Netwerkprestaties:** als u een workload hebt waarvoor een hoge doorvoer vereist is, biedt de Premium-laag meer bandbreedte in vergelijking met Standard of Basic. Ook binnen elke laag hebben grotere caches meer bandbreedte vanwege de onderliggende VM die de cache host. Zie de volgende [tabel voor](#cache-performance)meer informatie.
* **Doorvoer:** De Premium-laag biedt de maximaal beschikbare doorvoer. Als de cacheserver of -client de bandbreedtelimieten bereikt, ontvangt u mogelijk time-outs aan de clientzijde. Zie de volgende tabel voor meer informatie.
* **Hoge beschikbaarheid/SLA**: Azure Cache for Redis garandeert dat een Standard/Premium-cache ten minste 99,9% van de tijd beschikbaar is. Zie Azure Cache for [Redis Pricing voor](https://azure.microsoft.com/support/legal/sla/cache/v1_0/)meer informatie over onze SLA. De SLA dekt alleen de connectiviteit met de cacheeindpunten. De SLA heeft geen betrekking op bescherming tegen gegevensverlies. We raden u aan de functie voor gegevenspersistentie van Redis in de Premium-laag te gebruiken om de tolerantie tegen gegevensverlies te verhogen.
* **Redis Data Persistentie:** Met de Premium-laag u de cachegegevens in een Azure Storage-account blijven gebruiken. In een Basic/Standard-cache worden alle gegevens alleen in het geheugen opgeslagen. Onderliggende infrastructuurproblemen kunnen leiden tot mogelijk gegevensverlies. We raden u aan de functie voor gegevenspersistentie van Redis in de Premium-laag te gebruiken om de tolerantie tegen gegevensverlies te verhogen. Azure Cache for Redis biedt RDB- en AOF -opties (binnenkort beschikbaar) in Redis-persistentie. Zie [Persistentie configureren voor een Premium Azure-cache voor Redis voor](cache-how-to-premium-persistence.md)meer informatie.
* **Redis-cluster:** Als u caches van meer dan 120 GB wilt maken of om gegevens over meerdere Redis-knooppunten te scannen, u Redis-clustering gebruiken, die beschikbaar is in de premiumlaag. Elk knooppunt bestaat uit een primaire/replica cache paar voor hoge beschikbaarheid. Zie [Clustering configureren voor een Premium Azure-cache voor Redis voor](cache-how-to-premium-clustering.md)meer informatie.
* **Verbeterde beveiliging en netwerkisolatie:** De Implementatie van Azure Virtual Network (VNET) biedt verbeterde beveiliging en isolatie voor uw Azure-cache voor Redis, evenals subnetten, toegangsbeheerbeleid en andere functies om de toegang verder te beperken. Zie [Ondersteuning voor virtueel netwerk configureren voor een Premium Azure-cache voor Redis voor](cache-how-to-premium-vnet.md)meer informatie.
* **Redis configureren:** in zowel de standaard- als de Premium-lagen u Redis configureren voor Keyspace-meldingen.
* **Maximaal aantal clientverbindingen**: De Premium-laag biedt het maximum aantal clients dat verbinding kan maken met Redis, met een groter aantal verbindingen voor grotere caches. Clustering verhoogt het aantal beschikbare verbindingen voor een geclusterde cache niet. Zie [Azure Cache for Redis-prijzen voor](https://azure.microsoft.com/pricing/details/cache/)meer informatie.
* **Dedicated Core for Redis Server:** In de Premium-laag hebben alle cacheformaten een speciale kern voor Redis. In de basis/standaardlagen hebben de C1-grootte en hoger een speciale kern voor Redis-server.
* **Redis is single-threaded,** dus het hebben van meer dan twee cores biedt geen extra voordeel ten opzichte van het hebben van slechts twee cores, maar grotere VM-formaten hebben meestal meer bandbreedte dan kleinere formaten. Als de cacheserver of -client de bandbreedtelimieten bereikt, ontvangt u time-outs aan de clientzijde.
* **Prestatieverbeteringen:** caches in de Premium-laag worden geïmplementeerd op hardware met snellere processors, wat betere prestaties biedt in vergelijking met de laag Basic of Standard. Premium tier Caches hebben een hogere doorvoer en lagere latencies.

<a name="cache-performance"></a>

### <a name="azure-cache-for-redis-performance"></a>Azure-cache voor de prestaties van Redis
In de volgende tabel worden de maximale bandbreedtewaarden weergegeven `redis-benchmark.exe` die zijn waargenomen tijdens het testen van verschillende formaten standaard- en Premium-caches met behulp van een IaaS-vm ten opzichte van het Azure-cache voor Redis-eindpunt. Voor SSL-doorvoer wordt redis-benchmark gebruikt met stunnel om verbinding te maken met het Azure Cache voor Redis-eindpunt.

>[!NOTE] 
>Deze waarden zijn niet gegarandeerd en er is geen SLA voor deze nummers, maar moet typisch zijn. U moet uw eigen toepassing laden om de juiste cachegrootte voor uw toepassing te bepalen.
>Deze getallen kunnen veranderen naarmate we regelmatig nieuwere resultaten plaatsen.
>

Uit deze tabel kunnen we de volgende conclusies trekken:

* De doorvoer voor de caches met dezelfde grootte is hoger in de premiumlaag in vergelijking met de standaardlaag. Met een cache van 6 GB bedraagt de doorvoer van P1 bijvoorbeeld 180.000 aanvragen per seconde (RPS) in vergelijking met 100.000 RPS voor C3.
* Met Redis-clustering neemt de doorvoer lineair toe naarmate u het aantal shards (knooppunten) in het cluster verhoogt. Als u bijvoorbeeld een P4-cluster van 10 shards maakt, is de beschikbare doorvoer 400.000 * 10 = 4 miljoen RPS.
* De doorvoer voor grotere sleutelgroottes is hoger in de Premium-laag in vergelijking met de standaardlaag.

| Prijscategorie | Grootte | CPU-kernen | Beschikbare bandbreedte | 1-KB waardegrootte | 1-KB waardegrootte |
| --- | --- | --- | --- | --- | --- |
| **Standaardcacheformaten** | | |**Megabits per seconde (Mb/s) / Megabytes per seconde (MB/s)** |**Aanvragen per seconde (RPS) Niet-SSL** |**SSL aanvragen per seconde (RPS)** |
| C0 | 250 MB | Gedeeld | 100 / 12.5  |  15.000 |   7.500 |
| C1 |   1 GB | 1      | 500 / 62.5  |  38,000 |  20,720 |
| C2 | 2,5 GB | 2      | 500 / 62.5  |  41,000 |  37,000 |
| C3 |   6 GB | 4      | 1000 / 125  | 100.000 |  90,000 |
| C4 |  13 GB | 2      | 500 / 62.5  |  60.000 |  55,000 |
| C5 |  26 GB | 4      | 1,000 / 125 | 102,000 |  93,000 |
| C6 |  53 GB | 8      | 2,000 / 250 | 126,000 | 120,000 |
| **Premium-cacheformaten** | |**CPU-cores per shard** | **Megabits per seconde (Mb/s) / Megabytes per seconde (MB/s)** |**Aanvragen per seconde (RPS) Niet-SSL, per shard** |**SSL,aanvragen per seconde (RPS)** |
| P1 |   6 GB |  2 | 1,500 / 187.5 | 180,000 | 172,000 |
| P2 |  13 GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P3 |  26 GB |  4 | 3,000 / 375   | 350,000 | 341,000 |
| P4 |  53 GB |  8 | 6,000 / 750   | 400,000 | 373,000 |
| P5 | 120 GB | 20 | 6,000 / 750   | 400,000 | 373,000 |

Voor instructies over het instellen van een tunnel `redis-benchmark.exe`of het downloaden van de Redis-tools, zoals , zie de sectie [Hoe kan ik Redis-opdrachten uitvoeren?](#cache-commands)

<a name="cache-region"></a>

### <a name="in-what-region-should-i-locate-my-cache"></a>In welke regio moet ik mijn cache zoeken?
Zoek uw Azure-cache voor Redis in dezelfde regio als uw cacheclienttoepassing voor de beste prestaties en laagste latentie.

<a name="cache-billing"></a>

### <a name="how-am-i-billed-for-azure-cache-for-redis"></a>Hoe worden er kosten in rekening gebracht voor Azure Cache voor Redis?
Azure Cache for Redis-prijzen vindt [u hier.](https://azure.microsoft.com/pricing/details/cache/) Op de prijspagina worden prijzen vermeld als een uurtarief. Caches worden per minuut gefactureerd vanaf het moment dat de cache wordt gemaakt tot het moment dat een cache wordt verwijderd. Er is geen optie om de facturering van een cache te stoppen of te pauzeren.

### <a name="can-i-use-azure-cache-for-redis-with-azure-government-cloud-azure-china-cloud-or-microsoft-azure-germany"></a>Kan ik Azure Cache voor Redis gebruiken met Azure Government Cloud, Azure China Cloud of Microsoft Azure Germany?
Ja, Azure Cache voor Redis is beschikbaar in Azure Government Cloud, Azure China 21Vianet Cloud en Microsoft Azure Germany. De URL's voor het openen en beheren van Azure Cache voor Redis zijn verschillend in deze clouds in vergelijking met Azure Public Cloud.

| Cloud   | Dns-achtervoegsel voor Redis            |
|---------|---------------------------------|
| Public  | *.redis.cache.windows.net       |
| US Gov  | *.redis.cache.usgovcloudapi.net |
| Duitsland | *.redis.cache.cloudapi.de       |
| China   | *.redis.cache.chinacloudapi.cn  |

Zie de volgende koppelingen voor meer informatie over overwegingen bij het gebruik van Azure Cache voor Redis met andere clouds.

- [Azure Government Databases - Azure Cache voor Redis](../azure-government/documentation-government-services-database.md#azure-cache-for-redis)
- [Azure China 21Vianet Cloud - Azure Cache voor Redis](https://www.azure.cn/home/features/redis-cache/)
- [Microsoft Azure Duitsland](https://azure.microsoft.com/overview/clouds/germany/)

Zie [Hoe u verbinding maken met andere clouds - Azure Cache voor Redis voor Redis PowerShell](cache-how-to-manage-redis-cache-powershell.md#how-to-connect-to-other-clouds), voor informatie over het gebruik van Azure Cache voor Redis met PowerShell in Azure Government Cloud, Azure China 21Vianet Cloud en Microsoft Azure Germany.

<a name="cache-configuration"></a>

### <a name="what-do-the-stackexchangeredis-configuration-options-do"></a>Wat doen de configuratieopties van StackExchange.Redis?
StackExchange.Redis heeft vele opties. In dit gedeelte wordt gesproken over enkele van de gemeenschappelijke instellingen. Zie [StackExchange.Redis-configuratie](https://stackexchange.github.io/StackExchange.Redis/Configuration)voor meer gedetailleerde informatie over stackexchange.Redis-opties.

| ConfigurationOptions | Beschrijving | Aanbeveling |
| --- | --- | --- |
| AbortonConnectFail |Wanneer de verbinding is ingesteld op true, wordt er geen verbinding meer gemaakt na een netwerkstoring. |Stel in op false en laat StackExchange.Redis automatisch opnieuw verbinding maken. |
| ConnectRetry |Het aantal keren dat u verbindingspogingen herhaalt tijdens de eerste verbinding. |Zie de volgende notities voor richtlijnen. |
| ConnectTimeout |Time-out in ms voor connect-bewerkingen. |Zie de volgende notities voor richtlijnen. |

Meestal zijn de standaardwaarden van de client voldoende. U de opties afstemmen op basis van uw werkbelasting.

* **Nieuwe pogingen**
  * Voor ConnectRetry en ConnectTimeout is de algemene richtlijn om snel te mislukken en opnieuw te proberen. Deze richtlijnen zijn gebaseerd op uw werkbelasting en hoeveel tijd het gemiddeld kost voordat uw client een Redis-opdracht uitgeeft en een antwoord ontvangt.
  * Laat StackExchange.Redis automatisch opnieuw verbinding maken in plaats van de status van de verbinding te controleren en jezelf opnieuw te verbinden. **Vermijd het gebruik van de eigenschap ConnectionMultiplexer.IsConnected**.
  * Sneeuwballen - soms u lopen in een probleem waar je opnieuw proberen en de nieuwe pogingen sneeuwbal en nooit herstelt. Als er sneeuwbaleffect optreedt, moet u overwegen een exponentieel backoff retry-algoritme te gebruiken zoals beschreven in [Algemene richtlijnen opnieuw proberen](../best-practices-retry-general.md) die zijn gepubliceerd door de groep Microsoft Patterns & Practices.
  
* **Time-outwaarden**
  * Houd rekening met uw werkbelasting en stel de waarden dienovereenkomstig in. Als u grote waarden opslaat, stelt u de time-out in op een hogere waarde.
  * Stel `AbortOnConnectFail` in op false en laat StackExchange.Redis opnieuw verbinding maken voor u.
  * Gebruik één ConnectionMultiplexer-exemplaar voor de toepassing. U een LazyConnection gebruiken om één exemplaar te maken dat wordt geretourneerd door een eigenschap Connection, zoals weergegeven in [Verbinding met de cache met de klasse ConnectionMultiplexer.](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache)
  * Stel `ConnectionMultiplexer.ClientName` de eigenschap in op een unieke naam van een app-exemplaar voor diagnostische doeleinden.
  * Gebruik `ConnectionMultiplexer` meerdere exemplaren voor aangepaste workloads.
      * U dit model volgen als u een wisselende belasting in uw toepassing hebt. Bijvoorbeeld:
      * U een multiplexer voor het omgaan met grote sleutels.
      * U een multiplexer voor het omgaan met kleine sleutels.
      * U verschillende waarden instellen voor time-outs van verbindingen en logica opnieuw proberen voor elke ConnectionMultiplexer die u gebruikt.
      * Stel `ClientName` de eigenschap op elke multiplexer in om te helpen met diagnostische gegevens.
      * Deze richtlijnen kunnen leiden tot meer `ConnectionMultiplexer`gestroomlijnde latentie per .

### <a name="what-azure-cache-for-redis-clients-can-i-use"></a>Welke Azure-cache voor Redis-clients kan ik gebruiken?
Een van de grote dingen over Redis is dat er veel klanten ondersteunen veel verschillende ontwikkeling talen. Zie [Redis-clients](https://redis.io/clients)voor een actuele lijst met clients. Zie Azure Cache voor Redis en de artikelen van broers en zussen in de inhoudsopgave voor zelfstudies die verschillende talen en clients [bestrijken.](cache-dotnet-how-to-use-azure-redis-cache.md)

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]

<a name="cache-emulator"></a>

### <a name="is-there-a-local-emulator-for-azure-cache-for-redis"></a>Is er een lokale emulator voor Azure Cache voor Redis?
Er is geen lokale emulator voor Azure Cache voor Redis, maar u de MSOpenTech-versie van redis-server.exe uitvoeren vanuit de [Redis-opdrachtregelgereedschappen](https://github.com/MSOpenTech/redis/releases/) op uw lokale machine en er verbinding mee maken om een vergelijkbare ervaring te krijgen met een lokale cacheemulator, zoals in het volgende voorbeeld wordt weergegeven:

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


U optioneel een [redis.conf-bestand](https://redis.io/topics/config) configureren om beter overeen te komen met de [standaardcache-instellingen](cache-configure.md#default-redis-server-configuration) voor uw online Azure-cache voor Redis indien gewenst.

<a name="cache-commands"></a>

### <a name="how-can-i-run-redis-commands"></a>Hoe kan ik Redis-opdrachten uitvoeren?
U een van de opdrachten gebruiken die bij [Redis-opdrachten](https://redis.io/commands#) worden vermeld, behalve de opdrachten die worden vermeld bij [Redis-opdrachten die niet worden ondersteund in Azure-cache voor Redis.](cache-configure.md#redis-commands-not-supported-in-azure-cache-for-redis) U hebt verschillende opties om Redis-opdrachten uit te voeren.

* Als u een Standard- of Premium-cache hebt, u Redis-opdrachten uitvoeren met de [Redis-console.](cache-configure.md#redis-console) De Redis-console biedt een veilige manier om Redis-opdrachten uit te voeren in de Azure-portal.
* U ook de tools voor de opdrachtregel van Redis gebruiken. Voer de volgende stappen uit om ze te gebruiken:
* Download de [Redis-opdrachtregelgereedschappen](https://github.com/MSOpenTech/redis/releases/).
* Maak verbinding met `redis-cli.exe`de cache via . Geef het eindpunt van de cache door met de schakelaar -h en de sleutel met -a zoals in het volgende voorbeeld wordt weergegeven:
* `redis-cli -h <Azure Cache for Redis name>.redis.cache.windows.net -a <key>`

> [!NOTE]
> De opdrachtregelhulpprogramma's van Redis werken niet met de SSL-poort, maar u een hulpprogramma gebruiken om `stunnel` de hulpprogramma's veilig te verbinden met de SSL-poort door de aanwijzingen in het artikel Hoe u het [redis-opdrachtregelgereedschap moet gebruiken met Azure Cache voor het artikel van Redis te volgen.](https://docs.microsoft.com/azure/azure-cache-for-redis/cache-how-to-redis-cli-tool)
>
>

<a name="cache-reference"></a>

### <a name="why-doesnt-azure-cache-for-redis-have-an-msdn-class-library-reference-like-some-of-the-other-azure-services"></a>Waarom heeft Azure Cache voor Redis geen MSDN-klassebibliotheekverwijzing zoals sommige andere Azure-services?
Microsoft Azure Cache voor Redis is gebaseerd op de populaire open source Azure Cache voor Redis. Het kan worden benaderd door een breed scala van [Redis klanten](https://redis.io/clients) voor vele programmeertalen. Elke client heeft zijn eigen API die aanzet tot de Azure Cache voor Redis-instantie met behulp van [Redis-opdrachten.](https://redis.io/commands)

Omdat elke client anders is, is er niet één gecentraliseerde klassereferentie op MSDN en onderhoudt elke client zijn eigen referentiedocumentatie. Naast de referentiedocumentatie zijn er verschillende zelfstudies die laten zien hoe u aan de slag met Azure Cache voor Redis met behulp van verschillende talen en cacheclients. Zie Azure Cache [voor Redis gebruiken](cache-dotnet-how-to-use-azure-redis-cache.md) voor Redis en de artikelen van broer of zus in de inhoudsopgave om toegang te krijgen tot deze zelfstudies.

### <a name="can-i-use-azure-cache-for-redis-as-a-php-session-cache"></a>Kan ik Azure Cache voor Redis gebruiken als PHP-sessiecache?
Ja, als u Azure Cache voor Redis wilt gebruiken als een PHP-sessiecache, geeft u de verbindingstekenreeks op met uw Azure-cache voor de instantie Redis in `session.save_path`.

> [!IMPORTANT]
> Wanneer u Azure Cache voor Redis gebruikt als een PHP-sessiecache, moet u de beveiligingssleutel coderen die wordt gebruikt om verbinding te maken met de cache, zoals in het volgende voorbeeld wordt weergegeven:
>
> `session.save_path = "tcp://mycache.redis.cache.windows.net:6379?auth=<url encoded primary or secondary key here>";`
>
> Als de sleutel niet is gecodeerd met een URL, ontvangt u mogelijk een uitzondering met een bericht als:`Failed to parse session.save_path`
>
>

Zie [PHP Session handler](https://github.com/phpredis/phpredis#php-session-handler)voor meer informatie over het gebruik van Azure Cache voor Redis als PHP-sessiecache met de PhpRedis-client.

### <a name="what-are-redis-databases"></a>Wat zijn Redis databases?

Redis Databases zijn slechts een logische scheiding van gegevens binnen dezelfde Redis-instantie. Het cachegeheugen wordt gedeeld tussen alle databases en het werkelijke geheugenverbruik van een bepaalde database is afhankelijk van de sleutels/waarden die in die database zijn opgeslagen. Een C6-cache heeft bijvoorbeeld 53 GB geheugen en een P5 120 GB. U ervoor kiezen om alle 53 GB / 120 GB in één database te plaatsen of u deze opsplitsen tussen meerdere databases. 

> [!NOTE]
> Wanneer u een Premium Azure-cache voor Redis gebruikt met clustering ingeschakeld, is alleen database 0 beschikbaar. Deze beperking is een intrinsieke Beperking van Redis en is niet specifiek voor Azure Cache voor Redis. Zie [Moet ik wijzigingen aanbrengen in mijn clienttoepassing om clustering te gebruiken voor](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering) meer informatie?
> 
> 


<a name="cache-ssl"></a>

### <a name="when-should-i-enable-the-non-ssl-port-for-connecting-to-redis"></a>Wanneer moet ik de niet-SSL-poort inschakelen om verbinding te maken met Redis?
Redis-server biedt geen ondersteuning voor SSL, maar Azure Cache voor Redis wel. Als u verbinding maakt met Azure Cache voor Redis en uw client SSL ondersteunt, zoals StackExchange.Redis, moet u SSL gebruiken.

>[!NOTE]
>De niet-SSL-poort is standaard uitgeschakeld voor nieuwe Azure-cache voor Redis-exemplaren. Als uw client geen SSL ondersteunt, moet u de niet-SSL-poort inschakelen door de aanwijzingen te volgen in de sectie [Access-poorten](cache-configure.md#access-ports) van het artikel [Een cache configureren in Azure Cache voor Redis.](cache-configure.md)
>
>

Redis-hulpprogramma's zoals `redis-cli` werken niet met de SSL-poort, `stunnel` maar u een hulpprogramma gebruiken om de hulpprogramma's veilig te verbinden met de SSL-poort door de aanwijzingen te volgen in de [ASP.NET Session State Provider voor Redis Preview Release-blogpost.](https://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx)

Zie De sectie Hoe kan ik [Redis-opdrachten uitvoeren voor](#cache-commands) instructies voor het downloaden van de Redis-tools.

### <a name="what-are-some-production-best-practices"></a>Wat zijn enkele best practices voor de productie?
* [StackExchange.Redis best practices](#stackexchangeredis-best-practices)
* [Configuratie en concepten](#configuration-and-concepts)
* [Prestaties testen](#performance-testing)

#### <a name="stackexchangeredis-best-practices"></a>StackExchange.Redis best practices
* Stel `AbortConnect` in op false en laat de ConnectionMultiplexer automatisch opnieuw verbinding maken. [Zie hier voor meer informatie](https://gist.github.com/JonCole/36ba6f60c274e89014dd#file-se-redis-setabortconnecttofalse-md).
* De ConnectionMultiplexer opnieuw gebruiken - maak geen nieuwe voor elke aanvraag. Het `Lazy<ConnectionMultiplexer>` [patroon hier wordt](cache-dotnet-how-to-use-azure-redis-cache.md#connect-to-the-cache) aanbevolen.
* Redis werkt het beste met kleinere waarden, dus overweeg om grotere gegevens in meerdere sleutels te hakken. In [deze Redis discussie](https://groups.google.com/forum/#!searchin/redis-db/size/redis-db/n7aa2A4DZDs/3OeEPHSQBAAJ), 100 kb wordt beschouwd als groot. Lees [dit artikel](https://gist.github.com/JonCole/db0e90bedeb3fc4823c2#large-requestresponse-size) voor een voorbeeld probleem dat kan worden veroorzaakt door grote waarden.
* Configureer de [ThreadPool-instellingen](#important-details-about-threadpool-growth) om time-outs te voorkomen.
* Gebruik ten minste de standaard connectTimeout van 5 seconden. Dit interval geeft StackExchange.Redis voldoende tijd om de verbinding te herstellen in het geval van een netwerkblip.
* Wees je bewust van de prestatiekosten die verbonden zijn aan verschillende bewerkingen die u uitvoert. De `KEYS` opdracht is bijvoorbeeld een O(n)-bewerking en moet worden vermeden. De [redis.io site](https://redis.io/commands/) heeft details rond de tijdscomplexiteit voor elke bewerking die wordt ondersteund. Klik op elke opdracht om de complexiteit voor elke bewerking te zien.

#### <a name="configuration-and-concepts"></a>Configuratie en concepten
* Gebruik standaard- of premiumlaag voor productiesystemen. De laag Basic is een systeem met één knooppunt zonder gegevensreplicatie en zonder SLA. Gebruik minimaal een C1-cache. C0-caches worden meestal gebruikt voor eenvoudige dev/testscenario's.
* Vergeet niet dat Redis een **in-memory** data store is. Lees [dit artikel](https://gist.github.com/JonCole/b6354d92a2d51c141490f10142884ea4#file-whathappenedtomydatainredis-md) zodat u op de hoogte bent van scenario's waarin gegevensverlies kan optreden.
* Ontwikkel uw systeem zodanig dat het kan omgaan met verbinding blips [als gevolg van patchen en failover](https://gist.github.com/JonCole/317fe03805d5802e31cfa37e646e419d#file-azureredis-patchingexplained-md).

#### <a name="performance-testing"></a>Prestaties testen
* Begin met `redis-benchmark.exe` het gebruik van een gevoel voor mogelijke doorvoer te krijgen voor het schrijven van uw eigen perf tests. Omdat `redis-benchmark` ssl niet wordt ondersteund, moet u [de niet-SSL-poort inschakelen via de Azure-portal](cache-configure.md#access-ports) voordat u de test uitvoert. Zie Bijvoorbeeld [Hoe kan ik de prestaties van mijn cache benchmarken en testen?](#how-can-i-benchmark-and-test-the-performance-of-my-cache)
* De client-VM die wordt gebruikt voor het testen moet zich in dezelfde regio bevinden als uw Azure-cache voor bijvoorbeeld Redis.
* We raden u aan dv2 VM-serie voor uw klant te gebruiken, omdat ze betere hardware hebben en de beste resultaten moeten geven.
* Zorg ervoor dat uw client-VM die u kiest, minstens evenveel reken- en bandbreedtemogelijkheden heeft als de cache die u test.
* Schakel VRSS in op de clientmachine als u windows gebruikt. [Zie hier voor meer informatie](https://technet.microsoft.com/library/dn383582.aspx).
* Premium tier Redis-exemplaren hebben een betere netwerklatentie en doorvoer omdat ze worden uitgevoerd op betere hardware voor zowel CPU als netwerk.

<a name="cache-redis-commands"></a>

### <a name="what-are-some-of-the-considerations-when-using-common-redis-commands"></a>Wat zijn enkele van de overwegingen bij het gebruik van gemeenschappelijke Redis commando's?

* Vermijd het gebruik van bepaalde Redis-opdrachten die lang duren, tenzij u de impact van deze opdrachten volledig begrijpt. Voer bijvoorbeeld de opdracht [TOETSEN](https://redis.io/commands/keys) niet uit in productie. Afhankelijk van het aantal sleutels kan het lang duren voordat het is teruggegeven. Redis is een single-threaded server en het verwerkt opdrachten een voor een. Als u andere opdrachten na KEYS hebt uitgegeven, worden deze pas verwerkt als Redis de opdracht SLEUTELS verwerkt. De [redis.io site](https://redis.io/commands/) heeft details rond de tijdscomplexiteit voor elke bewerking die wordt ondersteund. Klik op elke opdracht om de complexiteit voor elke bewerking te zien.
* Sleutelgroottes - moet ik kleine sleutel/waarden of grote sleutel/waarden gebruiken? Het hangt af van het scenario. Als voor uw scenario grotere toetsen nodig zijn, u de Verbindingstimeout aanpassen en vervolgens de waarden opnieuw proberen en de logica voor nieuwe try aanpassen. Vanuit het oogpunt van de Redis-server geven kleinere waarden betere prestaties.
* Deze overwegingen betekenen niet dat u grotere waarden niet opslaan in Redis; u moet zich bewust zijn van de volgende overwegingen. Latencies zullen hoger zijn. Als u één set gegevens hebt die groter is en een die kleiner is, u meerdere ConnectionMultiplexer-exemplaren gebruiken, elk geconfigureerd met een andere set time-out- en probeerwaarden opnieuw, zoals beschreven in de vorige [sectie Wat doen de configuratieopties van StackExchange.Redis.](#cache-configuration)

<a name="cache-benchmarking"></a>

### <a name="how-can-i-benchmark-and-test-the-performance-of-my-cache"></a>Hoe kan ik de prestaties van mijn cache benchmarken en testen?
* [Schakel de diagnostische gegevens van de cache in](cache-how-to-monitor.md#enable-cache-diagnostics), zodat u de status van de cache kunt [bewaken](cache-how-to-monitor.md). U de statistieken in de Azure-portal bekijken en u ze ook [downloaden en bekijken](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) met behulp van de hulpprogramma's van uw keuze.
* U redis-benchmark.exe gebruiken om uw Redis-server te laden.
* Controleer of de load testing-client en de Azure-cache voor Redis zich in dezelfde regio bevinden.
* Gebruik redis-cli.exe en controleer de cache met de opdracht INFO.
* Als uw belasting een hoge geheugenfragmentatie veroorzaakt, moet u worden opgeschaald naar een grotere cachegrootte.
* Zie De sectie Hoe kan ik [Redis-opdrachten uitvoeren voor](#cache-commands) instructies voor het downloaden van de Redis-tools.

De volgende opdrachten geven een voorbeeld van het gebruik van redis-benchmark.exe. Voer deze opdrachten uit van een vm in dezelfde regio als uw cache voor nauwkeurige resultaten.

* Test Pipelined SET-aanvragen met behulp van een 1k-payload

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t SET -n 1000000 -d 1024 -P 50`
* Test Pipelined GET-aanvragen met behulp van een 1k-payload.
  OPMERKING: Voer de bovenstaande SET-test uit om cache in te vullen

  `redis-benchmark.exe -h **yourcache**.redis.cache.windows.net -a **yourAccesskey** -t GET -n 1000000 -d 1024 -P 50`

<a name="threadpool"></a>

### <a name="important-details-about-threadpool-growth"></a>Belangrijke details over de groei van ThreadPool
De CLR ThreadPool heeft twee soorten threads - "Worker" en "I/O Completion Port" (IOCP) threads.

* Worker threads worden gebruikt voor `Task.Run(…)`zaken `ThreadPool.QueueUserWorkItem(…)` als het verwerken van de , of methoden. Deze draden worden ook gebruikt door verschillende componenten in de CLR wanneer het werk moet gebeuren op een achtergrond draad.
* IOCP-threads worden gebruikt wanneer asynchrone IO plaatsvindt, zoals bij het lezen van het netwerk.

De thread pool biedt nieuwe worker threads of I/O completion threads on demand (zonder beperking) totdat deze de instelling 'Minimum' voor elk type thread bereikt. Standaard is het minimumaantal threads ingesteld op het aantal processors op een systeem.

Zodra het aantal bestaande (drukke) threads het "minimum" aantal threads raakt, zal de ThreadPool de snelheid beperken waarmee het nieuwe draden injecteert tot één draad per 500 milliseconden. Typisch, als uw systeem krijgt een uitbarsting van het werk nodig een IOCP draad, zal het verwerken dat snel werken. Echter, als de uitbarsting van het werk is meer dan de geconfigureerde "Minimum" instelling, zal er enige vertraging in de verwerking van een deel van het werk als de ThreadPool wacht op een van de twee dingen te gebeuren.

1. Een bestaande thread wordt vrij om het werk te verwerken.
2. Geen bestaande draad wordt gratis voor 500 ms, dus een nieuwe draad wordt gemaakt.

In principe betekent dit dat wanneer het aantal drukke threads groter is dan Min threads, u waarschijnlijk een vertraging van 500 ms betaalt voordat het netwerkverkeer door de toepassing wordt verwerkt. Ook is het belangrijk op te merken dat wanneer een bestaande draad blijft stationair voor langer dan 15 seconden (op basis van wat ik me herinner), zal het worden opgeruimd en deze cyclus van groei en krimp kan herhalen.

Als we kijken naar een voorbeeld foutmelding van StackExchange.Redis (build 1.0.450 of hoger), zult u zien dat het nu wordt afgedrukt ThreadPool statistieken (zie IOCP en WERKNEMER details hieronder).

    System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive,
    queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0,
    IOCP: (Busy=6,Free=994,Min=4,Max=1000),
    WORKER: (Busy=3,Free=997,Min=4,Max=1000)

In het vorige voorbeeld u zien dat er voor IOCP-thread zes drukke threads zijn en dat het systeem is geconfigureerd om vier minimale threads toe te staan. In dit geval zou de klant waarschijnlijk twee vertragingen van 500 ms hebben gezien, omdat 6 > 4.

Houd er rekening mee dat StackExchange.Redis time-outs kan raken als de groei van IOCP- of WORKER-threads wordt beperkt.

### <a name="recommendation"></a>Aanbeveling

Gezien deze informatie raden we klanten ten zeerste aan de minimale configuratiewaarde voor IOCP- en WORKER-threads in te stellen op iets dat groter is dan de standaardwaarde. We kunnen geen one-size-fits-all richtlijnen geven over wat deze waarde zou moeten zijn, omdat de juiste waarde voor een toepassing waarschijnlijk te hoog of te laag zal zijn voor een andere toepassing. Deze instelling kan ook van invloed zijn op de prestaties van andere delen van gecompliceerde toepassingen, dus elke klant moet deze instelling afstemmen op hun specifieke behoeften. Een goede startplaats is 200 of 300, dan testen en tweaken als dat nodig is.

Deze instelling configureren:

* We raden u aan deze instelling programmatisch te wijzigen met `global.asax.cs`behulp van de [threadpool.setminthreads (...)-methode](/dotnet/api/system.threading.threadpool.setminthreads#System_Threading_ThreadPool_SetMinThreads_System_Int32_System_Int32_) in . Bijvoorbeeld:

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
  > De waarde die door deze methode wordt opgegeven, is een globale instelling die van invloed is op het hele AppDomain. Als u bijvoorbeeld een 4-core machine hebt en *minWorkerThreads* en *minIoThreads* tijdens run-time op 50-apparaten per CPU wilt instellen, gebruikt u **ThreadPool.SetMinThreads(200, 200)**.

* Het is ook mogelijk om de minimale threads-instelling op te geven met `<processModel>` behulp van `Machine.config`de [ *minIoThreads-* of *minWorkerThreads-configuratie-instelling* ](https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx) onder het configuratie-element in , meestal gelegen op `%SystemRoot%\Microsoft.NET\Framework\[versionNumber]\CONFIG\`. **Het instellen van het aantal minimale threads op deze manier wordt over het algemeen niet aanbevolen, omdat het een systeembrede instelling is.**

  > [!NOTE]
  > De waarde die in dit configuratie-element is opgegeven, is een *instelling per kern.* Als u bijvoorbeeld een 4-core machine hebt en wilt dat uw *minIoThreads-instelling* `<processModel minIoThreads="50"/>`200 is tijdens runtime, gebruikt u .
  >

<a name="server-gc"></a>

### <a name="enable-server-gc-to-get-more-throughput-on-the-client-when-using-stackexchangeredis"></a>Server GC inschakelen om meer doorvoer op de client te krijgen bij het gebruik van StackExchange.Redis
Het inschakelen van server GC kan de client optimaliseren en betere prestaties en doorvoer bieden bij gebruik van StackExchange.Redis. Zie de volgende artikelen voor meer informatie over server GC en hoe u deze inschakelen:

* [Server GC inschakelen](/dotnet/framework/configure-apps/file-schema/runtime/gcserver-element)
* [Fundamenten van garbage collection](/dotnet/standard/garbage-collection/fundamentals)
* [Garbage Collection en Prestaties](/dotnet/standard/garbage-collection/performance)


### <a name="performance-considerations-around-connections"></a>Prestatieoverwegingen rond verbindingen

Elke prijscategorie heeft verschillende limieten voor clientverbindingen, geheugen en bandbreedte. Terwijl elke grootte van de cache *tot* een bepaald aantal verbindingen mogelijk maakt, heeft elke verbinding met Redis overhead die eraan is gekoppeld. Een voorbeeld van dergelijke overhead zou cpu- en geheugengebruik zijn als gevolg van TLS/SSL-versleuteling. De maximale verbindingslimiet voor een bepaalde cachegrootte gaat uit van een licht geladen cache. Als de belasting van verbindingsoverhead *plus* belasting van clientbewerkingen de capaciteit voor het systeem overschrijdt, kan de cache capaciteitsproblemen ondervinden, zelfs als u de verbindingslimiet voor de huidige cachegrootte niet hebt overschreden.

Zie [Azure Cache voor Redis-prijzen voor](https://azure.microsoft.com/pricing/details/cache/)meer informatie over de verschillende verbindingslimieten voor elke laag. Zie [Standaardconfiguratie van de Redis-server](cache-configure.md#default-redis-server-configuration)voor meer informatie over verbindingen en andere standaardconfiguraties.

<a name="cache-monitor"></a>

### <a name="how-do-i-monitor-the-health-and-performance-of-my-cache"></a>Hoe controleer ik de status en prestaties van mijn cache?
Microsoft Azure Cache voor Redis-exemplaren kan worden gecontroleerd in de [Azure-portal.](https://portal.azure.com) U statistieken bekijken, metrische gegevensgrafieken vastmaken aan het startbord, de datum- en tijdsreeks van bewakingsdiagrammen aanpassen, statistieken uit de grafieken toevoegen en verwijderen en waarschuwingen instellen wanneer aan bepaalde voorwaarden is voldaan. Zie [Azure Cache voor Redis controleren voor](cache-how-to-monitor.md)meer informatie.

Het menu Azure Cache for Redis **Resource** bevat ook verschillende hulpprogramma's voor het bewaken en oplossen van problemen met uw caches.

* **Diagnose en het oplossen van problemen** biedt informatie over veelvoorkomende problemen en strategieën voor het oplossen van deze problemen.
* **Resourcestatus** houdt uw resource in de gaten en geeft aan of deze wordt uitgevoerd zoals verwacht. Zie overzicht van azure [resource-status](../resource-health/resource-health-overview.md)voor meer informatie over de statusservice azure resource .
* **Nieuwe ondersteuningsaanvraag** biedt opties om een ondersteuningsverzoek voor uw cache te openen.

Met deze hulpprogramma's u de status van uw Azure-cache voor Redis-exemplaren controleren en u uw caching-toepassingen beheren. Zie het gedeelte 'Ondersteuning & probleemoplossingsinstellingen' van [Azure Cache configureren voor Redis](cache-configure.md)voor meer informatie.

<a name="cache-timeouts"></a>

### <a name="why-am-i-seeing-timeouts"></a>Waarom zie ik time-outs?
Time-outs gebeuren in de klant die u gebruikt om te praten met Redis. Wanneer een opdracht naar de Redis-server wordt verzonden, wordt de opdracht in de wachtrij geplaatst en pakt de Redis-server uiteindelijk de opdracht op en voert deze uit. De client kan echter een time-out tijdens dit proces en als het doet een uitzondering wordt verhoogd aan de belzijde. Zie problemen met het oplossen [van time-outs aan de clientzijde](cache-troubleshoot-client.md) en uitzonderingen op [de time-out van StackExchange.Redis](cache-troubleshoot-timeouts.md#stackexchangeredis-timeout-exceptions)voor meer informatie over het oplossen van time-outproblemen.

<a name="cache-disconnect"></a>

### <a name="why-was-my-client-disconnected-from-the-cache"></a>Waarom is de verbinding van mijn client losgekoppeld van de cache?
De volgende zijn enkele veel voorkomende redenen voor een cache verbinding te verbreken.

* Oorzaken aan de klantzijde
  * De clienttoepassing is opnieuw geïmplementeerd.
  * De clienttoepassing heeft een schalingbewerking uitgevoerd.
    * In het geval van Cloud Services of Web Apps kan dit te wijten zijn aan automatisch schalen.
  * De netwerklaag aan de clientzijde is gewijzigd.
  * Er zijn tijdelijke fouten opgetreden in de client of in de netwerkknooppunten tussen de client en de server.
  * De bandbreedtedrempellimieten zijn bereikt.
  * CPU-gebonden bewerkingen duurdete te lang om te voltooien.
* Server-side oorzaken
  * Op het standaardcacheaanbod heeft de Azure Cache for Redis-service een fail-over gestart van het primaire knooppunt naar het secundaire knooppunt.
  * Azure patchte de instantie waarin de cache is geïmplementeerd
    * Dit kan zijn voor Redis-serverupdates of algemeen VM-onderhoud.

### <a name="which-azure-cache-offering-is-right-for-me"></a>Welke Azure Cache-aanbieding is geschikt voor mij?
> [!IMPORTANT]
> Zoals de [aankondiging](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)van vorig jaar , zijn Azure Managed Cache Service en Azure In-Role Cache service op 30 november 2016 **uitgeschakeld.** Onze aanbeveling is om Azure Cache te gebruiken [voor Redis.](https://azure.microsoft.com/services/cache/) Zie [Migreren van Managed Cache Service naar Azure Cache voor Redis voor](cache-migrate-to-redis.md)informatie over migreren.
>
>

### <a name="azure-cache-for-redis"></a>Azure Cache voor Redis
Azure Cache voor Redis is over het algemeen beschikbaar in formaten tot 120 GB en heeft een beschikbaarheid SLA van 99,9%. De nieuwe [premium laag](cache-premium-tier-intro.md) biedt maten tot 1,2 TB en ondersteuning voor clustering, VNET en persistentie, met een SLA van 99,9%.

Azure Cache voor Redis biedt klanten de mogelijkheid om een veilige, speciale Azure-cache voor Redis te gebruiken, beheerd door Microsoft. Met deze aanbieding u gebruikmaken van de uitgebreide functieset en het ecosysteem van Redis en betrouwbare hosting en monitoring van Microsoft.

In tegenstelling tot traditionele caches die alleen betrekking hebben op sleutel-waarde paren, Redis is populair om zijn zeer performante gegevenstypen. Redis ondersteunt ook het uitvoeren van atoombewerkingen op deze typen, zoals het toevoegen aan een tekenreeks; het verhogen van de waarde in een hash; duwen naar een lijst; computerset kruispunt, unie en verschil; of het verkrijgen van het lid met de hoogste ranking in een gesorteerde set. Andere functies zijn ondersteuning voor transacties, pub/sub, Lua-scripting, sleutels met een beperkte time-to-live en configuratie-instellingen om Redis zich meer te laten gedragen als een traditionele cache.

Een ander belangrijk aspect van het succes van Redis is het gezonde, levendige open-source ecosysteem eromheen. Dit komt tot uiting in de diverse set Van Redis-clients die beschikbaar zijn in meerdere talen. Dit ecosysteem en een breed scala aan clients maken het mogelijk azure cache voor Redis te gebruiken door bijna elke werkbelasting die u zou bouwen in Azure.

Zie [Azure Cache gebruiken voor Redis](cache-dotnet-how-to-use-azure-redis-cache.md) en Azure Cache voor [Redis-documentatie voor](index.yml)meer informatie over aan de slag met Azure Cache voor Redis.

### <a name="managed-cache-service"></a>Managed Cache-service
[Managed Cache-service is op 30 november 2016 van de dienst uitgeschakeld.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Zie [Gearchiveerde beheerde cacheservicedocumentatie](/previous-versions/azure/azure-services/dn386094(v=azure.100))voor het bekijken van gearchiveerde documentatie .

### <a name="in-role-cache"></a>Cache in rollen
[In-Role Cache is op 30 november 2016 met pensioen gegaan.](https://azure.microsoft.com/blog/azure-managed-cache-and-in-role-cache-services-to-be-retired-on-11-30-2016/)

Zie [Gearchiveerde cachedocumentatie in de rol .](/previous-versions/azure/azure-services/dn386103(v=azure.100))

["minIoThreads" configuration setting]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx
