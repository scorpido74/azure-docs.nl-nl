---
title: Voorbeelden voor Azure Cache voor Redis
description: 'Meer informatie over het gebruik van Azure Cache voor Redis met deze codevoorbeelden: verbinding maken met een cache, gegevens lezen en schrijven in een cache, ASP.NET Azure Cache voor Redis-providers.'
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: sample
ms.date: 01/23/2017
ms.openlocfilehash: 8057a7db3d1a2db0e51ec15e4966ed0b31c84226
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2020
ms.locfileid: "75433409"
---
# <a name="azure-cache-for-redis-samples"></a>Voorbeelden voor Azure Cache voor Redis
In dit onderwerp wordt een lijst met Azure-cache voor Redis-voorbeelden weergegeven, waarin scenario's worden opgenomen, zoals verbinding maken met een cache, het lezen en schrijven van gegevens van en naar een cache en het gebruik van de ASP.NET Azure Cache voor Redis-providers. Sommige van de voorbeelden zijn downloadbare projecten, en sommige bieden stapsgewijze richtlijnen en bevatten codefragmenten, maar koppelen niet aan een downloadbaar project.

## <a name="hello-world-samples"></a>Hallo wereld monsters
De voorbeelden in deze sectie tonen de basisprincipes van het verbinden met een Azure-cache voor redis-instantie en het lezen en schrijven van gegevens naar de cache met behulp van verschillende talen en Redis-clients.

Het [voorbeeld van Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld) laat zien hoe u verschillende cachebewerkingen uitvoert met de [StackExchange.Redis](https://github.com/StackExchange/StackExchange.Redis) .NET-client.

In dit voorbeeld ziet u hoe u:

* Verschillende verbindingsopties gebruiken
* Objecten van en naar de cache lezen en schrijven met synchrone en asynchrone bewerkingen
* Opdrachten voor Redis MGET/MSET gebruiken om waarden van opgegeven toetsen terug te sturen
* Uitvoeren van transactionele bewerkingen van Redis
* Werken met Redis-lijsten en gesorteerde sets
* .NET-objecten opslaan met JsonConvert-serialisators
* Redis-sets gebruiken om tagging te implementeren
* Werken met Redis-cluster

Zie voor meer informatie de [stackexchange.redis-documentatie](https://github.com/StackExchange/StackExchange.Redis) op GitHub en voor meer gebruiksscenario's ziet u de [eenheidstests van StackExchange.Redis.Tests.](https://github.com/StackExchange/StackExchange.Redis/tree/master/tests)

[Hoe u Azure Cache voor Redis met Python gebruiken,](cache-python-get-started.md) is te zien hoe u aan de slag met Azure Cache voor Redis met Python en de [redis-py-client.](https://github.com/andymccurdy/redis-py)

[Met .NET-objecten in de cache](cache-dotnet-how-to-use-azure-redis-cache.md#work-with-net-objects-in-the-cache) u op één manier .NET-objecten serialiseren, zodat u ze schrijven en lezen vanuit een Azure-cache voor bijvoorbeeld Redis. 

## <a name="use-azure-cache-for-redis-as-a-scale-out-backplane-for-aspnet-signalr"></a>Azure Cache voor Redis gebruiken als een uitschalen backplane voor ASP.NET SignalR
Het [Voorbeeld van Azure Cache voor Redis gebruiken als een Backplane schaal voor ASP.NET SignalR-voorbeeld](https://github.com/rustd/RedisSamples/tree/master/RedisAsSignalRBackplane) laat zien hoe u Azure Cache voor Redis gebruiken als backplane van SignalR. Zie [SignalR Scaleout with Redis](https://www.asp.net/signalr/overview/performance/scaleout-with-redis)voor meer informatie over backplane.

## <a name="azure-cache-for-redis-customer-query-sample"></a>Voorbeeld van azure-cache voor de klantquery van Redis
In dit voorbeeld wordt aangetoond dat de prestaties worden vergeleken tussen toegang tot gegevens uit een cache en toegang tot gegevens uit persistentieopslag. Deze steekproef heeft twee projecten.

* [Demo hoe Azure Cache voor Redis de prestaties kan verbeteren door gegevens te cachingen](https://github.com/rustd/RedisSamples/tree/master/RedisCacheCustomerQuerySample)
* [De database en cache voor de demo zaaien](https://github.com/rustd/RedisSamples/tree/master/SeedCacheForCustomerQuerySample)

## <a name="aspnet-session-state-and-output-caching"></a>ASP.NET Sessiestatus en uitvoercaching
In het voorbeeld [Azure Cache voor Redis gebruiken om ASP.NET SessionState en OutputCache op](https://github.com/rustd/RedisSamples/tree/master/SessionState_OutputCaching) te slaan, wordt uitgelegd hoe u Azure Cache voor Redis gebruiken om ASP.NET sessie- en uitvoercache op te slaan met behulp van de sessionState- en OutputCache-providers voor Redis.

## <a name="manage-azure-cache-for-redis-with-maml"></a>Azure-cache voor Redis beheren met MAML
Het voorbeeld [Azure Cache voor Redis beheren met Azure Management Libraries](https://github.com/rustd/RedisSamples/tree/master/ManageCacheUsingMAML) laat zien hoe u Azure Management Libraries gebruiken om uw cache te beheren (maken/ bijwerken/ verwijderen). 

## <a name="custom-monitoring-sample"></a>Voorbeeld van aangepaste bewaking
Het voorbeeld [van Access Azure Cache voor Redis-controlegegevens](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) laat zien hoe u toegang hebt tot bewakingsgegevens voor uw Azure-cache voor Redis buiten de Azure Portal.

## <a name="a-twitter-style-clone-written-using-php-and-redis"></a>Een Twitter-stijl kloon geschreven met behulp van PHP en Redis
De [Retwis](https://github.com/SyntaxC4-MSFT/retwis) monster is de Redis Hello World. Het is een minimale Twitter-stijl sociale netwerk kloon geschreven met behulp van Redis en PHP met behulp van de [Predis](https://github.com/nrk/predis) client. De broncode is ontworpen om zeer eenvoudig te zijn en tegelijkertijd verschillende Redis-gegevensstructuren weer te geven.

## <a name="bandwidth-monitor"></a>Bandbreedtemonitor
Met het voorbeeld [van de bandbreedtemonitor](https://github.com/JonCole/SampleCode/tree/master/BandWidthMonitor) u de bandbreedte die op de client wordt gebruikt, controleren. Als u de bandbreedte wilt meten, voert u het voorbeeld uit op de cacheclientmachine, voert u gesprekken naar de cache en observeert u de bandbreedte die wordt gerapporteerd door het voorbeeld van de bandbreedtemonitor.
