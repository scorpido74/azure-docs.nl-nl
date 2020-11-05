---
title: bestand opnemen
description: bestand opnemen
services: redis-cache
author: curib
ms.service: cache
ms.topic: include
ms.date: 10/06/2020
ms.author: cauribeg
ms.custom: include file
ms.openlocfilehash: 07a80fcd1ec62b051a660bdac696f89e3b7c42b7
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93353945"
---
1. Als u een cache wilt maken, meldt u zich aan bij de [Azure-portal](https://portal.azure.com) en selecteert u **Een resource maken**.

    :::image type="content" source="media/redis-cache-create/create-resource.png" alt-text="Selecteer Azure Cache voor Redis.":::

   
1. Selecteer op de pagina **Nieuw** de optie **Databases** en selecteer vervolgens **Azure Cache voor Redis**.

    :::image type="content" source="media/redis-cache-create/select-cache.png" alt-text="Selecteer Azure Cache voor Redis.":::
   
1. Configureer op de pagina **Nieuwe Redis-cache** de instellingen voor de nieuwe cache.
   
   | Instelling      | Voorgestelde waarde  | Beschrijving |
   | ------------ |  ------- | -------------------------------------------------- |
   | **DNS-naam** | Geef een wereldwijd unieke naam op. | De cachenaam is een tekenreeks van 1 tot 63 tekens die alleen cijfers, letters en afbreekstreepjes mag bevatten. De naam moet beginnen en eindigen met een cijfer of letter en mag geen opeenvolgende afbreekstreepjes bevatten. De *hostnaam* van uw cache-exemplaar wordt *\<DNS name>.redis.cache.windows.net*. | 
   | **Abonnement** | Open de vervolgkeuzelijst en selecteer uw abonnement. | Het abonnement waarmee dit nieuwe Azure Cache voor Redis-exemplaar wordt gemaakt. | 
   | **Resourcegroep** | Open de vervolgkeuzelijst en selecteer een resourcegroep of kies **Nieuwe maken** en geef een naam voor de nieuwe resourcegroep op. | Naam voor de resourcegroep waarin de cache en andere resources moeten worden gemaakt. Door al uw app-resources in één resourcegroep te plaatsen, kunt u ze eenvoudig beheren of verwijderen. | 
   | **Locatie** | Open de vervolgkeuzelijst en selecteer een locatie. | Selecteer een [regio](https://azure.microsoft.com/regions/) in de buurt van andere services die gaan gebruikmaken van de cache. |
   | **Prijscategorie** | Open de vervolgkeuzelijst en selecteer een [Prijscategorie](https://azure.microsoft.com/pricing/details/cache/). |  De prijscategorie bepaalt de grootte, prestaties en functies die beschikbaar zijn voor de cache. Zie het [Azure Cache voor Redis-overzicht](../articles/azure-cache-for-redis/cache-overview.md) voor meer informatie. |

1. Selecteer het tabblad **Netwerken** of klik op de knop **Netwerken** onderaan de pagina.

1. Selecteer uw verbindingsmethode op het tabblad **Netwerk**.

1. Selecteer het tabblad **Volgende: Geavanceerd** of klik op de knop **Volgende: Geavanceerd** onderaan de pagina.

1. Selecteer in het tabblad **Geavanceerd** voor een basic of standard cache-exemplaar de schakeloptie inschakelen als u een niet-TLS-poort wilt inschakelen. U kunt ook selecteren welke Redis-versie u wilt gebruiken: 4 of 6 (preview).

    :::image type="content" source="media/redis-cache-create/redis-version.png" alt-text="Redis-versie 4 of 6.":::

1. Configureer in het tabblad **Geavanceerd** voor premium cache-exemplaar de instellingen voor een niet-TLS-poort, clustering en gegevenspersistentie. U kunt ook selecteren welke Redis-versie u wilt gebruiken: 4 of 6 (preview). 

1. Selecteer het tabblad **Volgende: Tags** of klik op de knop **Volgende: Tags** onderaan de pagina.

1. Voer desgewenst in het tabblad **Tags** de naam en waarde in om de resource te categoriseren. 

1. Selecteer **Controleren + maken**. Het tabblad Beoordelen + maken wordt weergegeven, waar uw configuratie wordt gevalideerd in Azure.

1. Selecteer **Maken** nadat het groene bericht Validatie geslaagd verschijnt.

Het duurt even voor de cache is gemaakt. U kunt de voortgang bekijken op de **overzichtspagina** van Azure Cache voor Redis. Als u bij **Status** **Wordt uitgevoerd** ziet staan, kunt u de cache gebruiken. 