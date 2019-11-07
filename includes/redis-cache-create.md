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
ms.sourcegitcommit: bc7725874a1502aa4c069fc1804f1f249f4fa5f7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/07/2019
ms.locfileid: "73719112"
---
1. Als u een cache wilt maken, meldt u zich aan bij de [Azure Portal](https://portal.azure.com) en selecteert u **een resource maken**. 
   
   ![Selecteer een resource maken](media/redis-cache-create/create-a-resource.png)
   
1. Selecteer op de pagina **Nieuw** de optie **data bases** en selecteer vervolgens **Azure-cache voor redis**.
   
   ![Azure-cache selecteren voor redis](media/redis-cache-create/redis-cache-new-cache-menu.png)
   
1. Configureer op de pagina **nieuw redis cache** de instellingen voor de nieuwe cache.
   
   | Instelling      | Voorgestelde waarde  | Beschrijving |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-naam** | Voer een wereld wijd unieke naam in. | De cache naam moet een teken reeks zijn tussen 1 en 63 tekens die alleen cijfers, letters of afbreek streepjes bevat. De naam moet beginnen en eindigen met een cijfer of letter en mag geen opeenvolgende afbreek streepjes bevatten. De *hostnaam* van uw cache-exemplaar wordt *\<DNS-naam >. redis. cache. Windows. net*. | 
   | **Abonnement** | Vervolg keuzelijst en selecteer uw abonnement. | Het abonnement waarmee deze nieuwe Azure-cache voor redis-exemplaar wordt gemaakt. | 
   | **Resourcegroep** | Vervolg keuzelijst en selecteer een resource groep of selecteer **nieuwe maken** en voer een nieuwe naam voor de resource groep in. | Naam voor de resource groep waarin u uw cache en andere resources wilt maken. Door al uw app-resources in één resource groep te plaatsen, kunt u ze eenvoudig beheren of verwijderen. | 
   | **Locatie** | Vervolg keuzelijst en selecteer een locatie. | Selecteer een [regio](https://azure.microsoft.com/regions/) in de buurt van andere services die uw cache gebruiken. |
   | **Prijscategorie** | Vervolg keuzelijst en selecteer een [prijs categorie](https://azure.microsoft.com/pricing/details/cache/). |  De prijscategorie bepaalt de grootte, prestaties en functies die beschikbaar zijn voor de cache. Zie het [Azure Cache voor Redis-overzicht](../articles/azure-cache-for-redis/cache-overview.md) voor meer informatie. |
   
1. Selecteer **Maken**. 
   
   ![Azure-cache maken voor redis](media/redis-cache-create/redis-cache-cache-create.png) 
   
   Het duurt even voordat de cache is gemaakt. U kunt de voortgang controleren op de **overzichts** pagina van de Azure-cache voor redis. Wanneer de **status** wordt weer gegeven als **actief**, is de cache klaar voor gebruik.
   
   ![Azure-cache voor redis gemaakt](media/redis-cache-create/redis-cache-cache-created.png)

