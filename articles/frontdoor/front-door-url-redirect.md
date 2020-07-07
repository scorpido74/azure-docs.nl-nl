---
title: Azure front-deur-URL-omleiding | Microsoft Docs
description: Dit artikel helpt u te begrijpen hoe de front-deur van Azure URL-omleiding voor hun routes ondersteunt, indien geconfigureerd.
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
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "80295482"
---
# <a name="url-redirect"></a>URL-omleiding
U kunt de voor deur van Azure gebruiken om verkeer om te leiden. U kunt verkeer omleiden op meerdere niveaus (protocol, hostnaam, pad, query teken reeks) en alle functionaliteit kan worden geconfigureerd voor afzonderlijke micro Services, aangezien de omleiding is gebaseerd op het pad. Dit vereenvoudigt de configuratie van de toepassing, optimaliseert het resource gebruik en ondersteunt nieuwe omleidings scenario's, waaronder globale en op pad gebaseerde omleiding.
</br>

![URL-omleiding voor Azure front-deur][1]

## <a name="redirection-types"></a>Type omleiding
Een omleidings type stelt de antwoord status code voor de clients in om het doel van de omleiding te begrijpen. De volgende typen omleiding worden ondersteund:

- **301 (permanent verplaatst)**: geeft aan dat aan de doel resource een nieuwe permanente URI is toegewezen en dat toekomstige verwijzingen naar deze resource een van de Inge sloten uri's moeten gebruiken. Gebruik 301-status code voor HTTP-naar-HTTPS-omleiding. 
- **302 (gevonden)**: geeft aan dat de doel resource zich tijdelijk bevindt in een andere URI. Omdat de omleiding mogelijk wordt gewijzigd, moet de client de ingangs-URI voor toekomstige aanvragen blijven gebruiken.
- **307 (tijdelijke omleiding)**: geeft aan dat de doel resource zich tijdelijk bevindt in een andere URI en dat de gebruikers agent de aanvraag methode niet mag wijzigen als deze een automatische omleiding naar die URI uitvoert. Omdat de omleiding na verloop van tijd kan worden gewijzigd, moet de client de oorspronkelijke ingangs-URI van de aanvraag blijven gebruiken voor toekomstige aanvragen.
- **308 (permanente omleiding)**: geeft aan dat aan de doel resource een nieuwe permanente URI is toegewezen en dat toekomstige verwijzingen naar deze bron een van de Inge sloten uri's moeten gebruiken. Clients met mogelijkheden voor het bewerken van koppelingen moeten verwijzingen automatisch opnieuw koppelen naar de doel-URI van de aanvraag naar een of meer van de nieuwe referenties die door de server worden verzonden, waar mogelijk.

## <a name="redirection-protocol"></a>Omleidings Protocol
U kunt het protocol instellen dat voor omleiding wordt gebruikt. Zo kunt u een van de meest voorkomende gebruiks voorbeelden van de omleidings functie gebruiken. Dit is het instellen van HTTP-naar-HTTPS-omleiding.

- **Alleen https**: Stel het protocol in op https alleen als u het verkeer van http naar https wilt omleiden. De Azure front-deur raadt u aan om de omleiding altijd in te stellen op HTTPS.
- **Alleen http**: Hiermee wordt de inkomende aanvraag omgeleid naar http. Gebruik deze waarde alleen als u het verkeer HTTP wilt laten, niet-versleuteld.
- **Overeenkomst aanvragen: met**deze optie behoudt u het protocol dat door de binnenkomende aanvraag wordt gebruikt. Een HTTP-aanvraag blijft dus HTTP en een HTTPS-aanvraag blijft HTTPS post-omleiding.

## <a name="destination-host"></a>Doelhost
Als onderdeel van het configureren van een omleidings routering kunt u de hostnaam of het domein voor de omleidings aanvraag ook wijzigen. U kunt dit veld instellen om de hostnaam in de URL voor de omleiding te wijzigen of op een andere manier de hostnaam van de inkomende aanvraag te behouden. Met dit veld kunt u dus alle aanvragen omleiden die zijn verzonden `https://www.contoso.com/*` naar `https://www.fabrikam.com/*` .

## <a name="destination-path"></a>Doelpad
Als u het pad van een URL als onderdeel van de omleiding wilt vervangen, kunt u dit veld instellen met de nieuwe waarde voor het pad. Als dat niet het geval is, kunt u ervoor kiezen om de padwaarde als onderdeel van de omleiding te behouden. Met dit veld kunt u dus alle aanvragen omleiden die worden verzonden `https://www.contoso.com/\*` naar `https://www.contoso.com/redirected-site` .

## <a name="query-string-parameters"></a>Query reeks parameters
U kunt ook de query reeks parameters in de omgeleide URL vervangen. Als u een bestaande query reeks wilt vervangen door de URL van de inkomende aanvraag, stelt u dit veld in op vervangen en stelt u vervolgens de juiste waarde in. Als dat niet het geval is, kunt u de oorspronkelijke reeks query reeksen behouden door het veld in te stellen op ' behouden '. Met dit veld kunt u bijvoorbeeld alle verkeer omleiden dat wordt verzonden naar `https://www.contoso.com/foo/bar` naar `https://www.contoso.com/foo/bar?&utm_referrer=https%3A%2F%2Fwww.bing.com%2F` . 

## <a name="destination-fragment"></a>Doel fragment
Het doel fragment is het deel van de URL na ' # ', dat doorgaans door browsers wordt gebruikt om op een specifieke sectie op een pagina te worden gegrond. U kunt dit veld instellen om een fragment toe te voegen aan de omleidings-URL.

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).

<!--Image references-->
[1]: ./media/front-door-url-redirect/front-door-url-redirect.png