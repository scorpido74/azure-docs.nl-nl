---
title: bestand opnemen
description: bestand opnemen
services: redis-cache
author: wesmc7777
ms.service: cache
ms.topic: include
ms.date: 03/28/2018
ms.author: wesmc
ms.custom: include file
ms.openlocfilehash: f059f23031c2cdd74daaa856213d7e06f87dc27c
ms.sourcegitcommit: a6718e2b0251b50f1228b1e13a42bb65e7bf7ee2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2019
ms.locfileid: "71273925"
---
1. Als u een cache wilt maken, meldt u zich eerst aan bij [Azure Portal](https://portal.azure.com). Selecteer vervolgens **Een resource maken** > **Databases** > **Azure Cache voor Redis**.

    ![Nieuw Azure-cache geheugen voor redis menu](media/redis-cache-create/redis-cache-new-cache-menu.png)

2. Configureer in **Nieuwe Azure Cache voor Redis** de instellingen voor de nieuwe cache.

    | Instelling      | Voorgestelde waarde  | Description |
    | ------------ |  ------- | -------------------------------------------------- |
    | **DNS-naam** | Wereldwijd unieke naam | Naam van cache. De naam is een tekenreeks van 1 tot 63 tekens die alleen cijfers, letters en het teken `-` mag bevatten. De cachenaam mag niet beginnen of eindigen met het teken `-`, en opeenvolgende `-`-tekens zijn niet toegestaan.  | 
    | **Abonnement** | Uw abonnement | Het abonnement waarmee dit nieuwe Azure Cache voor Redis-exemplaar is gemaakt. | 
    | **Resourcegroep** |  *TestResources* | Naam voor de nieuwe resourcegroep waarin de cache moet worden gemaakt. Door alle resources voor een app in een groep te plaatsen, kunt u ze samen beheren. Als u bijvoorbeeld de resourcegroep verwijdert, worden alle resources verwijderd die bij de app behoren. | 
    | **Location** | East US | Kies een [regio](https://azure.microsoft.com/regions/) vlakbij de andere services die gaan gebruikmaken van de cache. |
    | **[Prijscategorie](https://azure.microsoft.com/pricing/details/cache/)** |  Basic C0 (250 MB Cache) |  De prijscategorie bepaalt de grootte, prestaties en functies die beschikbaar zijn voor de cache. Zie het [Azure Cache voor Redis-overzicht](../articles/azure-cache-for-redis/cache-overview.md) voor meer informatie. |
    | **Vastmaken aan dashboard** |  geselecteerd | Maak de nieuwe cache vast aan het dashboard, zodat u deze gemakkelijk kunt vinden. |

    ![Azure-cache maken voor redis](media/redis-cache-create/redis-cache-cache-create.png) 

3. Nadat de instellingen van de cache zijn geconfigureerd, klikt u op **Maken**. 

    Het kan enkele minuten duren voordat de cache is gemaakt. Als u de status wilt zien, kunt u de voortgang bekijken op het dashboard. Nadat de cache is gemaakt, wordt de status **Wordt uitgevoerd** weergegeven en is de cache klaar voor gebruik.

    ![Azure-cache voor redis gemaakt](media/redis-cache-create/redis-cache-cache-created.png)

