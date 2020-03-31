---
title: Een Azure CDN-eindpunt wissen | Microsoft Documenten
description: Meer informatie over het verwijderen van alle inhoud in de cache van een Azure CDN-eindpunt.
services: cdn
documentationcenter: ''
author: mdgattuso
manager: danielgi
editor: sohamnchatterjee
ms.assetid: 0b50230b-fe82-4740-90aa-95d4dde8bd4f
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/17/2019
ms.author: magattus
ms.openlocfilehash: 1bfbc1b730811e1111a08a957db3a747f90fb587
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74546201"
---
# <a name="purge-an-azure-cdn-endpoint"></a>Een Azure CDN-eindpunt verwijderen
## <a name="overview"></a>Overzicht
Azure CDN edge nodes cache assets until the asset's time-to-live (TTL) expires.  Nadat de TTL van het asset is verlopen, zal het randknooppunt een nieuwe bijgewerkte kopie van het item ophalen wanneer een client het element van het randknooppunt aanvraagt.

De beste manier om ervoor te zorgen dat uw gebruikers altijd de nieuwste kopie van uw assets verkrijgen, is om uw assets voor elke update te versien en te publiceren als nieuwe URL's.  CDN haalt onmiddellijk de nieuwe assets op voor de volgende clientaanvragen.  Soms wilt u inhoud in de cache verwijderen van alle randknooppunten en ze allemaal dwingen om nieuwe bijgewerkte elementen op te halen.  Dit kan te wijten zijn aan updates van uw webtoepassing of om snel elementen bij te werken die onjuiste informatie bevatten.

> [!TIP]
> Houd er rekening mee dat het wissen van de inhoud in de cache op de CDN-edgeservers alleen wordt gewist.  Downstreamcaches, zoals proxyservers en lokale browsercaches, kunnen nog steeds een kopie in de cache van het bestand bevatten.  Het is belangrijk om dit te onthouden wanneer u de time-to-live van een bestand instelt.  U een downstreamclient dwingen om de nieuwste versie van uw bestand aan te vragen door het elke keer dat u het bijwerkt een unieke naam te geven, of door gebruik te maken van [querytekenreeksen.](cdn-query-string.md)  
> 
> 

Deze zelfstudie leidt u door het zuiveren van activa van alle randknooppunten van een eindpunt.

## <a name="walkthrough"></a>Kennismaking
1. Blader in de [Azure Portal](https://portal.azure.com)naar het CDN-profiel met het eindpunt dat u wilt verwijderen.
2. Klik in het CDN-profielblad op de zuiveringsknop.
   
    ![CDN-profielblad](./media/cdn-purge-endpoint/cdn-profile-blade.png)
   
    Het zuiveringsmes gaat open.
   
    ![CDN-zuiveringsblad](./media/cdn-purge-endpoint/cdn-purge-blade.png)
3. Selecteer op het purgeblad het serviceadres dat u wilt verwijderen uit de vervolgkeuzelijst URL.
   
    ![Zuiveringsformulier](./media/cdn-purge-endpoint/cdn-purge-form.png)
   
   > [!NOTE]
   > U ook bij het purgeblad komen door op de **knop Purge** op het CDN-eindpuntblad te klikken.  In dat geval wordt het **URL-veld** vooraf ingevuld met het serviceadres van dat specifieke eindpunt.
   > 
   > 
4. Selecteer welke elementen u wilt verwijderen uit de randknooppunten.  Als u alle elementen wilt wissen, klikt u op het selectievakje **Alle wissen.**  Typ anders het pad van elk element dat u wilt wissen in het tekstvak **Pad.** Onderstaande indelingen worden ondersteund in het pad.
    1. **Eén URL-zuivering**: Verwijder de afzonderlijke asset door de volledige URL op`/pictures/strasbourg.png`te geven, met of zonder bestandsextensie, bijvoorbeeld ;`/pictures/strasbourg`
    2. **Wildcard purge**: Sterretje\*( ) kan worden gebruikt als een wildcard. Verwijder alle mappen, submappen en bestanden onder `/*` een eindpunt met in het pad of verwijder alle submappen en `/*`bestanden onder een`/pictures/*`specifieke map door de map op te geven, gevolgd door, bijvoorbeeld .  Houd er rekening mee dat het verwijderen van jokertekens momenteel niet wordt ondersteund door Azure CDN van Akamai. 
    3. **Worteldomeinzuivering:** Verwijder de wortel van het eindpunt met "/" in het pad.
   
   > [!TIP]
   > Paden moeten worden opgegeven voor zuivering en moeten een relatieve URL zijn die past bij de volgende [reguliere expressie](/dotnet/standard/base-types/regular-expression-language-quick-reference). **Verwijder alles** en **Wildcard-zuivering** en wordt momenteel niet ondersteund door **Azure CDN van Akamai.**
   > > Enkele URL-zuivering`@"^\/(?>(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/?)*)$";`  
   > > Queryreeks`@"^(?:\?[-\@_a-zA-Z0-9\/%:;=!,.\+'&\(\)\u0020]*)?$";`  
   > > Wildcard `@"^\/(?:[a-zA-Z0-9-_.%=\(\)\u0020]+\/)*\*$";`zuivering . 
   > 
   > Er verschijnen meer **tekstvakken voor paden** nadat u tekst hebt ingevoerd, zodat u een lijst met meerdere elementen maken.  U elementen uit de lijst verwijderen door op de knop ellips (...) te klikken.
   > 
5. Klik op de knop **Wissen.**
   
    ![Knop Wissen](./media/cdn-purge-endpoint/cdn-purge-button.png)

> [!IMPORTANT]
> Het verwerken van aanvragen duurt ongeveer 10 minuten met **Azure CDN van Microsoft,** ongeveer 2 minuten met **Azure CDN van Verizon** (standaard en premium) en ongeveer 10 seconden met Azure **CDN van Akamai**.  Azure CDN heeft een limiet van 50 gelijktijdige zuiveringsaanvragen op een bepaald moment op profielniveau. 
> 
> 

## <a name="see-also"></a>Zie ook
* [Vooraf assets op een Azure CDN-eindpunt laden](cdn-preload-endpoint.md)
* [Azure CDN REST API-verwijzing - Een eindpunt verwijderen of vooraf laden](/rest/api/cdn/endpoints)

