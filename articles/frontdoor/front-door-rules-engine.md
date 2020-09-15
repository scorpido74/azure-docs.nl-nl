---
title: Architectuur en terminologie voor regelengine voor Azure Front Door
description: In dit artikel vindt u een overzicht van de Azure Front Door-regelengine.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/14/2020
ms.author: duau
ms.openlocfilehash: 94770e4fcad9659594854f81732c7b4e4a97051c
ms.sourcegitcommit: 03662d76a816e98cfc85462cbe9705f6890ed638
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/15/2020
ms.locfileid: "90530876"
---
# <a name="what-is-rules-engine-for-azure-front-door"></a>Wat is de Azure Front Door-regelengine? 

Met de regelengine kunt u aanpassen hoe HTTP-aanvragen aan de rand worden verwerkt en kunt u meer controle krijgen over het gedrag van uw webtoepassing. De Azure Front Door-regelengine omvat verschillende belangrijke functies, waaronder:

- HTTPS afdwingen, controleer of al uw eindgebruikers uw inhoud gebruiken via een beveiligde verbinding.
- Implementeer beveiligingskopteksten om beveiligingsproblemen in browsers te voorkomen, bijvoorbeeld HTTP Strict-Transport-Security (HSTS), X-XSS-Protection, Content-Security-Policy of X-Frame-Options, alsook Access-Control-Allow-Origin-headers voor Cross-origin-resource delen (CORS)-scenario’s. Kenmerken op basis van beveiliging kunnen ook worden gedefinieerd met cookies.
- Route-aanvragen naar mobiele of desktop versies van uw toepassing op basis van de patronen in de inhoud van de aanvraagheaders, cookies of querytekenreeksen.
- Omleidingsmogelijkheden gebruiken om 301, 302, 307 en 308 te retourneren naar de client om deze om te leiden naar nieuwe hostnamen, paden of protocollen.
- De cacheconfiguratie van uw route dynamisch aanpassen op basis van de binnenkomende aanvragen.
- Herschrijf het pad naar de aanvraag-URL en stuur de aanvraag door naar de juiste back-end in de geconfigureerde back-end-pool.

## <a name="architecture"></a>Architectuur 

Regelengine verwerkt aanvragen aan de rand. Wanneer een aanvraag wordt geconfigureerd voor het Front Door-eindpunt, wordt WAF eerst uitgevoerd, gevolgd door de configuratie van de regelengine die aan uw frontend/domein is gekoppeld. Wanneer de configuratie van een regelengine wordt uitgevoerd, betekent dit dat de bovenliggende regel voor doorsturen al overeenkomt. Of alle acties in elk van de regels in de configuratie van de regelengine worden uitgevoerd, is afhankelijk van of er aan alle voorwaarden voor overeenkomst binnen die regel is voldaan. Als een aanvraag niet overeenkomt met een van de voorwaarden in de configuratie van de regelengine, wordt de standaardregel voor doorsturen uitgevoerd. 

In de onderstaande configuratie is bijvoorbeeld een regelengine geconfigureerd om een antwoordheader toe te voegen die de maximale leeftijd van de cachecontrole wijzigt als aan de voorwaarde overeenkomst wordt voldaan. 

![antwoordheader actie](./media/front-door-rules-engine/rules-engine-architecture-3.png)

In een ander voorbeeld ziet u dat de regelengine is geconfigureerd om een gebruiker naar een mobiele versie van de site te sturen als de overeenkomst voor waarde, apparaattype, true is. 

![routeconfiguratie-onderdrukkingen](./media/front-door-rules-engine/rules-engine-architecture-1.png)

In beide voorbeelden, wanneer aan geen van de voorwaarden voor overeenkomst wordt voldaan, wordt in plaats hiervan de opgegeven routeregel uitgevoerd. 

## <a name="terminology"></a>Terminologie 

Met de engine voor de AFD-regels kunt u een reeks regelengineconfiguraties maken die elk bestaan uit een set regels. Hieronder vindt u een overzicht van een aantal nuttige terminologie bij het configureren van uw regelengine. 

- *Configuratie van een regelengine*: Een set regels die worden toegepast op één routeregel. Elke configuratie is beperkt tot 25 regels. U kunt maximaal 10 configuraties maken. 
- *Regel van regelengine*: Een regel die bestaat uit maximaal 10 voorwaarden voor overeenkomst en vijf acties.
- *Voorwaarde voor overeenkomst*: Er zijn talrijke voorwaarden voor overeenkomst die kunnen worden gebruikt voor het parseren van uw binnenkomende aanvragen. Een regel kan uit maximaal 10 voorwaarden voor overeenkomst bestaan. De voorwaarden voor overeenkomst worden geëvalueerd met de operator **EN**. Een volledige lijst met voorwaarden voor overeenkomst vindt u [hier](front-door-rules-engine-match-conditions.md). 
- *Actie*: Acties die bepalen wat er gebeurt met uw binnenkomende aanvragen - aanvraag/ antwoordheaderacties, doorsturen, omleidingen en herschrijven zijn nu allemaal beschikbaar. Een regel kan maximaal vijf acties bevatten; een regel kan echter slechts 1 overschrijving van routeconfiguratie bevatten.  Een volledige lijst met acties vindt u [hier](front-door-rules-engine-actions.md).


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het instellen van uw eerste [regelengineconfiguratie](front-door-tutorial-rules-engine.md). 
- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
