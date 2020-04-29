---
title: Voorbeelden voor Azure Cache voor Redis
description: 'Meer informatie over het gebruik van Azure cache voor redis met de volgende code voorbeelden: verbinding maken met een cache, gegevens lezen en schrijven in een cache, ASP.NET Azure cache voor redis-providers.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: sample
ms.date: 01/23/2017
ms.openlocfilehash: 8057a7db3d1a2db0e51ec15e4966ed0b31c84226
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75433409"
---
# <a name="azure-cache-for-redis-samples"></a>Voorbeelden voor Azure Cache voor Redis
In dit onderwerp vindt u een lijst met Azure cache voor redis-voor beelden, waarbij scenario's zoals het maken van verbinding met een cache, het lezen en schrijven van gegevens van en naar een cache en het ASP.NET Azure cache voor redis-providers worden gebruikt. Sommige van de voor beelden zijn Download bare projecten en sommige bieden stapsgewijze instructies en bevatten code fragmenten, maar ze kunnen niet worden gekoppeld aan een downloadbaar project.

## <a name="hello-world-samples"></a>Hallo wereld-voor beelden
In de voor beelden in deze sectie ziet u de basis beginselen van het maken van verbinding met een Azure-cache voor redis-exemplaar en het lezen en schrijven van gegevens naar de cache met behulp van diverse talen en redis-clients.

Het [Hello World](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) -voor beeld laat zien hoe u verschillende cache bewerkingen kunt uitvoeren met de .net-client [stack Exchange. redis](https://github.com/StackExchange/StackExchange.Redis) .

Dit voor beeld laat zien hoe u:

* Verschillende verbindings opties gebruiken
* Objecten van en naar de cache lezen en schrijven met synchrone en asynchrone bewerkingen
* Redis MGET/MSET-opdrachten gebruiken om waarden van opgegeven sleutels te retour neren
* Redis transactionele bewerkingen uitvoeren
* Werken met redis-lijsten en gesorteerde sets
* .NET-objecten opslaan met JsonConvert-serialisatiefunctie
* Redis sets gebruiken voor het implementeren van Tags
* Werken met redis-cluster

Zie de documentatie van [stack Exchange. redis](https://github.com/StackExchange/StackExchange.Redis) op github voor meer informatie en voor meer gebruiks scenario's raadpleegt u de eenheids tests voor [stack Exchange. redis. tests](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests) .

Het [gebruik van Azure cache voor redis met python](cache-python-get-started.md) laat zien hoe u aan de slag kunt gaan met Azure cache voor redis met python en de [redis-py-](https://github.com/andymccurdy/redis-py) client.

[Werken met .net-objecten in de cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) toont u één manier om .net-objecten te serialiseren zodat u deze kunt schrijven en lezen vanuit een Azure-cache voor redis-instantie. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Azure cache gebruiken voor redis als een grootschalige backplane voor ASP.NET-Signa lering
Het [gebruik van Azure cache voor redis als een scale-out-backplane voor ASP.net-signaal voor](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) beeld laat zien hoe u Azure cache kunt gebruiken voor redis als signaale backplane. Zie voor meer informatie over backplane [seingevings uitschalen met redis](https://www.asp.net/signalr/overview/performance/scaleout-with-redis).

## <a name="azure-cache-for-redis-customer-query-sample"></a>Voor beeld van Azure cache voor redis-klant query
Dit voor beeld laat zien hoe u de prestaties vergelijkt van toegang tot gegevens uit een cache en toegang hebt tot gegevens uit de persistentie opslag. Dit voor beeld heeft twee projecten.

* [Demo hoe Azure cache voor redis de prestaties kan verbeteren door gegevens in de cache op te slaan](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [De data base en cache voor de demo seeden](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>ASP.NET-sessie status en uitvoer cache
Het [gebruik van Azure cache voor redis voor het opslaan van ASP.net SessionState-en OutputCache](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) -voor beeld laat zien hoe u Azure cache voor redis kunt gebruiken om ASP.net-sessie-en uitvoer cache op te slaan met behulp van de SessionState-en OutputCache-providers voor redis.

## <a name="manage-azure-cache-for-redis-with-maml"></a>Azure-cache beheren voor redis met MAML
In het [voor beeld voor het beheren van Azure-cache voor redis met behulp van Azure Management libraries](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) wordt gedemonstreerd hoe u Azure-beheer bibliotheken kunt gebruiken voor het beheren van uw cache (maken/bijwerken/verwijderen). 

## <a name="custom-monitoring-sample"></a>Voor beeld van aangepaste bewaking
In de Azure- [cache voor redis-bewakings gegevens wordt](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) gedemonstreerd hoe u toegang kunt krijgen tot bewakings gegevens voor uw Azure-cache voor redis buiten Azure Portal.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Een Twitter-stijl kloon geschreven met behulp van PHP en redis
Het [Retwis](https://github.com/SyntaxC4-MSFT/retwis) -voor beeld is de Redis-Hallo wereld. Het is een minimale Twitter-stijl sociale netwerk kloon geschreven met redis en PHP met behulp van de [Predis](https://github.com/nrk/predis) -client. De bron code is ontworpen om zeer eenvoudig te zijn en tegelijkertijd om verschillende redis-gegevens structuren weer te geven.

## <a name="bandwidth-monitor"></a>Bandbreedte monitor
Met het voor beeld van [bandbreedte monitor](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) kunt u de band breedte bewaken die op de client wordt gebruikt. Als u de band breedte wilt meten, voert u het voor beeld op de cache-client computer uit, maakt u aanroepen naar de cache en bekijkt u de band breedte die wordt gerapporteerd door het voor beeld van de bandbreedte monitor.
