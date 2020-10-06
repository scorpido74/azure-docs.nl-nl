---
title: bestand opnemen
description: bestand opnemen
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 11/05/2019
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: a737e130d616a67bab28c7c96c0372216a6707af
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/05/2020
ms.locfileid: "73720304"
---
### <a name="retrieve-host-name-ports-and-access-keys-from-the-azure-portal"></a>Hostnaam, poorten en toegangssleutels ophalen uit Azure Portal

Cache-clients hebben de hostnaam, poorten en sleutels van de cache nodig om verbinding te maken met een Azure Cache for Redis-instantie. Sommige clients kunnen enigszins andere namen gebruiken om naar deze items te verwijzen. U kunt de hostnaam, poorten en toegangssleutels ophalen uit [Azure Portal](https://portal.azure.com).

- Als u de toegangssleutels wilt ophalen, selecteert u in de linkernavigatie van de cache de optie **Toegangssleutels**. 
  
  ![Sleutels van Azure Cache voor Redis](media/redis-cache-access-keys/redis-cache-keys.png)

- Als u de hostnaam en poorten wilt ophalen, selecteert u in de linkernavigatie van de cache de optie **Eigenschappen**. De hostnaam heeft de indeling *\<DNS name>.redis.cache.windows.net*.

  ![Eigenschappen van Azure Cache voor Redis](media/redis-cache-access-keys/redis-cache-hostname-ports.png)

