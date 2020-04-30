---
title: Azure front-deur | Microsoft Docs
description: In dit artikel vindt u een overzicht van Azure Front Door. Uitzoeken of dit de juiste keuze is voor taak verdeling van gebruikers verkeer voor uw toepassing.
services: frontdoor
documentationcenter: ''
author: megan-beatty
editor: ''
ms.service: frontdoor
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 4/30/2020
ms.author: mebeatty
ms.openlocfilehash: 19deb763c8e750490854892c90d0293d3e209c09
ms.sourcegitcommit: eaec2e7482fc05f0cac8597665bfceb94f7e390f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82515550"
---
# <a name="what-is-rules-engine-for-azure-front-door"></a>Wat is een regel engine voor Azure front-deur? 

Met de engine regels kunt u aanpassen hoe HTTP-aanvragen aan de rand worden verwerkt en meer controle over het gedrag van uw webtoepassing. De regel engine voor Azure front-deur omvat verschillende belang rijke functies, waaronder:

- Op headers gebaseerde route ring: route aanvragen op basis van de patronen in de inhoud van de aanvraag headers, cookies en query reeksen.
- Op para meters gebaseerde route ring: Profiteer van een reeks matching-voor waarden, waaronder post-argumenten, query reeksen, cookies en aanvraag methoden, voor het routeren van aanvragen op basis van HTTP-aanvraag parameters. 
- Onderdrukkingen van route configuraties: 
    - Gebruik omleidings mogelijkheden om 301/302/307/308 omleidingen te retour neren naar de client om om te leiden naar nieuwe hostnamen, paden en protocollen. 
    - Gebruik doorstuur mogelijkheden om het pad naar de aanvraag-URL opnieuw te schrijven zonder een traditionele omleiding uit te voeren en de aanvraag door te sturen naar de juiste back-end in de geconfigureerde back-end-groep. 
    - Pas uw cache configuratie aan en wijzig een route dynamisch van door sturen naar caching op basis van de voor waarden van de overeenkomst. 

> [!IMPORTANT]
> Deze openbare preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar in alle Azure-locaties. Raadpleeg voor meer informatie de [aanvullende gebruiksrechtovereenkomst voor Microsoft Azure-previews](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="architecture"></a>Architectuur 

Regel engine verwerkt aanvragen aan de rand. Wanneer een aanvraag wordt geconfigureerd voor het eind punt van de voor deur, wordt WAF eerst uitgevoerd, gevolgd door de configuratie van de regel engine die aan uw frontend/domein is gekoppeld. Wanneer de configuratie van een regel engine wordt uitgevoerd, betekent dit dat de bovenliggende routerings regel al overeenkomt. Of alle acties in elk van de regels in de configuratie van de engine van de regel worden uitgevoerd, is afhankelijk van de voor waarden van de overeenkomst binnen die regel waaraan wordt voldaan. Als een aanvraag overeenkomt met geen van de voor waarden in de configuratie van de regel engine, wordt de standaard routerings regel uitgevoerd. 

In de onderstaande configuratie is bijvoorbeeld een regel engine geconfigureerd om een antwoord header toe te voegen die de maximale leeftijd van de cache-Control wijzigt als aan de voor waarde match wordt voldaan. 

![actie van de reactie header](./media/front-door-rules-engine/rules-engine-architecture-3.png)

In een ander voor beeld ziet u dat regels engine is geconfigureerd voor het verzenden van een gebruiker naar een mobiele versie van de site als de overeenkomst voor waarde, apparaattype, True is. 

![route configuratie overschrijven](./media/front-door-rules-engine/rules-engine-architecture-1.png)

In beide voor beelden, wanneer aan geen van de match-voor waarden wordt voldaan, is de opgegeven route regel wat wordt uitgevoerd. 

## <a name="terminology"></a>Terminologie 

Met de engine voor de AFD-regels kunt u een reeks-engine configuraties maken die elk bestaan uit een set regels. Hieronder vindt u een overzicht van een aantal nuttige terminologie bij het configureren van uw regel engine. 

- *Configuratie van regel engine*: een set regels die worden toegepast op één route regel. Elke configuratie is beperkt tot 5 regels. U kunt Maxi maal 10 configuraties maken. 
- *Regel engine regels*: een regel die bestaat uit Maxi maal 10 match voorwaarden en vijf acties.
- *Match-voor waarde*: er zijn talloze match voorwaarden die kunnen worden gebruikt voor het parseren van uw binnenkomende aanvragen. Een regel kan Maxi maal 10 match voorwaarden bevatten. Overeenkomende voor waarden worden geëvalueerd met een **and** -operator. [Hier](front-door-rules-engine-match-conditions.md)vindt u een volledige lijst met matching voorwaarden. 
- *Actie*: acties bepalen wat er gebeurt met uw binnenkomende aanvragen-aanvraag/antwoord-header acties, door sturen, omleidingen en herschrijven zijn nu allemaal beschikbaar. Een regel kan Maxi maal vijf acties bevatten; een regel kan echter slechts 1 overschrijving van route configuratie bevatten.  [Hier](front-door-rules-engine-actions.md)vindt u een volledige lijst met acties.


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het instellen van de configuratie van uw eerste [regel engine](front-door-tutorial-rules-engine.md). 
- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
