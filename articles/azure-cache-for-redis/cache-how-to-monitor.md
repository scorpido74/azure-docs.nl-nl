---
title: Azure-cache controleren op Redis
description: Meer informatie over het bewaken van de status en prestaties van uw Azure-cache voor Redis-exemplaren
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 07/13/2017
ms.openlocfilehash: 86cbeddba699e89ce1127dbac72dac81dcc41449
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "76547486"
---
# <a name="how-to-monitor-azure-cache-for-redis"></a>Azure-cache controleren op Redis

Azure Cache voor Redis gebruikt [Azure Monitor](../azure-monitor/index.yml) om verschillende opties te bieden voor het bewaken van uw cache-exemplaren. U statistieken bekijken, metrische gegevensgrafieken vastmaken aan het startbord, de datum- en tijdsreeks van bewakingsdiagrammen aanpassen, statistieken uit de grafieken toevoegen en verwijderen en waarschuwingen instellen wanneer aan bepaalde voorwaarden is voldaan. Met deze hulpprogramma's u de status van uw Azure-cache voor Redis-exemplaren controleren en u uw caching-toepassingen beheren.

Statistieken voor Azure Cache voor Redis-exemplaren worden verzameld met de opdracht [Redis INFO](https://redis.io/commands/info) ongeveer twee keer per minuut en automatisch gedurende 30 dagen opgeslagen (zie [Cachestatistieken exporteren](#export-cache-metrics) om een ander bewaarbeleid te configureren) zodat ze kunnen worden weergegeven in de metrische grafieken en geëvalueerd door waarschuwingsregels. Zie [Beschikbare statistieken en rapportageintervallen](#available-metrics-and-reporting-intervals)voor meer informatie over de verschillende INFO-waarden die voor elke cachestatistiek worden gebruikt.

<a name="view-cache-metrics"></a>

Als u cachestatistieken wilt weergeven, [bladert u](cache-configure.md#configure-azure-cache-for-redis-settings) naar uw cache-instantie in de [Azure-portal.](https://portal.azure.com)  Azure Cache for Redis biedt een aantal ingebouwde grafieken op het **overzichtsblad** en het **Blade van de Redis-statistieken.** Elke grafiek kan worden aangepast door statistieken toe te voegen of te verwijderen en het rapportageinterval te wijzigen.

![Redis-statistieken](./media/cache-how-to-monitor/redis-cache-redis-metrics-blade.png)

## <a name="view-pre-configured-metrics-charts"></a>Vooraf geconfigureerde metrische grafieken weergeven

Het **overzichtsblad** heeft de volgende vooraf geconfigureerde controlediagrammen.

* [Grafieken controleren](#monitoring-charts)
* [Gebruiksdiagrammen](#usage-charts)

### <a name="monitoring-charts"></a>Grafieken controleren

De sectie **Controle** in het **overzichtsblad** heeft **de grafieken Hits en Misses,** Gets and **Sets,** **Connections**en **Total Commands.**

![Grafieken controleren](./media/cache-how-to-monitor/redis-cache-monitoring-part.png)

### <a name="usage-charts"></a>Gebruiksdiagrammen

De sectie **Gebruik** in het **overzichtsblad** bevat **De belasting van Redis-server,** **geheugengebruik,** **netwerkbandbreedte**en **CPU-gebruiksdiagrammen** en geeft ook de **prijslaag** voor de cacheinstantie weer.

![Gebruiksdiagrammen](./media/cache-how-to-monitor/redis-cache-usage-part.png)

De **prijscategorie** geeft de prijslaag voor cache weer en kan worden gebruikt om de cache te [schalen](cache-how-to-scale.md) naar een andere prijscategorie.

## <a name="view-metrics-with-azure-monitor"></a>Statistieken weergeven met Azure-monitor

Als u Redis-statistieken wilt weergeven en aangepaste grafieken wilt maken met Azure Monitor, klikt u op **Metrische gegevens** in het **menu Resource**en past u uw grafiek aan met behulp van de gewenste statistieken, rapportage-interval, grafiektype en meer.

![Redis-statistieken](./media/cache-how-to-monitor/redis-cache-monitor.png)

Zie Overzicht van statistieken in Microsoft [Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md)voor meer informatie over het werken met metrische gegevens met Azure.

<a name="how-to-view-metrics-and-customize-chart"></a>
<a name="enable-cache-diagnostics"></a>
## <a name="export-cache-metrics"></a>Cachestatistieken exporteren

Cachestatistieken in Azure Monitor worden standaard [30 dagen opgeslagen](../azure-monitor/platform/data-platform-metrics.md) en vervolgens verwijderd. Als u uw cachestatistieken langer dan 30 dagen wilt behouden, u [een opslagaccount aanwijzen](../azure-monitor/platform/archive-diagnostic-logs.md) en een **bewaarbeleid (dagen)** opgeven voor uw cachestatistieken. 

Ga als lid van het opslagaccount naar uw cachestatistieken:

1. Selecteer op de pagina **Azure Cache for Redis** onder de kop **Controle** de optie **Diagnostische gegevens**.
2. Selecteer **+ Diagnostische instelling toevoegen**.
3. Geef de instellingen een naam.
4. Schakel **Archiveren naar een opslagaccount** in. Er worden normale gegevenstarieven in rekening gebracht voor opslag en transacties wanneer u diagnoses naar een opslagaccount verzendt.
4. Selecteer **Configureren** om het opslagaccount te kiezen waarin de cachestatistieken moeten worden opgeslagen.
5. Schakel onder de **statistiek**tabelkop aan naast de regelitems die u wilt opslaan, zoals **AllMetrics**. Geef een **beleid voor behoud (dagen)** op. De maximale dagenretentie die u opgeven is **365 dagen.** Als u de metrische gegevens echter voor altijd wilt behouden, stelt u **Retentie (dagen)** in op **0**.
6. Klik op **Opslaan**.


![Redis diagnostiek](./media/cache-how-to-monitor/redis-cache-diagnostics.png)

>[!NOTE]
>Naast het archiveren van uw cachestatistieken naar opslag, u ze ook [streamen naar een gebeurtenishub of ze naar Azure Monitor-logboeken verzenden.](../azure-monitor/platform/rest-api-walkthrough.md#retrieve-metric-values)
>

Als u toegang wilt krijgen tot uw statistieken, u ze bekijken in de Azure-portal zoals eerder beschreven in dit artikel, en u ze ook openen met de [API Azure Monitor Metrics REST.](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)

> [!NOTE]
> Als u opslagaccounts wijzigt, blijven de gegevens in het eerder geconfigureerde opslagaccount beschikbaar om te downloaden, maar worden deze niet weergegeven in de Azure-portal.  
> 

## <a name="available-metrics-and-reporting-intervals"></a>Beschikbare statistieken en rapportageintervallen

Cachestatistieken worden gerapporteerd met behulp van verschillende rapportageintervallen, waaronder **Afgelopen uur**, **Vandaag**, **Afgelopen week**en **Aangepast**. In **het metrische** blad voor elke grafiek met statistieken worden de gemiddelde, minimale en maximale waarden voor elke statistiek in de grafiek weergegeven en worden in sommige statistieken een totaal voor het rapportageinterval weergegeven. 

Elke statistiek bevat twee versies. Eén statistiek meet de prestaties voor de hele cache en voor caches die `(Shard 0-9)` [clustering](cache-how-to-premium-clustering.md)gebruiken, een tweede versie van de statistiek die in de naam de prestaties voor één shard in een cache bevat. Bijvoorbeeld als een cache vier scherven `Cache Hits` heeft, is het totale aantal `Cache Hits (Shard 3)` hits voor de hele cache, en is slechts de hits voor die scherf van de cache.

> [!NOTE]
> Zelfs wanneer de cache niet is inactief zonder verbonden actieve clienttoepassingen, ziet u mogelijk bepaalde cacheactiviteit, zoals verbonden clients, geheugengebruik en bewerkingen die worden uitgevoerd. Deze activiteit is normaal tijdens de werking van een Azure-cache voor bijvoorbeeld Redis.
> 
> 

| Gegevens | Beschrijving |
| --- | --- |
| Cache Hits |Het aantal geslaagde sleutelopzoekingen tijdens het opgegeven rapportageinterval. Dit nummer `keyspace_hits` wordt toegewezen aan van de opdracht Redis [INFO.](https://redis.io/commands/info) |
| Cachelatentie (voorbeeld) | De latentie van de berekende cache is gebaseerd op de internodelatentie van de cache. Deze statistiek wordt gemeten in microseconden en `Avg`heeft `Min`drie `Max`dimensies: , en , die de gemiddelde, minimale en maximale latentie van de cache vertegenwoordigen tijdens het opgegeven rapportageinterval. |
| Cache-missers |Het aantal mislukte sleutelopzoekingen tijdens het opgegeven rapportageinterval. Dit nummer `keyspace_misses` wordt toegewezen aan van de opdracht Redis INFO. Cache missers betekenen niet noodzakelijkerwijs dat er een probleem is met de cache. Wanneer u bijvoorbeeld het programmeerpatroon cache opzij gebruikt, ziet een toepassing er eerst in de cache voor een item. Als het item er niet is (cachemiss), wordt het item opgehaald uit de database en voor de volgende keer aan de cache toegevoegd. Cache missers zijn normaal gedrag voor de cache-aside programmering patroon. Als het aantal cachefouten hoger is dan verwacht, onderzoekt u de toepassingslogica die wordt gevuld en gelezen vanuit de cache. Als items worden uitgezet uit de cache als gevolg van geheugendruk, dan kan er een aantal `Used Memory` cache `Evicted Keys`mist, maar een betere statistiek te controleren voor geheugendruk zou zijn of . |
| Cache lezen |De hoeveelheid gegevens die tijdens het opgegeven rapportageinterval uit de cache in Megabytes per seconde (MB/s) wordt gelezen. Deze waarde is afgeleid van de netwerkinterfacekaarten die de virtuele machine ondersteunen die de cache host en niet specifiek is voor Redis. **Deze waarde komt overeen met de netwerkbandbreedte die door deze cache wordt gebruikt. Als u waarschuwingen wilt instellen voor netwerkbandbreedtelimieten aan de `Cache Read` serverzijde, maakt u deze met behulp van deze teller. Zie [deze tabel](cache-faq.md#cache-performance) voor de waargenomen bandbreedtelimieten voor verschillende cacheprijsniveaus en -formaten.** |
| Cache schrijven |De hoeveelheid gegevens die tijdens het opgegeven rapportageinterval naar de cache is geschreven in Megabytes per seconde (MB/s). Deze waarde is afgeleid van de netwerkinterfacekaarten die de virtuele machine ondersteunen die de cache host en niet specifiek is voor Redis. Deze waarde komt overeen met de netwerkbandbreedte van gegevens die vanuit de client naar de cache worden verzonden. |
| Verbonden clients |Het aantal clientverbindingen met de cache tijdens het opgegeven rapportageinterval. Dit nummer `connected_clients` wordt toegewezen aan van de opdracht Redis INFO. Zodra de [verbindingslimiet](cache-configure.md#default-redis-server-configuration) is bereikt, mislukken volgende verbindingspogingen met de cache. Zelfs als er geen actieve clienttoepassingen zijn, kunnen er nog enkele gevallen van verbonden clients zijn als gevolg van interne processen en verbindingen. |
| CPU |Het CPU-gebruik van de Azure-cache voor Redis-server als percentage tijdens het opgegeven rapportage-interval. Deze waarde wordt toegewezen `\Processor(_Total)\% Processor Time` aan het prestatiemeternet van het besturingssysteem. |
| Fouten | Specifieke fouten en prestatieproblemen die de cache kan ondervinden tijdens een opgegeven rapportage-interval. Deze statistiek heeft acht dimensies die verschillende fouttypen vertegenwoordigen, maar kunnen in de toekomst meer zijn toegevoegd. De fouttypen die nu worden weergegeven, zijn als volgt: <br/><ul><li>**Failover** – wanneer een cache mislukt (ondergeschikte bevordert om te masteren)</li><li>**Dataloss** – wanneer er gegevensverlies is op de cache</li><li>**Niet-reagerenclients** – wanneer de clients niet snel genoeg gegevens van de server lezen</li><li>**AOF** – wanneer er een probleem is met betrekking tot AOF persistentie</li><li>**RDB** – wanneer er een probleem is met betrekking tot RDB persistentie</li><li>**Importeren** – wanneer er een probleem is met betrekking tot RdB importeren</li><li>**Export** – wanneer er een probleem is met betrekking tot Export RDB</li></ul> |
| Uitgezette sleutels |Het aantal items dat uit de cache is verwijderd `maxmemory` tijdens het opgegeven rapportageinterval vanwege de limiet. Dit nummer `evicted_keys` wordt toegewezen aan van de opdracht Redis INFO. |
| Verlopen sleutels |Het aantal items dat is verlopen uit de cache tijdens het opgegeven rapportageinterval. Deze waarde `expired_keys` wordt toegewezen aan van de opdracht Redis INFO.|
| Krijgt |Het aantal ophalenbewerkingen uit de cache tijdens het opgegeven rapportageinterval. Deze waarde is de som van de volgende `cmdstat_get`waarden `cmdstat_hget` `cmdstat_hgetall`van `cmdstat_hmget` `cmdstat_mget`de opdracht Redis INFO allen: , , , , `cmdstat_getbit`en `cmdstat_getrange`, en is gelijk aan de som van cachehits en -missers tijdens het rapportageinterval. |
| Bewerkingen per seconde | Het totale aantal opdrachten per seconde dat door de cacheserver wordt verwerkt tijdens het opgegeven rapportageinterval.  Deze waarde wordt toegewezen aan "instantaneous_ops_per_sec" van de opdracht Redis INFO. |
| Redis-serverbelasting |Het percentage cycli waarin de Redis-server bezig is met verwerken en niet inactief wacht op berichten. Als deze teller 100 bereikt, betekent dit dat de Redis-server een prestatieplafond heeft bereikt en de CPU kan het werk niet sneller verwerken. Als u een hoge Redis-serverbelasting ziet, ziet u time-outuitzonderingen in de client. In dit geval u overwegen uw gegevens op te schalen of te partitioneren in meerdere caches. |
| Sets |Het aantal ingestelde bewerkingen naar de cache tijdens het opgegeven rapportage-interval. Deze waarde is de som van de volgende `cmdstat_set`waarden `cmdstat_hset` `cmdstat_hmset`van `cmdstat_hsetnx` `cmdstat_lset`de `cmdstat_mset` `cmdstat_msetnx`opdracht `cmdstat_setbit` `cmdstat_setex`Redis INFO: , , , , , , , , , , , , `cmdstat_setrange`, en `cmdstat_setnx`. |
| Totaal aantal toetsen  | Het maximum aantal sleutels in de cache tijdens de afgelopen rapportageperiode. Dit nummer `keyspace` wordt toegewezen aan van de opdracht Redis INFO. Vanwege een beperking van het onderliggende metrische systeem, voor caches met clustering ingeschakeld, total keys retourneert het maximum aantal sleutels van de shard die het maximum aantal sleutels had tijdens de rapportage-interval.  |
| Totaal bewerkingen |Het totale aantal opdrachten dat door de cacheserver is verwerkt tijdens het opgegeven rapportageinterval. Deze waarde `total_commands_processed` wordt toegewezen aan van de opdracht Redis INFO. Wanneer Azure Cache voor Redis puur wordt gebruikt voor pub/sub, `Sets`zijn er geen `Total Operations` statistieken voor `Cache Hits`, `Cache Misses`, `Gets`of , maar er zijn statistieken die het cachegebruik voor pub/sub-bewerkingen weerspiegelen. |
| Gebruikt geheugen |De hoeveelheid cachegeheugen die wordt gebruikt voor sleutel-/waardeparen in de cache in MB tijdens het opgegeven rapportage-interval. Deze waarde `used_memory` wordt toegewezen aan van de opdracht Redis INFO. Deze waarde omvat geen metadata of fragmentatie. |
| Percentage gebruikt geheugen | Het % van het totale geheugen dat wordt gebruikt tijdens het opgegeven rapportage-interval.  Deze waarde verwijst `used_memory` naar de waarde van de opdracht Redis INFO om het percentage te berekenen. |
| RSS gebruikt geheugen |De hoeveelheid cachegeheugen die in MB wordt gebruikt tijdens het opgegeven rapportage-interval, inclusief fragmentatie en metagegevens. Deze waarde `used_memory_rss` wordt toegewezen aan van de opdracht Redis INFO. |

<a name="operations-and-alerts"></a>
## <a name="alerts"></a>Waarschuwingen

U kunt instellen dat u waarschuwingen ontvangt op basis van metrische gegevens en activiteitenlogboeken. Met Azure Monitor kunt u een waarschuwing zodanig configureren dat deze bij activering het volgende doet:

* Een e-mailmelding verzenden
* Een webhook aanroepen
* Een logische Azure-app aanroepen

Als u waarschuwingsregels voor uw cache wilt configureren, klikt u op **Waarschuwingsregels** in het **menu Resource**.

![Bewaking](./media/cache-how-to-monitor/redis-cache-monitoring.png)

Zie Overzicht van waarschuwingen voor meer informatie over het configureren en gebruiken [van waarschuwingen.](../monitoring-and-diagnostics/insights-alerts-portal.md)

## <a name="activity-logs"></a>Activiteitenlogboeken
Activiteitslogboeken geven inzicht in de bewerkingen die zijn uitgevoerd in uw Azure-cache voor Redis-exemplaren. Het was voorheen bekend als "audit logs" of "operationele logs". Met behulp van activiteitslogboeken u bepalen wat, wie en wanneer voor schrijfbewerkingen (PUT, POST, DELETE) die zijn gemaakt op uw Azure-cache voor Redis-exemplaren. 

> [!NOTE]
> Activiteitslogboeken bevatten geen leesbewerkingen (GET).
>

Als u activiteitslogboeken voor uw cache wilt weergeven, klikt u op **Activiteitslogboeken** in het **menu Resource**.

Zie Overzicht van het [Azure-activiteitenlogboek](../azure-monitor/platform/platform-logs-overview.md)voor meer informatie over activiteitslogboeken .
