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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73720304"
---
### <a name="retrieve-host-name-ports-and-access-keys-from-the-azure-portal"></a>Hostnaam, poorten en toegangssleutels ophalen uit de Azure-portal

Als u verbinding wilt maken met een Azure-cache voor een instantie van Redis, hebben cacheclients de hostnaam, poorten en een sleutel voor de cache nodig. Sommige clients kunnen enigszins andere namen gebruiken om naar deze items te verwijzen. U de hostnaam, poorten en sleutels ophalen via de [Azure-portal.](https://portal.azure.com)

- Als u de toegangssleutels wilt ophalen, selecteert u **Access-sleutels**in de linkercache. 
  
  ![Sleutels van Azure Cache voor Redis](media/redis-cache-access-keys/redis-cache-keys.png)

- Als u de hostnaam en -poorten wilt ophalen, selecteert u **Eigenschappen**in de linkernavigatie in de cache . De hostnaam is van de DNS-naam van het formulier * \<>.redis.cache.windows.net*.

  ![Eigenschappen van Azure Cache voor Redis](media/redis-cache-access-keys/redis-cache-hostname-ports.png)

