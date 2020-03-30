---
title: Redis-cli gebruiken met Azure Cache voor Redis
description: Meer informatie over het gebruik van *redis-cli.exe* als opdrachtregelhulpmiddel voor interactie met een Azure-cache voor Redis als client.
author: yegu-ms
ms.author: yegu
ms.service: cache
ms.topic: conceptual
ms.date: 03/22/2018
ms.openlocfilehash: a48e69f19db88c7823365964c2fe9c0629a078bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75412672"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Het gereedschap Opdrachtregel van Redis gebruiken met Azure Cache voor Redis

*redis-cli.exe* is een populair opdrachtregelhulpmiddel voor interactie met een Azure-cache voor Redis als client. Deze tool is ook beschikbaar voor gebruik met Azure Cache voor Redis.

De tool is beschikbaar voor Windows-platforms door de [Redis-opdrachtregeltools voor Windows](https://github.com/MSOpenTech/redis/releases/)te downloaden. 

Als u het opdrachtregelgereedschap op een ander platform wilt uitvoeren, downloadt u Azure Cache voor Redis van [https://redis.io/download](https://redis.io/download).

## <a name="gather-cache-access-information"></a>Toegangsgegevens voor cache verzamelen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U de informatie verzamelen die nodig is om toegang te krijgen tot de cache met behulp van drie methoden:

1. Azure CLI met [lijsttoetsen az redis](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Azure PowerShell met [Get-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. Azure Portal gebruiken.

In deze sectie haalt u de sleutels op uit de Azure-portal.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Toegang inschakelen voor redis-cli.exe

Met Azure Cache voor Redis is alleen de SSL-poort (6380) standaard ingeschakeld. Het `redis-cli.exe` opdrachtregelgereedschap biedt geen ondersteuning voor SSL. U hebt twee configuratieopties om het te gebruiken:

1. [Schakel de niet-SSL-poort in (6379)](cache-configure.md#access-ports) - **Deze configuratie wordt niet aanbevolen** omdat in deze configuratie de toegangssleutels in duidelijke tekst via TCP worden verzonden. Deze wijziging kan de toegang tot uw cache in gevaar brengen. Het enige scenario waarin u deze configuratie overwegen, is wanneer u alleen toegang hebt tot een testcache.

2. Stunnel [stunnel](https://www.stunnel.org/downloads.html)downloaden en installeren.

    Voer **de start van stunnel GUI uit** om de server te starten.

    Klik met de rechtermuisknop op het pictogram van de taakbalk voor de tunnelserver en klik op **Logboekvenster weergeven**.

    Klik in het menu venster van het stunnellogboek op **Configuratieconfiguratie** > **bewerken** om het huidige configuratiebestand te openen.

    Voeg de volgende vermelding voor *redis-cli.exe* toe onder de sectie **Servicedefinities.** Voeg de werkelijke cachenaam `yourcachename`in plaats van . 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Sla het configuratiebestand op en sluit deze. 
  
    Klik in het menu venster van het stunnellogboek op **Configuratieconfiguratie** > **opnieuw laden**.


## <a name="connect-using-the-redis-command-line-tool"></a>Maak verbinding met het gereedschap Redis-opdrachtregel.

Wanneer u de tunnel gebruikt, voert u *redis-cli.exe*uit en geeft u alleen uw *poort*door en *geeft u de toegangssleutel* (primair of secundair) door om verbinding te maken met de cache.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![stunnel met redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Als u een testcache met de **onveilige** niet-SSL-poort gebruikt, voert u `redis-cli.exe` uw *hostnaam,* *poort*en *toegangssleutel* (primair of secundair) uit om verbinding te maken met de testcache.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel met redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van de [Redis Console](cache-configure.md#redis-console) om opdrachten uit te geven.

