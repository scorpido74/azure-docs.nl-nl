---
title: Het schalen van Azure Cache voor Redis
description: Meer informatie over het schalen van uw Azure-cache voor Redis-exemplaren met behulp van de Azure-portal en hulpprogramma's zoals Azure PowerShell en Azure CLI.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 04/11/2017
ms.openlocfilehash: 68c668561123aee943f54e6fdcbad7c6450957f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79277997"
---
# <a name="how-to-scale-azure-cache-for-redis"></a>Het schalen van Azure Cache voor Redis
Azure Cache voor Redis heeft verschillende cache-aanbiedingen, die flexibiliteit bieden in de keuze van cachegrootte en functies. Nadat een cache is gemaakt, u de grootte en de prijscategorie van de cache schalen als de vereisten van uw toepassing veranderen. In dit artikel ziet u hoe u uw cache schalen met de Azure-portal en hulpprogramma's zoals Azure PowerShell en Azure CLI.

## <a name="when-to-scale"></a>Wanneer schalen
U de [bewakingsfuncties](cache-how-to-monitor.md) van Azure Cache voor Redis gebruiken om de status en prestaties van uw cache te controleren en te bepalen wanneer de cache moet worden geschaald. 

U de volgende statistieken controleren om te bepalen of u moet schalen.

* Redis-serverbelasting
* Geheugengebruik
* Netwerkbandbreedte
* CPU-gebruik

Als u vaststelt dat uw cache niet meer voldoet aan de vereisten van uw toepassing, u schalen naar een grotere of kleinere prijscategorie voor caches die geschikt is voor uw toepassing. Zie wat Azure Cache voor het aanbod en de grootte van [Azure Cache voor Redis moet ik gebruiken voor](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use)meer informatie over het bepalen welke prijscategorie voor cache moet worden gebruikt.

