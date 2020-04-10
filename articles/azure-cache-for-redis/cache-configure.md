---
title: Azure-cache configureren voor Redis
description: De standaard-Redis-configuratie voor Azure Cache voor Redis begrijpen en meer informatie over het configureren van uw Azure-cache voor Redis-exemplaren
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 08/22/2017
ms.author: yegu
ms.openlocfilehash: 3f0de52782694e6cbc8fdb6b55d545191dbbb350
ms.sourcegitcommit: ae3d707f1fe68ba5d7d206be1ca82958f12751e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/10/2020
ms.locfileid: "81010304"
---
# <a name="how-to-configure-azure-cache-for-redis"></a>Azure-cache configureren voor Redis
In dit onderwerp worden de configuraties beschreven die beschikbaar zijn voor uw Azure-cache voor Redis-exemplaren. Dit onderwerp behandelt ook de standaardconfiguratie van de Redis-server voor Azure Cache voor Redis-exemplaren.

> [!NOTE]
> Zie Hoe u [persistentie configureert](cache-how-to-premium-persistence.md), [Clustering configureren](cache-how-to-premium-clustering.md)en Ondersteuning [voor virtueel netwerk configureren](cache-how-to-premium-vnet.md)voor meer informatie over het configureren en gebruiken van functies met een premiumcache.
>
>

## <a name="configure-azure-cache-for-redis-settings"></a>Azure-cache configureren voor Redis-instellingen
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Azure Cache for Redis-instellingen worden bekeken en geconfigureerd in de **Azure Cache for Redis-blade** met behulp van het **resourcemenu.**

![Azure-cache voor Redis-instellingen](./media/cache-configure/redis-cache-settings.png)

U de volgende instellingen weergeven en configureren met behulp van het **resourcemenu.**

