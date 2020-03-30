---
title: Gegevenspersistentie configureren - Premium Azure-cache voor Redis
description: Meer informatie over het configureren en beheren van gegevenspersistentie van uw Azure-cache in Premium-lagen voor Redis-exemplaren
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 08/24/2017
ms.openlocfilehash: 84a5b4784a36fb22ae50a7a1ec4fcb7e5ef5b7c5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80245273"
---
# <a name="how-to-configure-data-persistence-for-a-premium-azure-cache-for-redis"></a>Gegevenspersistentie configureren voor een Premium Azure-cache voor Redis
Azure Cache voor Redis heeft verschillende cache-aanbiedingen die flexibiliteit bieden in de keuze van cachegrootte en functies, waaronder premium-laagfuncties zoals clustering, persistentie en ondersteuning voor virtueel netwerk. In dit artikel wordt beschreven hoe u persistentie configureert in een premium Azure-cache voor redis-instantie.

Zie [Inleiding tot de Azure-cache voor Redis Premium-laag voor](cache-premium-tier-intro.md)meer informatie over andere functies met een premiumcache.

## <a name="what-is-data-persistence"></a>Wat is data persistentie?
[Met de persistentie van Redis](https://redis.io/topics/persistence) u doorgaan met gegevens die zijn opgeslagen in Redis. U ook snapshots maken en een back-up maken van de gegevens, die u laden in geval van een hardwarefout. Dit is een enorm voordeel ten opzichte van Basic of Standard laag waar alle gegevens worden opgeslagen in het geheugen en er kan potentieel verlies van gegevens in het geval van een storing waar Cache knooppunten zijn down. 

Azure Cache voor Redis biedt Redis-persistentie met behulp van de volgende modellen:

* **RDB-persistentie** - Wanneer de persistentie van de RDB -database is geconfigureerd, blijft Azure Cache voor Redis een momentopname van de Azure-cache voor Redis in een binaire Indeling van Redis naar schijf op basis van een configureerbare back-upfrequentie voorhouden. Als er een catastrofale gebeurtenis optreedt die zowel de primaire als de replicacache uitschakelt, wordt de cache gereconstrueerd met behulp van de meest recente momentopname. Meer informatie over de [voor-](https://redis.io/topics/persistence#rdb-advantages) en [nadelen](https://redis.io/topics/persistence#rdb-disadvantages) van RDB-persistentie.
* **AOF-persistentie** - Wanneer aof -persistentie (Alleen toevoegen aan bestand) is geconfigureerd, slaat Azure Cache voor Redis elke schrijfbewerking op in een logboek dat ten minste eenmaal per seconde wordt opgeslagen in een Azure Storage-account. Als er een catastrofale gebeurtenis optreedt die zowel de primaire als de replicacache uitschakelt, wordt de cache gereconstrueerd met behulp van de opgeslagen schrijfbewerkingen. Meer informatie over de [voor-](https://redis.io/topics/persistence#aof-advantages) en [nadelen](https://redis.io/topics/persistence#aof-disadvantages) van AOF persistentie.

Persistentie schrijft Redis-gegevens in een Azure Storage-account dat u bezit en beheert. U configureren vanuit de **nieuwe Azure-cache voor redis-blade** tijdens het maken van de cache en in het **resourcemenu** voor bestaande premiumcaches.

> [!NOTE]
> 
> Azure Storage versleutelt automatisch gegevens wanneer deze worden gehandhaafd. U uw eigen sleutels gebruiken voor de encryptie. Zie [Door de klant beheerde sleutels met Azure Key Vault](/azure/storage/common/storage-service-encryption)voor meer informatie.
> 
> 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Zodra een premium prijscategorie is geselecteerd, klikt u op **Persistentie van Redis**.

![De persistentie van Redis][redis-cache-persistence]

In de stappen in de volgende sectie wordt beschreven hoe u de persistentie van Redis op uw nieuwe premiumcache configureren. Zodra de persistentie van Redis is geconfigureerd, klikt u op **Maken** om uw nieuwe premium cache met Redis-persistentie te maken.

## <a name="enable-redis-persistence"></a>Enable Redis persistentie

Redis persistentie is ingeschakeld op de **Data persistentie** blade door te kiezen voor **rdb** of **AOF** persistentie. Voor nieuwe caches is dit blad toegankelijk tijdens het cachecreatieproces, zoals beschreven in de vorige sectie. Voor bestaande caches is het **persistentieblad gegevens** toegankelijk vanuit het **menu Resource** voor uw cache.

![Redis-instellingen][redis-cache-settings]


## <a name="configure-rdb-persistence"></a>RDB-persistentie configureren

Als u RDB-persistentie wilt inschakelen, klikt u op **RDB**. Als u rdb-persistentie voor een eerder ingeschakelde premiumcache wilt uitschakelen, klikt u op **Uitgeschakeld**.

![Redis RDB persistentie][redis-cache-rdb-persistence]

Als u het back-upinterval wilt configureren, selecteert u een **back-upfrequentie** in de vervolgkeuzelijst. Keuzes zijn **15 minuten,** **30 minuten,** **60 minuten,** **6 uur,** **12 uur**en **24 uur**. Dit interval begint af te tellen nadat de vorige back-upbewerking is voltooid en wanneer deze is verstreken, wordt een nieuwe back-up gestart.

Klik **op Opslagaccount** om het opslagaccount te selecteren dat u wilt gebruiken en kies de **primaire sleutel** of **secundaire sleutel** die u wilt gebruiken in de vervolgkeuzelijst **Opslagsleutel.** U moet een opslagaccount kiezen in dezelfde regio als de cache en een **Premium Storage-account** wordt aanbevolen omdat de premium-opslag een hogere doorvoer heeft. 

> [!IMPORTANT]
> Als de opslagsleutel voor uw persistentieaccount opnieuw wordt gegenereerd, moet u de gewenste sleutel opnieuw configureren vanuit de vervolgkeuzelijst **Opslagsleutel.**
> 
> 

Klik op **OK** om de persistentieconfiguratie op te slaan.

De volgende back-up (of eerste back-up voor nieuwe caches) wordt gestart zodra het back-upfrequentieinterval is verstreken.

## <a name="configure-aof-persistence"></a>AOF-persistentie configureren

Als u AOF-persistentie wilt inschakelen, klikt u op **AOF**. Als u AOF-persistentie voor een eerder ingeschakelde premiumcache wilt uitschakelen, klikt u op **Uitgeschakeld**.

![Redis AOF persistentie][redis-cache-aof-persistence]

Als u AOF-persistentie wilt configureren, geeft u een **first storage-account op**. Dit opslagaccount moet zich in dezelfde regio bevinden als de cache en een **Premium Storage-account** wordt aanbevolen omdat premium-opslag een hogere doorvoer heeft. U optioneel een extra opslagaccount met de naam **Second Storage Account**configureren. Als een tweede opslagaccount is geconfigureerd, worden de schrijfbewerkingen naar de replicacache naar dit tweede opslagaccount geschreven. Kies voor elk geconfigureerd opslagaccount de **primaire sleutel** of **secundaire sleutel** die u wilt gebruiken in de vervolgkeuzelijst **Opslagsleutel.** 

> [!IMPORTANT]
> Als de opslagsleutel voor uw persistentieaccount opnieuw wordt gegenereerd, moet u de gewenste sleutel opnieuw configureren vanuit de vervolgkeuzelijst **Opslagsleutel.**
> 
> 

Wanneer AOF-persistentie is ingeschakeld, worden schrijfbewerkingen naar de cache opgeslagen in het aangewezen opslagaccount (of accounts als u een tweede opslagaccount hebt geconfigureerd). In het geval van een catastrofale fout die zowel de primaire als de replicacache naar beneden haalt, wordt het opgeslagen AOF-logboek gebruikt om de cache opnieuw op te bouwen.

## <a name="persistence-faq"></a>Veelgestelde vragen over persistentie
De volgende lijst bevat antwoorden op veelgestelde vragen over Azure Cache voor de persistentie van Redis.

* [Kan ik persistentie inschakelen voor een eerder gemaakte cache?](#can-i-enable-persistence-on-a-previously-created-cache)
* [Kan ik tegelijkertijd AOF en RDB-persistentie inschakelen?](#can-i-enable-aof-and-rdb-persistence-at-the-same-time)
* [Welk persistentiemodel moet ik kiezen?](#which-persistence-model-should-i-choose)
* [Wat gebeurt er als ik heb geschaald naar een ander formaat en een back-up is hersteld die is gemaakt vóór de schaalbewerking?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)


### <a name="rdb-persistence"></a>RDB persistentie
* [Kan ik de RDB-back-upfrequentie wijzigen nadat ik de cache heb gemaakt?](#can-i-change-the-rdb-backup-frequency-after-i-create-the-cache)
* [Waarom als ik een RDB back-up frequentie van 60 minuten is er meer dan 60 minuten tussen back-ups?](#why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups)
* [Wat gebeurt er met de oude RDB back-ups wanneer een nieuwe back-up wordt gemaakt?](#what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made)

### <a name="aof-persistence"></a>AOF persistentie
* [Wanneer moet ik een tweede opslagaccount gebruiken?](#when-should-i-use-a-second-storage-account)
* [Heeft AOF persistentie invloed op de hele, latentie of prestaties van mijn cache?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)
* [Hoe kan ik het tweede opslagaccount verwijderen?](#how-can-i-remove-the-second-storage-account)
* [Wat is een herschrijven en hoe beïnvloedt het mijn cache?](#what-is-a-rewrite-and-how-does-it-affect-my-cache)
* [Wat kan ik verwachten bij het schalen van een cache met AOF ingeschakeld?](#what-should-i-expect-when-scaling-a-cache-with-aof-enabled)
* [Hoe worden mijn AOF-gegevens georganiseerd in de opslag?](#how-is-my-aof-data-organized-in-storage)


### <a name="can-i-enable-persistence-on-a-previously-created-cache"></a>Kan ik persistentie inschakelen voor een eerder gemaakte cache?
Ja, Redis-persistentie kan zowel bij het maken van cache als op bestaande premium caches worden geconfigureerd.

### <a name="can-i-enable-aof-and-rdb-persistence-at-the-same-time"></a>Kan ik tegelijkertijd AOF en RDB-persistentie inschakelen?

Nee, u alleen RDB of AOF inschakelen, maar niet beide tegelijk.

### <a name="which-persistence-model-should-i-choose"></a>Welk persistentiemodel moet ik kiezen?

AOF-persistentie slaat elke schrijf naar een logboek op, wat enige invloed heeft op de doorvoer, in vergelijking met RDB-persistentie, waardoor back-ups worden opgeslagen op basis van het geconfigureerde back-upinterval, met minimale impact op de prestaties. Kies AOF-persistentie als uw primaire doel is om gegevensverlies te minimaliseren en u een daling van de doorvoer voor uw cache verwerken. Kies RDB-persistentie als u een optimale doorvoer op uw cache wilt behouden, maar toch een mechanisme wilt voor gegevensherstel.

* Meer informatie over de [voor-](https://redis.io/topics/persistence#rdb-advantages) en [nadelen](https://redis.io/topics/persistence#rdb-disadvantages) van RDB-persistentie.
* Meer informatie over de [voor-](https://redis.io/topics/persistence#aof-advantages) en [nadelen](https://redis.io/topics/persistence#aof-disadvantages) van AOF persistentie.

Zie Voor meer informatie over de prestaties bij het gebruik van AOF-persistentie [invloed hebben op de hele, latentie of prestaties van mijn cache?](#does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache)

### <a name="what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation"></a>Wat gebeurt er als ik heb geschaald naar een ander formaat en een back-up is hersteld die is gemaakt vóór de schaalbewerking?

Voor zowel RDB als AOF persistentie:

* Als u hebt geschaald naar een groter formaat, is er geen impact.
* Als u hebt geschaald naar een kleiner formaat en u een aangepaste [databasesinstelling](cache-configure.md#databases) hebt die groter is dan de limiet voor [databases](cache-configure.md#databases) voor uw nieuwe grootte, worden gegevens in die databases niet hersteld. Zie [Wordt mijn aangepaste databases-instelling beïnvloed tijdens het schalen voor](cache-how-to-scale.md#is-my-custom-databases-setting-affected-during-scaling) meer informatie?
* Als u hebt geschaald naar een kleiner formaat, en er is niet genoeg ruimte in de kleinere omvang om alle gegevens van de laatste back-up te houden, zullen sleutels worden uitgezet tijdens het herstelproces, meestal met behulp van de [allkeys-lru](https://redis.io/topics/lru-cache) uitzetting beleid.

### <a name="can-i-change-the-rdb-backup-frequency-after-i-create-the-cache"></a>Kan ik de RDB-back-upfrequentie wijzigen nadat ik de cache heb gemaakt?
Ja, u de back-upfrequentie voor RDB-persistentie op het **persistentieblad gegevens** wijzigen. Zie Persistentie van Redis configureren voor instructies.

### <a name="why-if-i-have-an-rdb-backup-frequency-of-60-minutes-there-is-more-than-60-minutes-between-backups"></a>Waarom als ik een RDB back-up frequentie van 60 minuten is er meer dan 60 minuten tussen back-ups?
Het rdb-interval voor back-ups voor persistentie wordt pas gestart nadat het vorige back-upproces is voltooid. Als de back-upfrequentie 60 minuten bedraagt en het duurt 15 minuten om een back-upproces te voltooien, wordt de volgende back-up pas 75 minuten na de begintijd van de vorige back-up gestart.

### <a name="what-happens-to-the-old-rdb-backups-when-a-new-backup-is-made"></a>Wat gebeurt er met de oude RDB back-ups wanneer een nieuwe back-up wordt gemaakt?
Alle RDB-persistentiebacks, behalve de meest recente, worden automatisch verwijderd. Deze verwijdering kan niet onmiddellijk gebeuren, maar oudere back-ups worden niet voor onbepaalde tijd voortgezet.


### <a name="when-should-i-use-a-second-storage-account"></a>Wanneer moet ik een tweede opslagaccount gebruiken?

U moet een tweede opslagaccount gebruiken voor AOF-persistentie wanneer u denkt dat u hoger dan verwachte ingestelde bewerkingen op de cache hebt ingesteld.  Als u het secundaire opslagaccount instelt, zorgt u ervoor dat uw cache de limieten voor opslagbandbreedte niet bereikt.

### <a name="does-aof-persistence-affect-throughout-latency-or-performance-of-my-cache"></a>Heeft AOF persistentie invloed op de hele, latentie of prestaties van mijn cache?

AOF persistentie beïnvloedt doorvoer met ongeveer 15% – 20% wanneer de cache onder de maximale belasting is (CPU en Server Load zowel onder de 90%). Er mogen geen latentieproblemen zijn wanneer de cache zich binnen deze limieten bevindt. De cache bereikt deze limieten echter sneller met AOF ingeschakeld.

### <a name="how-can-i-remove-the-second-storage-account"></a>Hoe kan ik het tweede opslagaccount verwijderen?

U het Secundaire opslagaccount voor persistentie van AOF verwijderen door het tweede opslagaccount in te stellen als het zelfde opslagaccount. Zie [AOF-persistentie configureren](#configure-aof-persistence)voor instructies .

### <a name="what-is-a-rewrite-and-how-does-it-affect-my-cache"></a>Wat is een herschrijven en hoe beïnvloedt het mijn cache?

Wanneer het AOF-bestand groot genoeg wordt, wordt een herschrijven automatisch in de wachtrij geplaatst in de cache. Het herschrijven wijzigt het formaat van het AOF-bestand met de minimale set bewerkingen die nodig zijn om de huidige gegevensset te maken. Verwacht tijdens herschrijft eerder prestatielimieten, vooral bij het omgaan met grote gegevenssets. Herschrijft komen minder vaak voor als het AOF-bestand groter wordt, maar zal een aanzienlijke hoeveelheid tijd in beslag nemen wanneer het gebeurt.

### <a name="what-should-i-expect-when-scaling-a-cache-with-aof-enabled"></a>Wat kan ik verwachten bij het schalen van een cache met AOF ingeschakeld?

Als het AOF-bestand op het moment van schalen aanzienlijk groot is, verwacht u dan dat de schaalbewerking langer duurt dan verwacht, omdat het bestand opnieuw wordt geladen nadat de schaling is voltooid.

Zie [Wat gebeurt er als ik geschaald ben naar een andere grootte en er een back-up is hersteld die is gemaakt vóór de schalingbewerking?](#what-happens-if-i-have-scaled-to-a-different-size-and-a-backup-is-restored-that-was-made-before-the-scaling-operation)

### <a name="how-is-my-aof-data-organized-in-storage"></a>Hoe worden mijn AOF-gegevens georganiseerd in de opslag?

Gegevens die zijn opgeslagen in AOF-bestanden zijn onderverdeeld in blobs per knooppunt om de prestaties van het opslaan van de gegevens in opslag te verhogen. In de volgende tabel wordt weergegeven hoeveel paginablobs worden gebruikt voor elke prijscategorie:

| Premiumlaag | Blobs |
|--------------|-------|
| P1           | 4 per shard    |
| P2           | 8 per shard    |
| P3           | 16 per shard   |
| P4           | 20 per shard   |

Wanneer clustering is ingeschakeld, heeft elke shard in de cache zijn eigen set paginablobs, zoals aangegeven in de vorige tabel. Een P2-cache met drie shards verdeelt bijvoorbeeld het AOF-bestand over blobs van 24 pagina's (8 blobs per shard, met 3 scherven).

Na een herschrijven, twee sets van AOF-bestanden bestaan in de opslag. Herschrijft vindt plaats op de achtergrond en toevoegen aan de eerste set bestanden, terwijl bewerkingen die naar de cache worden verzonden tijdens de herschrijving aan de tweede set worden ingesteld. Een back-up wordt tijdelijk opgeslagen tijdens herschrijven in geval van een storing, maar wordt onmiddellijk verwijderd nadat een herschrijven is voltooid.


## <a name="next-steps"></a>Volgende stappen
Meer informatie over het gebruik van meer premium cachefuncties.

* [Inleiding tot de azure-cache voor de laag Redis Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-rdb-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-rdb-persistence.png

[redis-cache-aof-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-aof-persistence.png

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png
