---
title: Assets vooraf laden op een Azure CDN-eindpunt | Microsoft Documenten
description: Meer informatie over het vooraf laden van cache-inhoud op een Azure CDN-eindpunt.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: ''
ms.assetid: 5ea3eba5-1335-413e-9af3-3918ce608a83
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/12/2018
ms.author: magattus
ms.openlocfilehash: d91507ad2cb271b23b588ef7da88e6e6712915b1
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "67593584"
---
# <a name="pre-load-assets-on-an-azure-cdn-endpoint"></a>Vooraf assets op een Azure CDN-eindpunt laden
[!INCLUDE [cdn-verizon-only](../../includes/cdn-verizon-only.md)]

Standaard worden assets alleen in de cache opgeslagen wanneer ze worden aangevraagd. Omdat de edge-servers de inhoud nog niet in de cache hebben opgeslagen en de aanvraag naar de oorsprongsserver moeten worden doorgestuurd, kan het eerste verzoek van elke regio langer duren dan volgende aanvragen. Om deze latentie van de eerste hit te voorkomen, moet u uw assets vooraf laden. Naast een betere klantervaring, kan het vooraf laden van uw cache-assets het netwerkverkeer op de origin-server verminderen.

> [!NOTE]
> Items vooraf laden is handig voor grote gebeurtenissen of inhoud die tegelijkertijd beschikbaar wordt voor veel gebruikers, zoals een nieuwe filmrelease of een software-update.
> 
> 

In deze zelfstudie u inhoud in de cache op alle Azure CDN-randknooppunten voorladen.

## <a name="to-pre-load-assets"></a>Activa vooraf laden
1. Blader in de [Azure-portal](https://portal.azure.com)naar het CDN-profiel met het eindpunt dat u wilt vooraf laden. Het profielvenster wordt geopend.
    
2. Klik op het eindpunt in de lijst. Het deelvenster Eindpunt wordt geopend.
3. Selecteer **Laden**in het cdn-eindpuntvenster .
   
    ![CdN-eindpuntvenster](./media/cdn-preload-endpoint/cdn-endpoint-blade.png)
   
    Het deelvenster **Laden** wordt geopend.
   
    ![CDN-belastingvenster](./media/cdn-preload-endpoint/cdn-load-blade.png)
4. Voer bij **Inhoudspad**het volledige pad in van elk `/pictures/kitten.png`item dat u wilt laden (bijvoorbeeld).
   
   > [!TIP]
   > Nadat u tekst hebt ingevoerd, worden er meer tekstvakken **voor inhoudspaden** weergegeven waarmee u een lijst met meerdere elementen maken. Als u elementen uit de lijst wilt verwijderen, selecteert u de knop ellips (...) en selecteert u **Verwijderen**.
   > 
   > Elk inhoudspad moet een relatieve URL zijn die past bij de volgende [reguliere expressies:](/dotnet/standard/base-types/regular-expression-language-quick-reference)  
   > - Eén bestandspad laden:`^(?:\/[a-zA-Z0-9-_.%=\u0020]+)+$`  
   > - Eén bestand laden met querytekenreeks:`^(?:\?[-_a-zA-Z0-9\/%:;=!,.\+'&\u0020]*)?$` 
   > 
   > Omdat elk item zijn eigen pad moet hebben, is er geen wildcardfunctionaliteit voor het vooraf laden van assets.
   > 
   > 
   
    ![Knop Laden](./media/cdn-preload-endpoint/cdn-load-paths.png)
5. Wanneer u klaar bent met het invoeren van inhoudspaden, selecteert u **Laden**.
   

> [!NOTE]
> Er is een limiet van 10 laadaanvragen per minuut per CDN-profiel en 50 gelijktijdige paden kunnen tegelijk worden verwerkt. Elk pad heeft een padlengtelimiet van 1024 tekens.
> 
> 

## <a name="see-also"></a>Zie ook
* [Een Azure CDN-eindpunt verwijderen](cdn-purge-endpoint.md)
* [Azure CDN REST API-verwijzing: inhoud vooraf laden op een eindpunt](https://docs.microsoft.com/rest/api/cdn/endpoints/loadcontent)
* [Azure CDN REST API-verwijzing: inhoud verwijderen uit een eindpunt](https://docs.microsoft.com/rest/api/cdn/endpoints/purgecontent)

