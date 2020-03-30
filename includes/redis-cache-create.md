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
ms.openlocfilehash: 3c064aa8e57a77b96161da06847f543816be1217
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "73719112"
---
1. Als u een cache wilt maken, meldt u zich aan bij de [Azure-portal](https://portal.azure.com) en selecteert u **Een resource maken.** 
   
   ![Een resource maken selecteren](media/redis-cache-create/create-a-resource.png)
   
1. Selecteer op de pagina **Nieuw** de optie **Databases** en selecteer Vervolgens Azure Cache **voor Redis**.
   
   ![Azure-cache selecteren voor Redis](media/redis-cache-create/redis-cache-new-cache-menu.png)
   
1. Configureer op de pagina **Nieuwe Redis-cache** de instellingen voor uw nieuwe cache.
   
   | Instelling      | Voorgestelde waarde  | Beschrijving |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-naam** | Voer een wereldwijd unieke naam in. | De naam van de cache moet een tekenreeks tussen 1 en 63 tekens zijn die alleen cijfers, letters of koppeltekens bevat. De naam moet beginnen en eindigen met een getal of letter en mag geen opeenvolgende koppeltekens bevatten. De *hostnaam* van uw cache-instantie is * \<de DNS-naam>.redis.cache.windows.net*. | 
   | **Abonnement** | Deplaats en selecteer uw abonnement. | Het abonnement waaronder deze nieuwe Azure-cache voor bijvoorbeeld Redis kan worden gemaakt. | 
   | **Resourcegroep** | Val op en selecteer een resourcegroep of selecteer **Nieuw maken** en voer een nieuwe naam van de resourcegroep in. | Naam voor de resourcegroep waarin u uw cache en andere bronnen maken. Door al uw app-bronnen in één resourcegroep te plaatsen, u ze eenvoudig samen beheren of verwijderen. | 
   | **Locatie** | Zet naar beneden en selecteer een locatie. | Selecteer een [regio](https://azure.microsoft.com/regions/) in de buurt van andere services die uw cache gebruiken. |
   | **Prijslaag** | Vervolgkeuzelijst en selecteer een [prijslaag](https://azure.microsoft.com/pricing/details/cache/). |  De prijscategorie bepaalt de grootte, prestaties en functies die beschikbaar zijn voor de cache. Zie het [Azure Cache voor Redis-overzicht](../articles/azure-cache-for-redis/cache-overview.md) voor meer informatie. |
   
1. Selecteer **Maken**. 
   
   ![ Een Azure-cache voor Redis maken](media/redis-cache-create/redis-cache-cache-create.png) 
   
   Het duurt een tijdje voor de cache te maken. U de voortgang controleren op de **pagina** Overzicht van Azure Cache for Redis. Wanneer **de status** wordt weergegeven als **Actief,** is de cache klaar voor gebruik.
   
   ![Azure-cache voor Redis is gemaakt](media/redis-cache-create/redis-cache-cache-created.png)

