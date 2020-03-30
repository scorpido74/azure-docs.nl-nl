---
title: Redis-clustering configureren - Premium Azure-cache voor Redis
description: Meer informatie over het maken en beheren van Redis-clustering voor azure cache voor Azure-cache voor Redis-exemplaren
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 06/13/2018
ms.openlocfilehash: 761c464730096eba36bc7c04227745cf362e5cc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278036"
---
# <a name="how-to-configure-redis-clustering-for-a-premium-azure-cache-for-redis"></a>Redis-clustering configureren voor een Premium Azure-cache voor Redis
Azure Cache voor Redis heeft verschillende cache-aanbiedingen, die flexibiliteit bieden in de keuze van cachegrootte en functies, waaronder premium-laagfuncties zoals clustering, persistentie en ondersteuning voor virtueel netwerk. In dit artikel wordt beschreven hoe u clustering configureert in een premium Azure-cache voor redis-instantie.

Zie [Inleiding tot de Azure-cache voor Redis Premium-laag voor](cache-premium-tier-intro.md)meer informatie over andere functies met een premiumcache.

## <a name="what-is-redis-cluster"></a>Wat is Redis Cluster?
Azure Cache for Redis biedt Redis-cluster zoals [geïmplementeerd in Redis](https://redis.io/topics/cluster-tutorial). Met Redis Cluster krijgt u de volgende voordelen: 

* De mogelijkheid om uw gegevensset automatisch te splitsen over meerdere knooppunten. 
* De mogelijkheid om bewerkingen voort te zetten wanneer een subset van de knooppunten storingen ondervindt of niet in staat is om te communiceren met de rest van het cluster. 
* Meer doorvoer: doorvoer neemt lineair toe naarmate u het aantal shards verhoogt. 
* Meer geheugengrootte: Verhoogt lineair naarmate u het aantal shards verhoogt.  

Clustering verhoogt het aantal beschikbare verbindingen voor een geclusterde cache niet. Zie [Wat azure cache voor Redis-aanbieding en grootte moet ik gebruiken](cache-faq.md#what-azure-cache-for-redis-offering-and-size-should-i-use) voor meer informatie over grootte, doorvoer en bandbreedte met premium caches?

In Azure wordt het Redis-cluster aangeboden als een primair/replicamodel waarbij elke shard een primaire/replica-paar heeft met replicatie waarbij de replicatie wordt beheerd door Azure Cache voor Redis-service. 

## <a name="clustering"></a>Clustering
Clustering is ingeschakeld in de **nieuwe Azure-cache voor redis-blade** tijdens het maken van cache. 

[!INCLUDE [redis-cache-create](../../includes/redis-cache-premium-create.md)]

Clustering is geconfigureerd op het **Redis** Cluster-blad.

![Clustering][redis-cache-clustering]

U maximaal 10 shards in het cluster hebben. Klik **op Ingeschakeld** en schuif de schuifregelaar of typ een getal tussen 1 en 10 voor **Shardtelling** en klik op **OK**.

Elke shard is een primaire/replica-cachepaar dat wordt beheerd door Azure en de totale grootte van de cache wordt berekend door het aantal shards te vermenigvuldigen met de cachegrootte die is geselecteerd in de prijscategorie. 

![Clustering][redis-cache-clustering-selected]

Zodra de cache is gemaakt, maakt u verbinding met deze en gebruikt u deze net als een niet-geclusterde cache, en distribueert Redis de gegevens over de cachescherven. Als diagnostische gegevens zijn [ingeschakeld,](cache-how-to-monitor.md#enable-cache-diagnostics)worden metrische gegevens afzonderlijk vastgelegd voor elke shard en kunnen ze worden [weergegeven](cache-how-to-monitor.md) in de Azure-cache voor het Redis-blad. 

> [!NOTE]
> 
> Er zijn enkele kleine verschillen vereist in uw clienttoepassing wanneer clustering is geconfigureerd. Zie [Moet ik wijzigingen aanbrengen in mijn clienttoepassing om clustering te gebruiken voor](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering) meer informatie?
> 
> 

Zie het [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) gedeelte van het voorbeeld [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) voor voorbeeldcode voor het werken met clustering met de StackExchange.Redis-client.

<a name="cluster-size"></a>

## <a name="change-the-cluster-size-on-a-running-premium-cache"></a>De clustergrootte wijzigen in een premiumcache
Als u de clustergrootte wilt wijzigen in een draaiende premiumcache met clustering ingeschakeld, klikt u op **Clustergrootte** in het **menu Resource**.

![Redis-clustergrootte][redis-cache-redis-cluster-size]

Als u de clustergrootte wilt wijzigen, gebruikt u de schuifregelaar of typt u een getal tussen 1 en 10 in het tekstvak **Shardtellen** en klikt u op **OK** om op te slaan.

Als u de clustergrootte vergroot, wordt de maximale doorvoer en cachegrootte vergroot. Als u de clustergrootte verhoogt, wordt de maximumsnelheid niet verhoogd. verbindingen beschikbaar voor clients.

> [!NOTE]
> Als u een cluster schaalt, wordt de opdracht [MIGREREN](https://redis.io/commands/migrate) uitgevoerd, wat een dure opdracht is, dus voor minimale impact u overwegen deze bewerking uit te voeren tijdens niet-piekuren. Tijdens het migratieproces ziet u een piek in de serverbelasting. Het schalen van een cluster is een langlopend proces en de hoeveelheid tijd die nodig is, is afhankelijk van het aantal sleutels en de grootte van de waarden die aan deze sleutels zijn gekoppeld.
> 
> 

## <a name="clustering-faq"></a>Veelgestelde vragen over clusteren
De volgende lijst bevat antwoorden op veelgestelde vragen over Azure Cache for Redis-clustering.

* [Moet ik wijzigingen aanbrengen in mijn clienttoepassing om clustering te gebruiken?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)
* [Hoe worden sleutels verdeeld in een cluster?](#how-are-keys-distributed-in-a-cluster)
* [Wat is de grootste cachegrootte die ik kan maken?](#what-is-the-largest-cache-size-i-can-create)
* [Ondersteunen alle Redis-clients clustering?](#do-all-redis-clients-support-clustering)
* [Hoe maak ik verbinding met mijn cache wanneer clustering is ingeschakeld?](#how-do-i-connect-to-my-cache-when-clustering-is-enabled)
* [Kan ik rechtstreeks verbinding maken met de afzonderlijke scherven van mijn cache?](#can-i-directly-connect-to-the-individual-shards-of-my-cache)
* [Kan ik clustering configureren voor een eerder gemaakte cache?](#can-i-configure-clustering-for-a-previously-created-cache)
* [Kan ik clustering configureren voor een basis- of standaardcache?](#can-i-configure-clustering-for-a-basic-or-standard-cache)
* [Kan ik clustering gebruiken met de providers van ASP.NET-sessiestatus en uitvoercaching?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)
* [Ik krijg MOVE-uitzonderingen bij het gebruik van StackExchange.Redis en clustering, wat moet ik doen?](#i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do)

### <a name="do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering"></a>Moet ik wijzigingen aanbrengen in mijn clienttoepassing om clustering te gebruiken?
* Wanneer clustering is ingeschakeld, is alleen database 0 beschikbaar. Als uw clienttoepassing meerdere databases gebruikt en probeert deze te lezen of te schrijven naar een andere database dan 0, wordt de volgende uitzondering gegenereerd. `Unhandled Exception: StackExchange.Redis.RedisConnectionException: ProtocolFailure on GET --->` `StackExchange.Redis.RedisCommandException: Multiple databases are not supported on this server; cannot switch to database: 6`
  
  Zie [Clusterspecificatie van Redis - Geïmplementeerde subset voor](https://redis.io/topics/cluster-spec#implemented-subset)meer informatie.
* Als u [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/)gebruikt, moet u 1.0.481 of hoger gebruiken. U maakt verbinding met de cache via dezelfde [eindpunten, poorten en sleutels](cache-configure.md#properties) die u gebruikt wanneer u verbinding maakt met een cache die geen clustering heeft ingeschakeld. Het enige verschil is dat alle leest en schrijft moet worden gedaan om database 0.
  
  * Andere klanten kunnen verschillende vereisten hebben. Zie [Ondersteunen alle Redis-clients clustering?](#do-all-redis-clients-support-clustering)
* Als uw toepassing meerdere belangrijke bewerkingen gebruikt die in één opdracht zijn gebatched, moeten alle sleutels zich in dezelfde shard bevinden. Zie Hoe worden sleutels verdeeld [in een cluster als](#how-are-keys-distributed-in-a-cluster) u sleutels in dezelfde shard wilt vinden?
* Als u Redis ASP.NET Session State provider gebruikt, moet u 2.0.1 of hoger gebruiken. Zie [Kan ik clustering gebruiken met de providers van de ASP.NET-sessiestatus en uitvoercaching?](#can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers)

### <a name="how-are-keys-distributed-in-a-cluster"></a>Hoe worden sleutels verdeeld in een cluster?
Volgens de Redis [Keys distributiemodel](https://redis.io/topics/cluster-spec#keys-distribution-model) documentatie: De sleutelruimte is opgesplitst in 16384 slots. Elke sleutel wordt gehasht en toegewezen aan een van deze sleuven, die worden verdeeld over de knooppunten van het cluster. U configureren welk deel van de sleutel is gehasht om ervoor te zorgen dat meerdere sleutels zich in dezelfde shard bevinden met hash-tags.

* Sleutels met een hash-tag - als een `{` `}`deel van de sleutel is ingesloten en alleen dat deel van de sleutel wordt gehasht met het oog op het bepalen van de hash-sleuf van een sleutel. De volgende 3 toetsen bevinden zich bijvoorbeeld in `{key}1`dezelfde `{key}2`shard: , en `{key}3` omdat alleen het `key` deel van de naam is gehasht. Zie [Sleutels hashtags](https://redis.io/topics/cluster-spec#keys-hash-tags)voor een volledige lijst met specificaties van de sleutels hash tag .
* Sleutels zonder hashtag - de volledige sleutelnaam wordt gebruikt voor hashing. Dit resulteert in een statistisch gelijkmatige verdeling over de scherven van de cache.

Voor de beste prestaties en doorvoer raden we u aan de toetsen gelijkmatig te verdelen. Als u sleutels met een hashtag gebruikt, is het de verantwoordelijkheid van de toepassing om ervoor te zorgen dat de sleutels gelijkmatig worden verdeeld.

Zie [Sleutels distributiemodel](https://redis.io/topics/cluster-spec#keys-distribution-model), [Redis Cluster-gegevenssharding](https://redis.io/topics/cluster-tutorial#redis-cluster-data-sharding)en [Keys hashtags](https://redis.io/topics/cluster-spec#keys-hash-tags)voor meer informatie .

Zie het [clustering.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/Clustering.cs) gedeelte van het voorbeeld [van Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) voor voorbeeldcode voor het groeperen en lokaliseren van sleutels in dezelfde shard met de StackExchange.Redis-client.

### <a name="what-is-the-largest-cache-size-i-can-create"></a>Wat is de grootste cachegrootte die ik kan maken?
De grootste premium cache grootte is 120 GB. U tot 10 shards maken, waardoor u een maximale grootte van 1,2 TB GB hebt. Als u een groter formaat nodig hebt, u [meer aanvragen.](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase) Zie [Azure Cache for Redis Pricing voor](https://azure.microsoft.com/pricing/details/cache/)meer informatie .

### <a name="do-all-redis-clients-support-clustering"></a>Ondersteunen alle Redis-clients clustering?
Niet alle klanten ondersteunen Redis clustering! Controleer de documentatie voor de bibliotheek die u gebruikt, om te controleren of u een bibliotheek en versie gebruikt die clustering ondersteunen. StackExchange.Redis is een bibliotheek die clustering ondersteunt, in de nieuwere versies. Zie Het [gedeelte Spelen met de cluster](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) van de [zelfstudie redis-](https://redis.io/topics/cluster-tutorial)en clusterbeheer voor meer informatie over andere clients. 

Het Redis-clusteringprotocol vereist dat elke client rechtstreeks verbinding maakt met elke shard in de clustermodus en definieert ook nieuwe foutreacties zoals 'MOVED' na 'CROSSSLOTS'. Als u een client probeert te gebruiken die clustering niet ondersteunt met een cache in de clustermodus, kan dit leiden tot veel [uitzonderingen op de omleiding van VERPLAATST](https://redis.io/topics/cluster-spec#moved-redirection)of uw toepassing gewoon verbreken als u multi-key-aanvragen met meerdere sleufsinhet geanders doet.

> [!NOTE]
> Als u StackExchange.Redis als klant gebruikt, moet u ervoor zorgen dat u de nieuwste versie van [StackExchange.Redis](https://www.nuget.org/packages/StackExchange.Redis/) 1.0.481 of hoger gebruikt om te clusteren om correct te werken. Als u problemen hebt met verplaatsingsuitzonderingen, [raadpleegt u uitzonderingen verplaatsen](#move-exceptions) voor meer informatie.
>

### <a name="how-do-i-connect-to-my-cache-when-clustering-is-enabled"></a>Hoe maak ik verbinding met mijn cache wanneer clustering is ingeschakeld?
U verbinding maken met uw cache via dezelfde [eindpunten,](cache-configure.md#properties) [poorten](cache-configure.md#properties)en [sleutels](cache-configure.md#access-keys) die u gebruikt wanneer u verbinding maakt met een cache die geen clustering heeft ingeschakeld. Redis beheert de clustering op de backend, zodat u deze niet hoeft te beheren vanuit uw client.

### <a name="can-i-directly-connect-to-the-individual-shards-of-my-cache"></a>Kan ik rechtstreeks verbinding maken met de afzonderlijke scherven van mijn cache?
Het clusterprotocol vereist dat de client de juiste shardverbindingen maakt. Dus de klant moet dit goed doen voor je. Met dat gezegd, elke scherf bestaat uit een primaire / replica cache paar, gezamenlijk bekend als een cache-instantie. U verbinding maken met deze cache-exemplaren met behulp van het hulpprogramma redis-cli in de [onstabiele](https://redis.io/download) tak van de Redis-opslagplaats op GitHub. Deze versie implementeert basisondersteuning `-c` wanneer deze met de switch wordt gestart. Zie [Spelen met het cluster](https://redis.io/topics/cluster-tutorial#playing-with-the-cluster) [https://redis.io](https://redis.io) op in de zelfstudie van het [Redis-cluster](https://redis.io/topics/cluster-tutorial)voor meer informatie.

Voor niet-ssl gebruikt u de volgende opdrachten.

    Redis-cli.exe –h <<cachename>> -p 13000 (to connect to instance 0)
    Redis-cli.exe –h <<cachename>> -p 13001 (to connect to instance 1)
    Redis-cli.exe –h <<cachename>> -p 13002 (to connect to instance 2)
    ...
    Redis-cli.exe –h <<cachename>> -p 1300N (to connect to instance N)

Voor ssl, `1300N` `1500N`vervangen door .

### <a name="can-i-configure-clustering-for-a-previously-created-cache"></a>Kan ik clustering configureren voor een eerder gemaakte cache?
Ja. Zorg er eerst voor dat uw cache premium is, door te schalen als dat niet het is. Vervolgens moet u de clusterconfiguratieopties kunnen zien, inclusief een optie om het cluster in te schakelen. U de clustergrootte wijzigen nadat de cache is gemaakt of nadat u voor het eerst clustering hebt ingeschakeld.

   >[!IMPORTANT]
   >U het inschakelen van clustering niet ongedaan maken. En een cache met clustering ingeschakeld en slechts een scherf gedraagt zich *anders* dan een cache van dezelfde grootte *zonder* clustering.

### <a name="can-i-configure-clustering-for-a-basic-or-standard-cache"></a>Kan ik clustering configureren voor een basis- of standaardcache?
Clustering is alleen beschikbaar voor premium caches.

### <a name="can-i-use-clustering-with-the-redis-aspnet-session-state-and-output-caching-providers"></a>Kan ik clustering gebruiken met de providers van ASP.NET-sessiestatus en uitvoercaching?
* **Redis Output Cache provider** - geen wijzigingen nodig.
* **Redis Session State provider** - om clustering te gebruiken, moet u [RedisSessionStateProvider](https://www.nuget.org/packages/Microsoft.Web.RedisSessionStateProvider) 2.0.1 of hoger gebruiken of een uitzondering wordt gegooid. Dit is een brekende verandering; Zie [v2.0.0 Breaking Change Details voor](https://github.com/Azure/aspnet-redis-providers/wiki/v2.0.0-Breaking-Change-Details)meer informatie.

<a name="move-exceptions"></a>

### <a name="i-am-getting-move-exceptions-when-using-stackexchangeredis-and-clustering-what-should-i-do"></a>Ik krijg MOVE-uitzonderingen bij het gebruik van StackExchange.Redis en clustering, wat moet ik doen?
Als u StackExchange.Redis gebruikt `MOVE` en uitzonderingen ontvangt bij het gebruik van clustering, moet u ervoor zorgen dat u [StackExchange.Redis 1.1.603](https://www.nuget.org/packages/StackExchange.Redis/) of hoger gebruikt. Zie [De cacheclients configureren](cache-dotnet-how-to-use-azure-redis-cache.md#configure-the-cache-clients)voor instructies voor het configureren van uw .NET-toepassingen om StackExchange.Redis te gebruiken.

## <a name="next-steps"></a>Volgende stappen
Meer informatie over het gebruik van meer premium cachefuncties.

* [Inleiding tot de azure-cache voor de laag Redis Premium](cache-premium-tier-intro.md)

<!-- IMAGES -->

[redis-cache-clustering]: ./media/cache-how-to-premium-clustering/redis-cache-clustering.png

[redis-cache-clustering-selected]: ./media/cache-how-to-premium-clustering/redis-cache-clustering-selected.png

[redis-cache-redis-cluster-size]: ./media/cache-how-to-premium-clustering/redis-cache-redis-cluster-size.png
