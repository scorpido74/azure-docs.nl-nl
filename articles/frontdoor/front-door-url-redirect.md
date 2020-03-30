---
title: Azure-voordeur - URL-omleiding | Microsoft Documenten
description: In dit artikel u begrijpen hoe Azure Front Door URL-omleiding voor hun routes ondersteunt, indien geconfigureerd.
services: front-door
documentationcenter: ''
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/08/2019
ms.author: sharadag
ms.openlocfilehash: 5e3e44c4aee84fe9e2e21174a1d65fdf26b765a2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80295482"
---
# <a name="url-redirect"></a>URL-omleiding
U Azure Front Door gebruiken om verkeer om te leiden. U verkeer op meerdere niveaus omleiden (protocol, hostnaam, pad, queryreeks) en alle functionaliteit kan worden geconfigureerd voor afzonderlijke microservices, omdat de omleiding op paden is gebaseerd. Dit vereenvoudigt de configuratie van toepassingen, optimaliseert het resourcegebruik en ondersteunt nieuwe omleidingsscenario's, waaronder globale en op paden gebaseerde omleiding.
</br>

![Azure-URL voor de voordeur omleiden][1]

## <a name="redirection-types"></a>Omleidingstypen
Met een omleidingstype wordt de statuscode voor de respons ingesteld voor de clients om het doel van de omleiding te begrijpen. De volgende soorten omleidingworden ondersteund:

- **301 (Permanent verplaatst)**: Geeft aan dat aan de doelbron een nieuwe permanente URI is toegewezen en dat eventuele toekomstige verwijzingen naar deze bron een van de meegeleverde URI's moeten gebruiken. Gebruik 301-statuscode voor HTTP naar HTTPS-omleiding. 
- **302 (Gevonden)**: geeft aan dat de doelbron tijdelijk onder een andere URI bevindt. Aangezien de omleiding bij gelegenheid kan worden gewijzigd, moet de klant de effectieve aanvraag URI blijven gebruiken voor toekomstige verzoeken.
- **307 (Tijdelijke omleiding):** geeft aan dat de doelbron tijdelijk onder een andere URI bevindt en dat de gebruikersagent de aanvraagmethode NIET mag wijzigen als deze automatisch naar die URI wordt omgeleid. Aangezien de omleiding in de loop van de tijd kan veranderen, moet de client doorgaan met het gebruik van de oorspronkelijke effectieve aanvraag URI voor toekomstige verzoeken.
- **308 (Permanente omleiding):** geeft aan dat aan de doelbron een nieuwe permanente URI is toegewezen en dat eventuele toekomstige verwijzingen naar deze bron een van de meegeleverde URI's moeten gebruiken. Clients met mogelijkheden voor het bewerken van koppelingen moeten, waar mogelijk, automatisch verwijzingen naar de effectieve aanvraag URI opnieuw koppelen aan een of meer van de nieuwe referenties die door de server worden verzonden.

## <a name="redirection-protocol"></a>Omleidingsprotocol
U het protocol instellen dat wordt gebruikt voor omleiding. Dit maakt een van de meest voorkomende use cases van redirect functie, dat is om HTTP in te stellen op HTTPS omleiding.

- **HTTPS alleen:** Stel het protocol alleen in op HTTPS als u het verkeer van HTTP naar HTTPS wilt omleiden. Azure Front Door raadt u aan om de omleiding altijd alleen in te stellen op HTTPS.
- **HTTP alleen:** Hiermee wordt de binnenkomende aanvraag omgeleid naar HTTP. Gebruik deze waarde alleen als u uw verkeer HTTP wilt behouden dat niet versleuteld is.
- **Wedstrijdaanvraag:** met deze optie blijft het protocol behouden dat wordt gebruikt door de binnenkomende aanvraag. Er blijft dus een HTTP-verzoek over en een HTTPS-verzoek blijft HTTPS-berichtomleiding.

## <a name="destination-host"></a>Bestemmingshost
Als onderdeel van het configureren van een omleidingsrouter, u ook de hostnaam of het domein voor de omleidingsaanvraag wijzigen. U dit veld zo instellen dat de hostnaam in de URL voor de omleiding wordt gewijzigd of de hostnaam op een andere manier wordt behouden voor de binnenkomende aanvraag. Dus, met behulp van dit veld `https://www.contoso.com/*` `https://www.fabrikam.com/*`kunt u alle aanvragen verzonden naar .

## <a name="destination-path"></a>Doelpad
Voor gevallen waarin u het padsegment van een URL wilt vervangen als onderdeel van omleiding, u dit veld instellen op de nieuwe padwaarde. Anders u ervoor kiezen om de padwaarde te behouden als onderdeel van omleiding. Met dit veld u dus alle `https://www.contoso.com/\*` `https://www.contoso.com/redirected-site`aanvragen die naar.

## <a name="query-string-parameters"></a>Querytekenreeksparameters
U ook de querytekenreeksparameters vervangen in de omgeleide URL. Als u een bestaande querytekenreeks wilt vervangen uit de URL van de binnenkomende aanvraag, stelt u dit veld in op 'Vervangen' en stelt u de juiste waarde in. Anders u de oorspronkelijke set querytekenreeksen behouden door het veld in te stellen op 'Behouden'. Als voorbeeld, met behulp van dit veld, `https://www.contoso.com/foo/bar` `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F`kunt u alle verkeer verzonden naar . 

## <a name="destination-fragment"></a>Doelfragment
Het doelfragment is het gedeelte van de URL na '#', dat normaal gesproken door browsers wordt gebruikt om op een specifieke sectie op een pagina te landen. U dit veld zo instellen dat een fragment wordt toegevoegd aan de omleidings-URL.

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png