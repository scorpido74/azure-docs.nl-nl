---
title: Redis-CLI gebruiken met Azure cache voor redis
description: Meer informatie over het gebruik van redis-CLI met Azure cache voor redis.
author: yegu-ms
ms.service: cache
ms.topic: conceptual
ms.date: 03/22/2018
ms.author: yegu
ms.openlocfilehash: 510110a201e943b8ffce07fc7d0de1361264ae62
ms.sourcegitcommit: 5a8c65d7420daee9667660d560be9d77fa93e9c9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74122714"
---
# <a name="how-to-use-the-redis-command-line-tool-with-azure-cache-for-redis"></a>Het opdracht regel programma redis gebruiken met Azure cache voor redis

*redis-cli. exe* is een populair opdracht regel programma voor interactie met een Azure-cache voor redis als een-client. Dit hulp programma is ook beschikbaar voor gebruik met Azure cache voor redis.

Het hulp programma is beschikbaar voor Windows-platforms door de [redis-opdracht regel Programma's voor Windows](https://github.com/MSOpenTech/redis/releases/)te downloaden. 

Als u het opdracht regel programma wilt uitvoeren op een ander platform, downloadt u Azure-cache voor redis van [https://redis.io/download](https://redis.io/download).

## <a name="gather-cache-access-information"></a>Toegangs gegevens van cache verzamelen

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

U kunt de gegevens die nodig zijn voor toegang tot de cache verzamelen met behulp van drie methoden:

1. Azure CLI met [AZ redis List-Keys](https://docs.microsoft.com/cli/azure/redis?view=azure-cli-latest#az-redis-list-keys)
2. Azure PowerShell met [Get-AzRedisCacheKey](https://docs.microsoft.com/powershell/module/az.rediscache/Get-AzRedisCacheKey)
3. Het Azure Portal gebruiken.

In deze sectie haalt u de sleutels op uit het Azure Portal.

[!INCLUDE [redis-cache-create](../../includes/redis-cache-access-keys.md)]


## <a name="enable-access-for-redis-cliexe"></a>Toegang inschakelen voor redis-cli. exe

Met Azure cache voor redis is alleen de SSL-poort (6380) standaard ingeschakeld. Het opdracht regel programma `redis-cli.exe` biedt geen ondersteuning voor SSL. U hebt twee configuratie-opties om deze te gebruiken:

1. [De niet-SSL-poort (6379) inschakelen](cache-configure.md#access-ports) - **deze configuratie wordt niet aanbevolen** omdat in deze configuratie de toegangs sleutels worden verzonden via TCP als gewone tekst. Met deze wijziging kan de toegang tot uw cache worden aangetast. Het enige scenario waarin u rekening moet houden met deze configuratie is wanneer u alleen een test cache opent.

2. Down load en Installeer [stunnel](https://www.stunnel.org/downloads.html).

    Voer **STUNNEL GUI start** uit om de server te starten.

    Klik met de rechter muisknop op het taak balk pictogram voor de stunnel-server en klik op **logboek venster weer geven**.

    Klik in het menu van het logboek venster stunnel op **configuratie** > **configuratie bewerken** om het huidige configuratie bestand te openen.

    Voeg in de sectie **service definities** de volgende vermelding toe voor *redis-cli. exe* . Plaats de daad werkelijke cache naam in plaats van `yourcachename`. 

    ```
    [redis-cli]
    client = yes
    accept = 127.0.0.1:6380
    connect = yourcachename.redis.cache.windows.net:6380
    ```

    Sla het configuratie bestand op en sluit het. 
  
    Klik in het menu van het stunnel-logboek op **configuratie** > **configuratie opnieuw laden**.


## <a name="connect-using-the-redis-command-line-tool"></a>Verbinding maken met behulp van het opdracht regel programma redis.

Wanneer u stunnel gebruikt, voert u *redis-cli. exe*uit en geeft u alleen uw *poort*door en de *toegangs sleutel* (primair of secundair) om verbinding te maken met de cache.

```
redis-cli.exe -p 6380 -a YourAccessKey
```

![stunnel met redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-stunnel.png)

Als u een test cache gebruikt met de niet- **beveiligde** niet-SSL-poort, voert u `redis-cli.exe` uit en geeft u de *hostnaam*, *poort*en *toegangs sleutel* (primair of secundair) door om verbinding te maken met de test cache.

```
redis-cli.exe -h yourcachename.redis.cache.windows.net -p 6379 -a YourAccessKey
```

![stunnel met redis-cli](media/cache-how-to-redis-cli-tool/cache-redis-cli-non-ssl.png)




## <a name="next-steps"></a>Volgende stappen

Meer informatie over het gebruik van de [redis-console](cache-configure.md#redis-console) om opdrachten uit te geven.