* [Overzicht](#overview)
* [Activiteitenlogboek](#activity-log)
* [Toegangsbeheer (IAM)](#access-control-iam)
* [Tags](#tags)
* [Problemen vaststellen en oplossen](#diagnose-and-solve-problems)
* [Instellingen](#settings)
    * [Toegangssleutels](#access-keys)
    * [Geavanceerde instellingen](#advanced-settings)
    * [Azure-cache voor Redis Advisor](#azure-cache-for-redis-advisor)
    * [Schalen](#scale)
    * [Clustergrootte](#cluster-size)
    * [Gegevenspersistentie](#redis-data-persistence)
    * [Updates plannen](#schedule-updates)
    * [Geo-replicatie](#geo-replication)
    * [Virtual Network](#virtual-network)
    * [Firewall](#firewall)
    * [Eigenschappen](#properties)
    * [Sloten](#locks)
    * [Automation-script](#automation-script)
* Beheer
    * [Gegevens importeren](#importexport)
    * [Gegevens exporteren](#importexport)
    * [Opnieuw opstarten](#reboot)
* [Bewaking](#monitoring)
    * [Redis-statistieken](#redis-metrics)
    * [Waarschuwingsregels](#alert-rules)
    * [Diagnostiek](#diagnostics)
* Instellingen voor & probleemoplossing ondersteunen
    * [Resourcestatus](#resource-health)
    * [Nieuw ondersteuningsverzoek](#new-support-request)


## <a name="overview"></a>Overzicht

**Overzicht** biedt u basisinformatie over uw cache, zoals naam, poorten, prijscategorie en geselecteerde cachestatistieken.

### <a name="activity-log"></a>Activiteitenlogboek

Klik **op Het logboek Activiteit** om acties in uw cache weer te geven. U ook filteren gebruiken om deze weergave uit te vouwen met andere bronnen. Zie [Controlebewerkingen met Resource Manager](../azure-resource-manager/management/view-activity-logs.md)voor meer informatie over het werken met controlelogboeken. Zie [Operations en waarschuwingen](cache-how-to-monitor.md#operations-and-alerts)voor meer informatie over het bewaken van Azure Cache voor Redis-gebeurtenissen.

### <a name="access-control-iam"></a>Toegangsbeheer (IAM)

De sectie **Toegangsbeheer (IAM)** biedt ondersteuning voor rbac (role-based access control) in de Azure-portal. Deze configuratie helpt organisaties eenvoudig en nauwkeurig aan hun vereisten voor toegangsbeheer te voldoen. Zie [Toegangsbeheer op basis van rollen in de Azure-portal](../role-based-access-control/role-assignments-portal.md)voor meer informatie.

### <a name="tags"></a>Tags

Met de sectie **Labels** u uw resources ordenen. Zie [Tags gebruiken om uw Azure-resources te organiseren](../azure-resource-manager/management/tag-resources.md) voor meer informatie.


### <a name="diagnose-and-solve-problems"></a>Problemen vaststellen en oplossen

Klik **op Diagnosticeren en problemen oplossen** die moeten worden voorzien van veelvoorkomende problemen en strategieën om deze op te lossen.



## <a name="settings"></a>Instellingen
Met de sectie **Instellingen** u de volgende instellingen voor uw cache openen en configureren.

* [Toegangssleutels](#access-keys)
* [Geavanceerde instellingen](#advanced-settings)
* [Azure-cache voor Redis Advisor](#azure-cache-for-redis-advisor)
* [Schalen](#scale)
* [Clustergrootte](#cluster-size)
* [Gegevenspersistentie](#redis-data-persistence)
* [Updates plannen](#schedule-updates)
* [Geo-replicatie](#geo-replication)
* [Virtual Network](#virtual-network)
* [Firewall](#firewall)
* [Eigenschappen](#properties)
* [Sloten](#locks)
* [Automation-script](#automation-script)



### <a name="access-keys"></a>Toegangssleutels
Klik **op Toegangssleutels** om de toegangssleutels voor uw cache weer te geven of opnieuw te genereren. Deze sleutels worden gebruikt door de clients die verbinding maken met uw cache.

![Azure-cache voor Access-sleutels van Redis](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Geavanceerde instellingen
De volgende instellingen zijn geconfigureerd op het geavanceerde **instellingenblad.**

* [Toegangspoorten](#access-ports)
* [Geheugenbeleid](#memory-policies)
* [Keyspace-meldingen (geavanceerde instellingen)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Toegangspoorten
Standaard is niet-TLS/SSL-toegang uitgeschakeld voor nieuwe caches. Als u de niet-TLS-poort wilt inschakelen, klikt u op **Nee** voor **Toegang alleen toestaan via SSL** op het geavanceerde **instellingenblad** en klikt u vervolgens op **Opslaan**.

> [!NOTE]
> TLS-toegang tot Azure Cache voor Redis ondersteunt TLS 1.0, 1.1 en 1.2 op dit moment, maar versies 1.0 en 1.1 worden binnenkort buiten gebruik gesteld.  Lees onze [Remove TLS 1.0 en 1.1 pagina](cache-remove-tls-10-11.md) voor meer informatie.

![Azure-cache voor Redis Access-poorten](./media/cache-configure/redis-cache-access-ports.png)

<a name="maxmemory-policy-and-maxmemory-reserved"></a>
#### <a name="memory-policies"></a>Geheugenbeleid
De instellingen voor het geheugen **van Maxmemory**, **maxmemory-gereserveerde** **instellingen** op het geavanceerde **instellingenblad** configureren het geheugenbeleid voor de cache.

![Azure-cache voor Redis Max-geheugenbeleid](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Maxmemory-beleid** configureert het uitzettingsbeleid voor de cache en stelt u in staat om te kiezen uit het volgende uitzettingsbeleid:

* `volatile-lru`- Dit is het standaard uitzettingsbeleid.
* `allkeys-lru`
* `volatile-random`
* `allkeys-random`
* `volatile-ttl`
* `noeviction`

Zie `maxmemory` [Uitzettingsbeleid](https://redis.io/topics/lru-cache#eviction-policies)voor meer informatie over beleid .

Met de instelling **maxmemory-gereserveerdconfigureert** u de hoeveelheid geheugen in MB die is gereserveerd voor bewerkingen buiten de cache, zoals replicatie tijdens failover. Als u deze waarde instelt, u een consistentere Redis-serverervaring hebben wanneer uw belasting varieert. Deze waarde moet hoger worden ingesteld voor workloads die zwaar zijn geschreven. Wanneer het geheugen is gereserveerd voor dergelijke bewerkingen, is het niet beschikbaar voor opslag van gegevens in de cache.

De instelling **maxfragmentatiegeheugen gereserveerd** configureert de hoeveelheid geheugen in MB die is gereserveerd voor geheugenfragmentatie. Als u deze waarde instelt, u een consistentere Redis-serverervaring hebben wanneer de cache vol of dicht bij vol is en de fragmentatieverhouding hoog is. Wanneer het geheugen is gereserveerd voor dergelijke bewerkingen, is het niet beschikbaar voor opslag van gegevens in de cache.

Een ding om te overwegen bij het kiezen van een nieuwe geheugenreserveringswaarde **(maxmemory-gereserveerd** of **maxfragmentationmemory-gereserveerd)** is hoe deze wijziging van invloed kan zijn op een cache die al wordt uitgevoerd met grote hoeveelheden gegevens erin. Als u bijvoorbeeld een cache van 53 GB met 49 GB aan gegevens hebt en vervolgens de reserveringswaarde wijzigt in 8 GB, wordt het maximaal beschikbare geheugen voor het systeem gedaald tot 45 GB. Als uw `used_memory` huidige `used_memory_rss` of uw waarden hoger zijn dan de nieuwe limiet van 45 `used_memory` `used_memory_rss` GB, moet het systeem gegevens verwijderen totdat beide en lager zijn dan 45 GB. Uitzetting kan de belasting van de server en de fragmentatie van het geheugen verhogen. Zie Beschikbare statistieken en `used_memory` rapportageintervallen voor meer informatie over cachestatistieken zoals en `used_memory_rss`zie [Beschikbare statistieken en rapportageintervallen](cache-how-to-monitor.md#available-metrics-and-reporting-intervals).

> [!IMPORTANT]
> De instellingen die zijn gereserveerd voor maximaal geheugen **en maxfragmentatiegeheugenzijn** alleen beschikbaar voor standaard- en **Premium-caches.**
>
>

#### <a name="keyspace-notifications-advanced-settings"></a>Keyspace-meldingen (geavanceerde instellingen)
Redis keyspace-meldingen zijn geconfigureerd op het **geavanceerde instellingenblad.** Met Keyspace-meldingen kunnen clients meldingen ontvangen wanneer bepaalde gebeurtenissen zich voordoen.

![Azure-cache voor geavanceerde instellingen van Redis](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Keyspace-meldingen en de instelling **voor instelling voor spatiegebeurtenissen** voor meldingen zijn alleen beschikbaar voor standaard- en Premium-caches.
>
>

Zie [Meldingen van Redis Keyspace](https://redis.io/topics/notifications)voor meer informatie. Zie voor voorbeeldcode het [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) bestand in het [voorbeeld van Hello world.](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)


<a name="recommendations"></a>
## <a name="azure-cache-for-redis-advisor"></a>Azure-cache voor Redis Advisor
In de Azure Cache voor het blade **van Redis Advisor** worden aanbevelingen voor uw cache weergegeven. Tijdens normale bewerkingen worden geen aanbevelingen weergegeven.

![Aanbevelingen](./media/cache-configure/redis-cache-no-recommendations.png)

Als er omstandigheden optreden tijdens de bewerkingen van uw cache, zoals een hoog geheugengebruik, netwerkbandbreedte of serverbelasting, wordt een waarschuwing weergegeven in de **Azure-cache voor het blade van Redis.**

![Aanbevelingen](./media/cache-configure/redis-cache-recommendations-alert.png)

Meer informatie is te vinden op het **recommendations** blade.

![Aanbevelingen](./media/cache-configure/redis-cache-recommendations.png)

U deze statistieken controleren in de secties [Monitorgrafieken](cache-how-to-monitor.md#monitoring-charts) en [Gebruiksdiagrammen](cache-how-to-monitor.md#usage-charts) van het **Azure-cache voor Redis-blad.**

Elke prijscategorie heeft verschillende limieten voor clientverbindingen, geheugen en bandbreedte. Als uw cache de maximale capaciteit voor deze statistieken over een langdurige periode benadert, wordt een aanbeveling gemaakt. Zie de volgende tabel voor meer informatie over de statistieken en limieten die worden beoordeeld door het hulpprogramma **Aanbevelingen:**

| Azure-cache voor metriek Redis | Meer informatie |
| --- | --- |
| Gebruik van netwerkbandbreedte |[Cacheprestaties - beschikbare bandbreedte](cache-faq.md#cache-performance) |
| Verbonden clients |[Standaardconfiguratie van de Redis-server - maxclients](#maxclients) |
| Serverbelasting |[Gebruiksgrafieken - Redis Server Load](cache-how-to-monitor.md#usage-charts) |
| Geheugengebruik |[Cacheprestaties - grootte](cache-faq.md#cache-performance) |

Als u uw cache wilt upgraden, klikt u nu op **Bijwerken** om de prijscategorie te wijzigen en uw cache [te schalen.](#scale) Zie [Wat voor Azure-cache voor Redis-aanbieding en grootte moet ik gebruiken voor](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) meer informatie over het kiezen van een prijscategorie?


### <a name="scale"></a>Schalen
Klik **op Schalen** om de prijscategorie voor uw cache weer te geven of te wijzigen. Zie [Azure-cache schalen voor Redis voor](cache-how-to-scale.md)meer informatie over schalen.

![Azure-cache voor de prijscategorie Redis](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Clustergrootte van Redis
Klik **op Clustergrootte** om de clustergrootte te wijzigen voor een draaiende premiumcache waarbij clustering is ingeschakeld.

![Clustergrootte](./media/cache-configure/redis-cache-redis-cluster-size.png)

Als u de clustergrootte wilt wijzigen, gebruikt u de schuifregelaar of typt u een getal tussen 1 en 10 in het tekstvak **Shardtellen** en klikt u op **OK** om op te slaan.

> [!IMPORTANT]
> Redis-clustering is alleen beschikbaar voor Premium-caches. Zie [Clustering configureren voor een Premium Azure-cache voor Redis voor](cache-how-to-premium-clustering.md)meer informatie.
>
>


### <a name="redis-data-persistence"></a>Redis-gegevenspersistentie
Klik **op Gegevenspersistentie** om gegevenspersistentie voor uw premium cache in te schakelen, uit te schakelen of te configureren. Azure Cache voor Redis biedt Redis persistentie met behulp van [RDB persistentie](cache-how-to-premium-persistence.md#configure-rdb-persistence) of [AOF persistentie.](cache-how-to-premium-persistence.md#configure-aof-persistence)

Zie [Persistentie configureren voor een Premium Azure-cache voor Redis voor](cache-how-to-premium-persistence.md)meer informatie.


> [!IMPORTANT]
> Redis data persistentie is alleen beschikbaar voor Premium caches.
>
>

### <a name="schedule-updates"></a>Updates plannen
Met het blade **Updates van schema** u een onderhoudsvenster aanwijzen voor Redis-serverupdates voor uw cache.

> [!IMPORTANT]
> Het onderhoudsvenster is alleen van toepassing op Redis-serverupdates en niet op Azure-updates of -updates voor het besturingssysteem van de VM's die de cache hosten.
>
>

![Updates plannen](./media/cache-configure/redis-schedule-updates.png)

Als u een onderhoudsvenster wilt opgeven, controleert u de gewenste dagen en geeft u het beginuur van het onderhoudsvenster voor elke dag op en klikt u op **OK**. De tijd van het onderhoudsvenster is in UTC.

> [!IMPORTANT]
> De functionaliteit **Updates plannen** is alleen beschikbaar voor premium-caches. Zie [Azure Cache voor Beheer van Redis voor](cache-administration.md#schedule-updates)meer informatie en instructies voor meer informatie en instructies - Updates plannen .
>
>

### <a name="geo-replication"></a>Geo-replicatie

Het **georeplicatieblad** biedt een mechanisme voor het koppelen van twee Azure-cache in de Premium-laag voor Redis-exemplaren. De ene cache wordt aangeduid als de primaire gekoppelde cache en de andere als de secundaire gekoppelde cache. De secundaire gekoppelde cache wordt alleen-lezen en gegevens die naar de primaire cache worden geschreven, worden gerepliceerd naar de secundaire gekoppelde cache. Deze functionaliteit kan worden gebruikt om een cache te repliceren in Azure-regio's.

> [!IMPORTANT]
> **Georeplicatie** is alleen beschikbaar voor Premium-lagencaches. Zie [Geo-replicatie configureren voor Azure-cache voor Redis voor](cache-how-to-geo-replication.md)meer informatie en instructies.
>
>

### <a name="virtual-network"></a>Virtual Network
Met de sectie **Virtueel netwerk** u de virtuele netwerkinstellingen voor uw cache configureren. Zie Ondersteuning voor [virtueel netwerk configureren voor een Premium Azure-cache voor Redis voor](cache-how-to-premium-vnet.md)informatie over het maken van een premium cache met VNET-ondersteuning en het bijwerken van de instellingen.

> [!IMPORTANT]
> Virtuele netwerkinstellingen zijn alleen beschikbaar voor premium caches die zijn geconfigureerd met VNET-ondersteuning tijdens het maken van caches.
>
>

### <a name="firewall"></a>Firewall

Firewallregels configuratie is beschikbaar voor alle Azure Cache voor Redis lagen.

Klik op **Firewall** om firewallregels voor cache weer te geven en te configureren.

![Firewall](./media/cache-configure/redis-firewall-rules.png)

U firewallregels opgeven met een begin- en eind-IP-adresbereik. Wanneer firewallregels zijn geconfigureerd, kunnen alleen clientverbindingen uit de opgegeven IP-adresbereiken verbinding maken met de cache. Wanneer een firewallregel wordt opgeslagen, is er een korte vertraging voordat de regel van kracht is. Deze vertraging is meestal minder dan een minuut.

> [!IMPORTANT]
> Verbindingen vanuit Azure Cache voor Redis-bewakingssystemen zijn altijd toegestaan, zelfs als firewallregels zijn geconfigureerd.
>
>

### <a name="properties"></a>Eigenschappen
Klik **op Eigenschappen** om informatie over uw cache weer te geven, inclusief het eindpunt en de poorten in de cache.

![Azure-cache voor eigenschappen van Redis](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Vergrendelingen
Met de sectie **Vergrendeling** u een abonnement, resourcegroep of resource vergrendelen om te voorkomen dat andere gebruikers in uw organisatie per ongeluk kritieke resources verwijderen of wijzigen. Zie voor meer informatie [Resources vergrendelen met Azure Resource Manager](../azure-resource-manager/management/lock-resources.md).

### <a name="automation-script"></a>Automation-script

Klik **op Het script van automatisering** om een sjabloon van uw geïmplementeerde resources te maken en te exporteren voor toekomstige implementaties. Zie [Resources implementeren met Azure Resource Manager-sjablonen voor](../azure-resource-manager/templates/deploy-powershell.md)meer informatie over het werken met sjablonen.

## <a name="administration-settings"></a>Beheerinstellingen
Met de instellingen in de sectie **Beheer** u de volgende administratieve taken voor uw cache uitvoeren.

![Beheer](./media/cache-configure/redis-cache-administration.png)

* [Gegevens importeren](#importexport)
* [Gegevens exporteren](#importexport)
* [Opnieuw opstarten](#reboot)


### <a name="importexport"></a>Import/Export
Importeren/exporteren is een Azure-cache voor Redis-gegevensbeheerbewerking, waarmee u gegevens in de cache importeren en exporteren door een Azure Cache for Redis Database (RDB)-momentopname te importeren en exporteren van een premiumcache naar een paginablob in een Azure Storage-account. Met Importeren/exporteren u migreren tussen verschillende Azure-cache voor Redis-exemplaren of de cache vullen met gegevens voordat u deze gebruikt.

Import kan worden gebruikt om Redis-compatibele RDB-bestanden van elke Redis-server te brengen die in elke cloud of omgeving wordt uitgevoerd, waaronder Redis die wordt uitgevoerd op Linux, Windows of een cloudprovider zoals Amazon Web Services en anderen. Het importeren van gegevens is een eenvoudige manier om een cache te maken met vooraf ingevulde gegevens. Tijdens het importproces laadt Azure Cache voor Redis de RDB-bestanden uit Azure-opslag in het geheugen en voegt u de sleutels vervolgens in de cache in.

Met exporteren u de gegevens die zijn opgeslagen in Azure Cache voor Redis exporteren naar RDB-bestanden die compatibel zijn met Redis. U deze functie gebruiken om gegevens van de ene Azure-cache voor Redis-instantie naar een andere of naar een andere Redis-server te verplaatsen. Tijdens het exportproces wordt een tijdelijk bestand gemaakt op de VM waarmee de Azure-cache voor de serverinstantie Van Redis wordt gehost en wordt het bestand geüpload naar het aangewezen opslagaccount. Wanneer de exportbewerking is voltooid met een status van succes of fout, wordt het tijdelijke bestand verwijderd.

> [!IMPORTANT]
> Import/export is alleen beschikbaar voor Premium-lagencaches. Zie [Gegevens importeren en exporteren in Azure Cache voor Redis voor](cache-how-to-import-export-data.md)meer informatie en instructies.
>
>

### <a name="reboot"></a>Opnieuw opstarten
Met het **rebootblad** u de knooppunten van uw cache opnieuw opstarten. Met deze rebootmogelijkheid u uw toepassing testen op tolerantie als er een fout van een cacheknooppunt optreedt.

![Opnieuw opstarten](./media/cache-configure/redis-cache-reboot.png)

Als u een premium cache hebt met clustering ingeschakeld, u selecteren welke shards van de cache u opnieuw wilt opstarten.

![Opnieuw opstarten](./media/cache-configure/redis-cache-reboot-cluster.png)

Als u een of meer knooppunten van uw cache opnieuw wilt opstarten, selecteert u de gewenste knooppunten en klikt u op **Opnieuw opstarten.** Als u een premium cache hebt met clustering ingeschakeld, selecteert u de shard(s) om opnieuw op te starten en klikt u vervolgens op **Opnieuw opstarten**. Na een paar minuten, de geselecteerde node (s) reboot, en zijn weer online een paar minuten later.

> [!IMPORTANT]
> Reboot is nu beschikbaar voor alle prijsniveaus. Zie [Azure Cache voor Redis-beheer voor](cache-administration.md#reboot)meer informatie en instructies voor meer informatie en instructies - Opnieuw opstarten .
>
>


## <a name="monitoring"></a>Bewaking

Met de sectie **Controle** u diagnostische gegevens en bewaking configureren voor uw Azure-cache voor Redis.
Zie [Azure Cache voor Redis controleren](cache-how-to-monitor.md)voor meer informatie over Azure Cache voor Redis-monitoring en -diagnose.

![Diagnostiek](./media/cache-configure/redis-cache-diagnostics.png)

* [Redis-statistieken](#redis-metrics)
* [Waarschuwingsregels](#alert-rules)
* [Diagnostiek](#diagnostics)

### <a name="redis-metrics"></a>Redis-statistieken
Klik **op Redis-statistieken** om [statistieken](cache-how-to-monitor.md#view-cache-metrics) voor uw cache weer te geven.

### <a name="alert-rules"></a>Waarschuwingsregels

Klik **op Waarschuwingsregels** om waarschuwingen te configureren op basis van Azure Cache voor Redis-statistieken. Zie [Waarschuwingen voor](cache-how-to-monitor.md#alerts)meer informatie.

### <a name="diagnostics"></a>Diagnostiek

Cachestatistieken in Azure Monitor worden standaard [30 dagen opgeslagen](../azure-monitor/platform/data-platform-metrics.md) en vervolgens verwijderd. Als u uw cachestatistieken langer dan 30 dagen wilt bewaren, klikt u op **Diagnostische gegevens** om [het opslagaccount](cache-how-to-monitor.md#export-cache-metrics) te configureren dat wordt gebruikt om cachediagnoses op te slaan.

>[!NOTE]
>Naast het archiveren van uw cachestatistieken naar opslag, u ze ook [streamen naar een gebeurtenishub of ze naar Azure Monitor-logboeken verzenden.](../azure-monitor/platform/stream-monitoring-data-event-hubs.md)
>
>

## <a name="support--troubleshooting-settings"></a>Instellingen voor & probleemoplossing ondersteunen
De instellingen in de sectie **Ondersteuning + probleemoplossing** bieden u opties voor het oplossen van problemen met uw cache.

![Ondersteuning en probleemoplossing](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Resourcestatus](#resource-health)
* [Nieuw ondersteuningsverzoek](#new-support-request)

### <a name="resource-health"></a>Status van resources
**Resourcestatus** houdt uw resource in de gaten en geeft aan of deze wordt uitgevoerd zoals verwacht. Zie overzicht van azure [resource-status](../resource-health/resource-health-overview.md)voor meer informatie over de statusservice azure resource .

> [!NOTE]
> Resourcestatus kan momenteel niet rapporteren over de status van Azure Cache voor Redis-exemplaren die in een virtueel netwerk worden gehost. Zie [Werken alle cachefuncties bij het hosten van een cache in een VNET voor](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet) meer informatie?
>
>

### <a name="new-support-request"></a>Nieuw ondersteuningsverzoek
Klik **op Nieuw ondersteuningsverzoek** om een ondersteuningsaanvraag voor uw cache te openen.





## <a name="default-redis-server-configuration"></a>Standaardconfiguratie van de Redis-server
Nieuwe Azure-cache voor Redis-exemplaren zijn geconfigureerd met de volgende standaardconfiguratiewaarden van Redis:

> [!NOTE]
> De instellingen in deze sectie `StackExchange.Redis.IServer.ConfigSet` kunnen niet worden gewijzigd met behulp van de methode. Als deze methode wordt aangeroepen met een van de opdrachten in deze sectie, wordt een uitzondering die vergelijkbaar is met het volgende voorbeeld gegooid:  
>
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>
> Alle waarden die configureerbaar zijn, zoals **max-memory-policy,** kunnen worden geconfigureerd via de Azure-portal of command-line beheertools zoals Azure CLI of PowerShell.
>
>

| Instelling | Standaardwaarde | Beschrijving |
| --- | --- | --- |
| `databases` |16 |Het standaardaantal databases is 16, maar u een ander getal configureren op basis van de prijscategorie. <sup>1</sup> De standaarddatabase is DB 0, u een andere `connection.GetDatabase(dbid)` `dbid` database selecteren `0` op `databases - 1`basis van een verbinding met behulp van waar een getal tussen en. |
| `maxclients` |Afhankelijk van de prijscategorie<sup>2</sup> |Deze waarde is het maximum aantal verbonden clients dat tegelijkertijd is toegestaan. Zodra de limiet is bereikt, sluit Redis alle nieuwe verbindingen, en retourt u een fout van het 'max aantal bereikte clients'. |
| `maxmemory-policy` |`volatile-lru` |Maxmemory-beleid is de instelling voor hoe Redis selecteert wat moet worden verwijderd wanneer `maxmemory` (de grootte van de cache die u hebt geselecteerd toen u de cache hebt gemaakt) is bereikt. Met Azure Cache voor Redis `volatile-lru`is de standaardinstelling , die de sleutels met een vervaldatumset verwijdert met behulp van een LRU-algoritme. Deze instelling kan worden geconfigureerd in de Azure-portal. Zie [Geheugenbeleid](#memory-policies)voor meer informatie . |
| `maxmemory-samples` |3 |Om geheugen op te slaan, LRU en minimale TTL-algoritmen zijn benaderd algoritmen in plaats van nauwkeurige algoritmen. Redis controleert standaard drie toetsen en kiest de toetsen die minder recent is gebruikt. |
| `lua-time-limit` |5.000 |Maximale uitvoeringstijd van een Lua-script in milliseconden. Als de maximale uitvoeringstijd is bereikt, registreert Redis dat een script nog steeds in uitvoering is na de maximaal toegestane tijd en begint het beantwoorden van query's met een fout. |
| `lua-event-limit` |500 |Maximale grootte van de wachtrij voor scriptgebeurtenissen. |
| `client-output-buffer-limit` `normalclient-output-buffer-limit` `pubsub` |0 0 032mb 8mb 60 |De clientuitvoerbufferlimieten kunnen worden gebruikt om de ontkoppeling af te dwingen van clients die om de een of andere reden geen gegevens van de server lezen (een veelvoorkomende reden is dat een Pub/Sub-client berichten niet zo snel kan gebruiken als de uitgever ze kan produceren). Zie voor meer [https://redis.io/topics/clients](https://redis.io/topics/clients)informatie . |

<a name="databases"></a>
<sup>1.</sup> De limiet `databases` voor is verschillend voor elke Azure-cache voor de prijscategorie Van Redis en kan worden ingesteld bij het maken van cache. Als `databases` er geen instelling is opgegeven tijdens het maken van de cache, is de standaardinstelling 16.

* Basis- en standaardcaches
  * C0-cache (250 MB) - maximaal 16 databases
  * C1-cache (1 GB) - maximaal 16 databases
  * C2-cache (2,5 GB) - maximaal 16 databases
  * C3-cache (6 GB) - maximaal 16 databases
  * C4-cache (13 GB) - maximaal 32 databases
  * C5-cache (26 GB) - maximaal 48 databases
  * C6-cache (53 GB) - maximaal 64 databases
* Premium caches
  * P1 (6 GB - 60 GB) - maximaal 16 databases
  * P2 (13 GB - 130 GB) - maximaal 32 databases
  * P3 (26 GB - 260 GB) - maximaal 48 databases
  * P4 (53 GB - 530 GB) - tot 64 databases
  * Alle premium caches met Redis-cluster ingeschakeld - Redis-cluster `databases` ondersteunt alleen het gebruik van database 0, zodat de limiet voor elke premium cache met Redis-cluster is ingeschakeld, effectief 1 is en de opdracht [Selecteren](https://redis.io/commands/select) is niet toegestaan. Zie [Moet ik wijzigingen aanbrengen in mijn clienttoepassing om clustering te gebruiken voor](cache-how-to-premium-clustering.md#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering) meer informatie?

Zie [Wat zijn Redis-databases voor](cache-faq.md#what-are-redis-databases) meer informatie over databases?

> [!NOTE]
> De `databases` instelling kan alleen worden geconfigureerd tijdens het maken van cache en alleen met PowerShell- en CLI-instellingen of andere beheerclients. Zie `databases` [Nieuw-AzRedisCache](cache-how-to-manage-redis-cache-powershell.md#databases)voor een voorbeeld van configureren tijdens het maken van caches met PowerShell.
>
>

<a name="maxclients"></a>
<sup>2</sup> `maxclients` is verschillend voor elke Azure-cache voor de prijscategorie Van Redis.

* Basis- en standaardcaches
  * C0-cache (250 MB) - maximaal 256 verbindingen
  * C1-cache (1 GB) - tot 1.000 verbindingen
  * C2-cache (2,5 GB) - maximaal 2.000 verbindingen
  * C3-cache (6 GB) - tot 5.000 verbindingen
  * C4-cache (13 GB) - tot 10.000 verbindingen
  * C5-cache (26 GB) - tot 15.000 verbindingen
  * C6-cache (53 GB) - tot 20.000 verbindingen
* Premium caches
  * P1 (6 GB - 60 GB) - tot 7.500 aansluitingen
  * P2 (13 GB - 130 GB) - tot 15.000 aansluitingen
  * P3 (26 GB - 260 GB) - tot 30.000 aansluitingen
  * P4 (53 GB - 530 GB) - tot 40.000 aansluitingen

> [!NOTE]
> Terwijl elke grootte van de cache *tot* een bepaald aantal verbindingen mogelijk maakt, heeft elke verbinding met Redis overhead die eraan is gekoppeld. Een voorbeeld van dergelijke overhead zou cpu- en geheugengebruik zijn als gevolg van TLS/SSL-versleuteling. De maximale verbindingslimiet voor een bepaalde cachegrootte gaat uit van een licht geladen cache. Als de belasting van verbindingsoverhead *plus* belasting van clientbewerkingen de capaciteit voor het systeem overschrijdt, kan de cache capaciteitsproblemen ondervinden, zelfs als u de verbindingslimiet voor de huidige cachegrootte niet hebt overschreden.
>
>



## <a name="redis-commands-not-supported-in-azure-cache-for-redis"></a>Opdrachten voor Redis worden niet ondersteund in Azure Cache voor Redis
> [!IMPORTANT]
> Omdat de configuratie en het beheer van Azure Cache voor Redis-exemplaren door Microsoft wordt beheerd, worden de volgende opdrachten uitgeschakeld. Als u ze probeert aan te roepen, `"(error) ERR unknown command"`ontvangt u een foutmelding die vergelijkbaar is met .
>
> * BGREWRITEAOF
> * BGSAVE (BGSAVE)
> * Config
> * FOUTOPSPORING
> * Migreren
> * OPSLAAN
> * Afsluiten
> * SLAVEOF SLAVEOF
> * CLUSTER - Clusterschrijfopdrachten zijn uitgeschakeld, maar alleen-lezen clusteropdrachten zijn toegestaan.
>
>

Zie [https://redis.io/commands](https://redis.io/commands)voor meer informatie over Redis-opdrachten .

## <a name="redis-console"></a>Redis console
U opdrachten veilig uitvoeren in uw Azure-cache voor Redis-exemplaren met behulp van de **Redis-console**, die beschikbaar is in de Azure-portal voor alle cachelagen.

> [!IMPORTANT]
> - De Redis Console werkt niet met [VNET](cache-how-to-premium-vnet.md). Wanneer uw cache deel uitmaakt van een VNET, hebben alleen clients in het VNET toegang tot de cache. Omdat Redis Console wordt uitgevoerd in uw lokale browser, die zich buiten het VNET bevindt, kan deze geen verbinding maken met uw cache.
> - Niet alle Redis-opdrachten worden ondersteund in Azure Cache voor Redis. Zie de vorige [Redis-opdrachten](#redis-commands-not-supported-in-azure-cache-for-redis) die niet worden ondersteund in de sectie Azure Cache voor Redis voor een lijst met Redis-opdrachten die zijn uitgeschakeld voor Azure-cache voor Redis. Zie [https://redis.io/commands](https://redis.io/commands)voor meer informatie over Redis-opdrachten .
>
>

Als u toegang wilt krijgen tot de Redis-console, klikt u op **Console** vanuit de **Azure-cache voor het redis-blad.**

![Redis console](./media/cache-configure/redis-console-menu.png)

Als u opdrachten wilt uitvoeren tegen uw cache-instantie, typt u de gewenste opdracht in de console.

![Redis console](./media/cache-configure/redis-console.png)


### <a name="using-the-redis-console-with-a-premium-clustered-cache"></a>De Redis-console gebruiken met een premium geclusterde cache

Wanneer u de Redis-console gebruikt met een premium geclusterde cache, u opdrachten uitgeven aan één shard van de cache. Als u een opdracht wilt uitgeven aan een specifieke scherf, maakt u eerst verbinding met de gewenste shard door erop te klikken op de shardkiezer.

![Redis console](./media/cache-configure/redis-console-premium-cluster.png)

Als u probeert toegang te krijgen tot een sleutel die is opgeslagen in een andere shard dan de verbonden shard, ontvangt u een foutbericht dat vergelijkbaar is met het volgende bericht:

```
shard1>get myKey
(error) MOVED 866 13.90.202.154:13000 (shard 0)
```

In het vorige voorbeeld is shard 1 `myKey` de geselecteerde shard, maar bevindt zich in shard 0, zoals aangegeven door het `(shard 0)` gedeelte van het foutbericht. Selecteer in dit `myKey`voorbeeld toegang tot shard 0 met de shardkiezer en geef vervolgens de gewenste opdracht uit.


## <a name="move-your-cache-to-a-new-subscription"></a>Uw cache verplaatsen naar een nieuw abonnement
U uw cache verplaatsen naar een nieuw abonnement door op **Verplaatsen**te klikken.

![Azure-cache verplaatsen voor Redis](./media/cache-configure/redis-cache-move.png)

Zie Resources verplaatsen [naar een nieuwe resourcegroep of -abonnement](../azure-resource-manager/management/move-resource-group-and-subscription.md)voor informatie over het verplaatsen van resources van de ene resourcegroep naar de andere en van het ene abonnement naar het andere.

## <a name="next-steps"></a>Volgende stappen
* Zie [Hoe kan ik Redis-opdrachten uitvoeren voor](cache-faq.md#how-can-i-run-redis-commands) meer informatie over het werken met Redis-opdrachten?
