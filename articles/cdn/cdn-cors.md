---
title: Azure CDN gebruiken met CORS | Microsoft Documenten
description: Meer informatie over het gebruik van het Azure Content Delivery Network (CDN) met Cross-Origin Resource Sharing (CORS).
services: cdn
documentationcenter: ''
author: zhangmanling
manager: erikre
editor: ''
ms.assetid: 86740a96-4269-4060-aba3-a69f00e6f14e
ms.service: azure-cdn
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/23/2017
ms.author: mazha
ms.openlocfilehash: 169de21b6dbdafaaeff64e315daa104f3b6faadd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74278108"
---
# <a name="using-azure-cdn-with-cors"></a>Azure CDN gebruiken met CORS
## <a name="what-is-cors"></a>Wat is CORS?
CORS (Cross Origin Resource Sharing) is een HTTP-functie waarmee een webtoepassing die onder het ene domein wordt uitgevoerd, toegang heeft tot bronnen in een ander domein. Om de mogelijkheid van cross-site scripting-aanvallen te verminderen, implementeren alle moderne webbrowsers een beveiligingsbeperking die bekend staat als [het beleid van same-origin.](https://www.w3.org/Security/wiki/Same_Origin_Policy)  Dit voorkomt dat een webpagina API's in een ander domein aanroept.  CORS biedt een veilige manier om een oorsprong (het oorsprongsdomein) toe te staan API's in een andere oorsprong aan te roepen.

## <a name="how-it-works"></a>Hoe werkt het?
Er zijn twee soorten CORS-verzoeken, *eenvoudige verzoeken* en *complexe aanvragen.*

### <a name="for-simple-requests"></a>Voor eenvoudige verzoeken:

1. De browser stuurt de CORS-aanvraag met een extra **Origin** HTTP-aanvraagkop. De waarde van deze koptekst is de oorsprong die de bovenliggende pagina heeft weergegeven, die wordt gedefinieerd als de combinatie van *protocol,* *domein* en *poort.*  Wanneer een pagina\:uit https /www.contoso.com probeert toegang te krijgen tot de gegevens van een gebruiker in de fabrikam.com oorsprong, wordt de volgende aanvraagkoptekst naar fabrikam.com verzonden:

   `Origin: https://www.contoso.com`

2. De server kan reageren met een van de volgende opties:

   * Een header **Access-Control-Allow-Origin** geeft aan welke oorsprongssite is toegestaan. Bijvoorbeeld:

     `Access-Control-Allow-Origin: https://www.contoso.com`

   * Een HTTP-foutcode zoals 403 als de server de cross-origin-aanvraag niet toestaat na het controleren van de Origin-header

   * Een header **Access-Control-Allow-Origin** met een wildcard waarmee alle oorsprong en selectie het eens is:

     `Access-Control-Allow-Origin: *`

### <a name="for-complex-requests"></a>Voor complexe aanvragen:

Een complexe aanvraag is een CORS-verzoek waarbij de browser een *preflight-verzoek* moet verzenden (dat wil zeggen een voorlopige sonde) voordat de werkelijke CORS-aanvraag wordt verzonden. De preflight-aanvraag vraagt de server toestemming of het `OPTIONS` oorspronkelijke CORS-verzoek kan doorgaan en is een verzoek om dezelfde URL.

> [!TIP]
> Voor meer informatie over CORS-stromen en veelvoorkomende valkuilen, bekijk de [Gids voor CORS voor REST API's](https://www.moesif.com/blog/technical/cors/Authoritative-Guide-to-CORS-Cross-Origin-Resource-Sharing-for-REST-APIs/).
>
>

## <a name="wildcard-or-single-origin-scenarios"></a>Wildcard- of scenario's voor één oorsprong
CORS op Azure CDN werkt automatisch zonder extra configuratie wanneer de header **Access-Control-Allow-Origin** is ingesteld op wildcard (*) of één origine.  Het CDN slaat het eerste antwoord in de cache en volgende aanvragen gebruiken dezelfde koptekst.

Als er al aanvragen zijn gedaan bij het CDN voordat CORS is ingesteld op uw oorsprong, moet u inhoud op uw eindpuntinhoud verwijderen om de inhoud opnieuw te laden met de header **Access-Control-Allow-Origin.**

## <a name="multiple-origin-scenarios"></a>Meerdere oorsprongsscenario's
Als u een specifieke lijst van oorsprong voor CORS moet toestaan, wordt het een beetje ingewikkelder. Het probleem treedt op wanneer de CDN de header **Access-Control-Allow-Origin** voor de eerste CORS-oorsprong in de cache opslaat.  Wanneer een andere CORS-oorsprong een volgend verzoek indient, wordt de **header Access-Control-Allow-Origin** in de cache weergegeven, die niet overeenkomt.  Er zijn verschillende manieren om dit te corrigeren.

### <a name="azure-cdn-standard-profiles"></a>Azure CDN-standaardprofielen
Op Azure CDN Standard van Microsoft u een regel maken in de [standaardregelsengine](cdn-standard-rules-engine-reference.md) om de **Origin-header** op de aanvraag te controleren. Als het een geldige oorsprong is, stelt uw regel de header **Access-Control-Allow-Origin** in met de gewenste waarde. In dit geval wordt de **header Access-Control-Allow-Origin** van de origin-server van het bestand genegeerd en beheert de regelsvan het CDN de toegestane CORS-oorsprong volledig.

![Regels voorbeeld met standaard regels motor](./media/cdn-cors/cdn-standard-cors.png)

> [!TIP]
> U extra acties aan uw regel toevoegen om extra antwoordkoppen te wijzigen, zoals **Methoden voor toegangsbeheer-toestaan.**
> 

Op **Azure CDN Standard van Akamai**, het enige mechanisme om meerdere oorsprong toe te staan zonder het gebruik van de joker oorsprong is het gebruik van [query string caching](cdn-query-string.md). Schakel de instelling voor querytekenreeksen in voor het CDN-eindpunt en gebruik vervolgens een unieke querytekenreeks voor aanvragen van elk toegestaan domein. Als u dit doet, wordt er een afzonderlijk object in het CDN voor elke unieke querytekenreeks gecachingd. Deze aanpak is echter niet ideaal, omdat het zal resulteren in meerdere kopieën van hetzelfde bestand dat op het CDN is opgeslagen.  

### <a name="azure-cdn-premium-from-verizon"></a>Azure CDN Premium van Verizon
Met de Verizon Premium-regelsmoet u [een regel maken](cdn-rules-engine.md) om de **Origin-header** op de aanvraag te controleren.  Als het een geldige oorsprong is, stelt uw regel de header **Access-Control-Allow-Origin** in op de oorsprong die in de aanvraag wordt opgegeven.  Als de oorsprong die is opgegeven in de **Origin-header** niet is toegestaan, moet uw regel de header **Access-Control-Allow-Origin** weglaten, waardoor de browser het verzoek weigert. 

Er zijn twee manieren om dit te doen met de Premium regels motor. In beide gevallen wordt de **access-control-allow-origin-header** van de origin-server van het bestand genegeerd en beheert de regelsengine van het CDN de toegestane CORS-oorsprong volledig.

#### <a name="one-regular-expression-with-all-valid-origins"></a>Eén reguliere uitdrukking met alle geldige oorsprong
In dit geval maakt u een reguliere expressie die alle oorsprongbevat die u wilt toestaan: 

    https?:\/\/(www\.contoso\.com|contoso\.com|www\.microsoft\.com|microsoft.com\.com)$

> [!TIP]
> **Azure CDN Premium van Verizon** gebruikt [Perl Compatible Regular Expressions](https://pcre.org/) als engine voor reguliere expressies.  U een tool als [Reguliere Expressie101](https://regex101.com/) gebruiken om uw reguliere expressie te valideren.  Merk op dat het teken "/" geldig is in reguliere expressies en niet hoeft te worden ontsnapt, maar ontsnappen aan dat teken wordt beschouwd als een best practice en wordt verwacht door sommige regex validators.
> 
> 

Als de reguliere expressie overeenkomt, vervangt uw regel de header **Access-Control-Allow-Origin** (indien aanwezig) van de oorsprong door de oorsprong die het verzoek heeft verzonden.  U ook extra CORS-headers toevoegen, zoals **Access-Control-Allow-Methods**.

![Voorbeeld van regels met reguliere expressie](./media/cdn-cors/cdn-cors-regex.png)

#### <a name="request-header-rule-for-each-origin"></a>Kopregel aanvragen voor elke oorsprong.
In plaats van reguliere expressies u in plaats daarvan een aparte regel maken voor elke oorsprong die u wilt toestaan met de [voorwaarde](/previous-versions/azure/mt757336(v=azure.100)#match-conditions)Wildcard **voor jokertekens aanvragen.** Net als bij de reguliere expressiemethode stelt de regelsmotor alleen de CORS-headers in. 

![Voorbeelden van regels zonder reguliere expressie](./media/cdn-cors/cdn-cors-no-regex.png)

> [!TIP]
> In het bovenstaande voorbeeld geeft het gebruik van het jokerteken * de regelsengine aan dat deze overeenkomt met zowel HTTP als HTTPS.
> 
> 