## <a name="scale-a-cache"></a>Een cache schalen
Als u uw cache wilt schalen, [bladert u naar de cache](cache-configure.md#configure-azure-cache-for-redis-settings) in de [Azure-portal](https://portal.azure.com) en klikt u op **Schalen** in het **menu Resource.**

![Schalen](./media/cache-how-to-scale/redis-cache-scale-menu.png)

Selecteer de gewenste prijscategorie in het blad **Van de prijscategorie selecteren** en klik op **Selecteren**.

![Prijscategorie][redis-cache-pricing-tier-blade]


U schalen naar een andere prijscategorie met de volgende beperkingen:

* U niet schalen van een hogere prijslaag naar een lagere prijscategorie.
  * U niet schalen van een **Premium-cache** naar een **Standaard-** of **Basic-cache.**
  * U niet schalen van een **standaardcache** naar een **Basic-cache.**
* U schalen van een **Basic-cache** naar een **standaardcache,** maar u de grootte niet tegelijkertijd wijzigen. Als u een andere grootte nodig hebt, u een volgende schalingbewerking uitvoeren naar de gewenste grootte.
* U niet rechtstreeks vanuit een **Basic-cache** naar een **Premium-cache** schalen. Schaal eerst van **Basic** naar **Standard** in één schalingbewerking en vervolgens van **Standaard** naar **Premium** in een volgende schalingbewerking.
* U niet schalen van een groter formaat naar de Grootte van de **C0 (250 MB).**
 
Terwijl de cache wordt geschaald naar de nieuwe prijscategorie, wordt een **schalingstatus** weergegeven in de **Azure-cache voor het blade van Redis.**

![Schalen][redis-cache-scaling]

Wanneer de schaling is voltooid, verandert de status van **Schalen** naar **Uitvoeren**.

## <a name="how-to-automate-a-scaling-operation"></a>Een schaalbewerking automatiseren
Naast het schalen van uw cache-exemplaren in de Azure-portal, u schalen met PowerShell-cmdlets, Azure CLI en met behulp van de Microsoft Azure Management Libraries (MAML). 

* [Schalen met PowerShell](#scale-using-powershell)
* [Schalen met Azure CLI](#scale-using-azure-cli)
* [Schalen met MAML](#scale-using-maml)

### <a name="scale-using-powershell"></a>Schalen met PowerShell

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U uw Azure-cache voor Redis-exemplaren schalen met PowerShell met behulp `Size` `Sku`van `ShardCount` de cmdlet [Set-AzRedisCache](https://docs.microsoft.com/powershell/module/az.rediscache/set-azrediscache) wanneer de eigenschappen of eigenschappen worden gewijzigd. In het volgende voorbeeld ziet `myCache` u hoe u een cache schalen die is vernoemd naar een cache van 2,5 GB. 

    Set-AzRedisCache -ResourceGroupName myGroup -Name myCache -Size 2.5GB

Zie [Een Azure-cache voor Redis schalen met Powershell](cache-how-to-manage-redis-cache-powershell.md#scale)voor meer informatie over schalen met PowerShell.

### <a name="scale-using-azure-cli"></a>Schalen met Azure CLI
Als u uw Azure-cache wilt schalen voor `azure rediscache set` Redis-exemplaren met Azure CLI, roept u de opdracht aan en geeft u de gewenste configuratiewijzigingen door die een nieuwe grootte, sku of clustergrootte bevatten, afhankelijk van de gewenste schalingbewerking.

Zie Instellingen wijzigen van een [bestaande Azure-cache voor Redis voor](cache-manage-cli.md#scale)meer informatie over schalen met Azure CLI.

### <a name="scale-using-maml"></a>Schalen met MAML
Als u uw Azure-cache wilt schalen naar Redis-exemplaren met `IRedisOperations.CreateOrUpdate` behulp van de [Maml (Microsoft Azure Management Libraries),](https://azure.microsoft.com/updates/management-libraries-for-net-release-announcement/)belt u de methode aan en geeft u de nieuwe grootte voor de `RedisProperties.SKU.Capacity`.

    static void Main(string[] args)
    {
        // For instructions on getting the access token, see
        // https://azure.microsoft.com/documentation/articles/cache-configure/#access-keys
        string token = GetAuthorizationHeader();

        TokenCloudCredentials creds = new TokenCloudCredentials(subscriptionId,token);

        RedisManagementClient client = new RedisManagementClient(creds);
        var redisProperties = new RedisProperties();

        // To scale, set a new size for the redisSKUCapacity parameter.
        redisProperties.Sku = new Sku(redisSKUName,redisSKUFamily,redisSKUCapacity);
        redisProperties.RedisVersion = redisVersion;
        var redisParams = new RedisCreateOrUpdateParameters(redisProperties, redisCacheRegion);
        client.Redis.CreateOrUpdate(resourceGroupName,cacheName, redisParams);
    }

Zie Azure Cache [voor Redis beheren met maml-voorbeeld voor](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) meer informatie.

## <a name="scaling-faq"></a>Veelgestelde vragen over schalen
De volgende lijst bevat antwoorden op veelgestelde vragen over Azure Cache voor Redis-schaling.

* [Kan ik schalen naar, vanuit of binnen een Premium-cache?](#can-i-scale-to-from-or-within-a-premium-cache)
* [Moet ik na het schalen mijn cachenaam of toegangssleutels wijzigen?](#after-scaling-do-i-have-to-change-my-cache-name-or-access-keys)
* [Hoe werkt schalen?](#how-does-scaling-work)
* [Verlies ik gegevens uit mijn cache tijdens het schalen?](#will-i-lose-data-from-my-cache-during-scaling)
* [Wordt de instelling voor aangepaste databases beïnvloed tijdens het schalen?](#is-my-custom-databases-setting-affected-during-scaling)
* [Is mijn cache beschikbaar tijdens het schalen?](#will-my-cache-be-available-during-scaling)
* Waarom kan ik mijn cache niet schalen of de shards in een cluster wijzigen als georeplicatie is geconfigureerd?
* [Bewerkingen die niet worden ondersteund](#operations-that-are-not-supported)
* [Hoe lang duurt het schalen?](#how-long-does-scaling-take)
* [Hoe weet ik wanneer de schaling is voltooid?](#how-can-i-tell-when-scaling-is-complete)

### <a name="can-i-scale-to-from-or-within-a-premium-cache"></a>Kan ik schalen naar, vanuit of binnen een Premium-cache?
* U niet schalen van een **Premium-cache** naar een prijscategorie **Basis** of **Standaard.**
* U schalen van de **ene prijscategorie** voor Premium-cache naar de andere.
* U niet rechtstreeks vanuit een **Basic-cache** naar een **Premium-cache** schalen. Schaal eerst van **Basic** naar **Standard** in één schalingbewerking en vervolgens van **Standaard** naar **Premium** in een volgende schalingbewerking.
* Als u clustering hebt ingeschakeld toen u uw **Premium-cache** hebt gemaakt, u [de clustergrootte wijzigen.](cache-how-to-premium-clustering.md#cluster-size) Als uw cache is gemaakt zonder dat clustering is ingeschakeld, u clustering op een later tijdstip configureren.
  
  Zie [Clustering configureren voor een Premium Azure-cache voor Redis voor](cache-how-to-premium-clustering.md)meer informatie.

### <a name="after-scaling-do-i-have-to-change-my-cache-name-or-access-keys"></a>Moet ik na het schalen mijn cachenaam of toegangssleutels wijzigen?
Nee, uw cachenaam en -sleutels zijn ongewijzigd tijdens een schalingbewerking.

### <a name="how-does-scaling-work"></a>Hoe werkt schalen?
* Wanneer een **Basic-cache** wordt geschaald naar een andere grootte, wordt deze afgesloten en wordt een nieuwe cache ingericht met behulp van de nieuwe grootte. Gedurende deze periode is de cache niet beschikbaar en gaan alle gegevens in de cache verloren.
* Wanneer een **Basiscache** wordt geschaald naar een **standaardcache,** wordt een replicacache ingericht en worden de gegevens gekopieerd van de primaire cache naar de replicacache. De cache blijft beschikbaar tijdens het schalingproces.
* Wanneer een **standaardcache** wordt geschaald naar een andere grootte of naar een **Premium-cache,** wordt een van de replica's afgesloten en opnieuw ingericht naar de nieuwe grootte en de overgedragen gegevens, en vervolgens voert de andere replica een failover uit voordat deze opnieuw wordt ingericht, vergelijkbaar met het proces dat optreedt tijdens een storing van een van de cacheknooppunten.

### <a name="will-i-lose-data-from-my-cache-during-scaling"></a>Verlies ik gegevens uit mijn cache tijdens het schalen?
* Wanneer een **Basic-cache** wordt geschaald naar een nieuwe grootte, gaan alle gegevens verloren en is de cache niet beschikbaar tijdens de schalingbewerking.
* Wanneer een **Basic-cache** wordt geschaald naar een **standaardcache,** blijven de gegevens in de cache doorgaans behouden.
* Wanneer een **standaardcache** wordt geschaald naar een groter formaat of een grotere laag of een **Premium-cache** wordt geschaald naar een groter formaat, worden alle gegevens doorgaans behouden. Wanneer een **Standard-** of **Premium-cache** wordt geschaald naar een kleiner formaat, kunnen gegevens verloren gaan, afhankelijk van hoeveel gegevens er in de cache staan die verband houden met de nieuwe grootte wanneer deze wordt geschaald. Als gegevens verloren gaan bij het verkleinen, worden sleutels verwijderd met behulp van het [allkeys-lru-uitzettingsbeleid.](https://redis.io/topics/lru-cache) 

### <a name="is-my-custom-databases-setting-affected-during-scaling"></a>Wordt de instelling voor aangepaste databases beïnvloed tijdens het schalen?
Als u een aangepaste waarde `databases` hebt geconfigureerd voor de instelling tijdens het maken van de cache, moet u er rekening mee houden dat sommige prijsniveaus verschillende [databaseslimieten](cache-configure.md#databases)hebben. Hier volgen enkele overwegingen bij het schalen in dit scenario:

* Wanneer u wordt geschaald `databases` naar een prijscategorie met een ondergrens dan de huidige laag:
  * Als u het standaardnummer `databases`van , dat is 16 voor alle prijsniveaus, gebruikt, gaan er geen gegevens verloren.
  * Als u een aangepast `databases` aantal gebruikt dat binnen de limieten valt voor `databases` de laag waarop u schaalt, wordt deze instelling bewaard en gaan er geen gegevens verloren.
  * Als u een aangepast `databases` aantal gebruikt dat de limieten `databases` van de nieuwe laag overschrijdt, wordt de instelling verlaagd tot de grenzen van de nieuwe laag en gaan alle gegevens in de verwijderde databases verloren.
* Wanneer u wordt geschaald naar `databases` een prijscategorie met `databases` dezelfde of hogere limiet dan de huidige laag, wordt uw instelling behouden en gaan er geen gegevens verloren.

Hoewel standaard- en premiumcaches een SLA van 99,9% hebben voor beschikbaarheid, is er geen SLA voor gegevensverlies.

### <a name="will-my-cache-be-available-during-scaling"></a>Is mijn cache beschikbaar tijdens het schalen?
* **Standaard-** en **Premium-caches** blijven beschikbaar tijdens de schalingbewerking. Er kunnen echter verbindingsblips optreden tijdens het schalen van Standaard- en Premium-caches, en ook tijdens het schalen van Basic- naar Standard-caches. Deze verbindingsblips zullen naar verwachting klein zijn en redis-clients moeten hun verbinding direct kunnen herstellen.
* **Basiscaches** zijn offline tijdens het schalen naar een andere grootte. Basiscaches blijven beschikbaar bij het schalen van **Basic** naar **Standard,** maar kunnen een kleine verbindingsblip ervaren. Als er een verbindingsblip optreedt, moeten redis-clients hun verbinding direct kunnen herstellen.


### <a name="scaling-limitations-with-geo-replication"></a>Beperkingen schalen met Geo-replicatie

Zodra u een georeplicatiekoppeling tussen twee caches hebt toegevoegd, u geen schalingsbewerking meer starten of het aantal shards in een cluster wijzigen. U moet de koppeling van de cache ontkoppelen om deze opdrachten uit te geven. Zie [Georeplicatie configureren](cache-how-to-geo-replication.md)voor meer informatie .


### <a name="operations-that-are-not-supported"></a>Bewerkingen die niet worden ondersteund
* U niet schalen van een hogere prijslaag naar een lagere prijscategorie.
  * U niet schalen van een **Premium-cache** naar een **Standaard-** of **Basic-cache.**
  * U niet schalen van een **standaardcache** naar een **Basic-cache.**
* U schalen van een **Basic-cache** naar een **standaardcache,** maar u de grootte niet tegelijkertijd wijzigen. Als u een andere grootte nodig hebt, u een volgende schalingbewerking uitvoeren naar de gewenste grootte.
* U niet rechtstreeks vanuit een **Basic-cache** naar een **Premium-cache** schalen. Schaal eerst van **Basic** naar **Standard** in één schalingbewerking en schaal vervolgens van **Standaard** naar **Premium** in een volgende bewerking.
* U niet schalen van een groter formaat naar de Grootte van de **C0 (250 MB).**

Als een schalingbewerking mislukt, probeert de service de bewerking terug te draaien en wordt de cache teruggezet naar de oorspronkelijke grootte.


### <a name="how-long-does-scaling-take"></a>Hoe lang duurt het schalen?
De schalingtijd is afhankelijk van hoeveel gegevens zich in de cache bevinden, waarbij grotere hoeveelheden gegevens langer in beslag nemen. Schalen duurt ongeveer 20 minuten. Voor geclusterde caches duurt het schalen ongeveer 20 minuten per shard.

### <a name="how-can-i-tell-when-scaling-is-complete"></a>Hoe weet ik wanneer de schaling is voltooid?
In de Azure-portal u de schalingbewerking zien die wordt uitgevoerd. Wanneer de schaling is voltooid, wordt de status van de cache gewijzigd in **Actief .**

<!-- IMAGES -->

[redis-cache-pricing-tier-blade]: ./media/cache-how-to-scale/redis-cache-pricing-tier-blade.png

[redis-cache-scaling]: ./media/cache-how-to-scale/redis-cache-scaling.png
