---
title: Azure Front Door - Ondersteuning voor wildcarddomeinen
description: In dit artikel u begrijpen hoe Azure Front Door het toewijzen en beheren van jokerdomeinen in de lijst met aangepaste domeinen ondersteunt
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2020
ms.author: sharadag
ms.openlocfilehash: c568c9cc5c57098385cc7399459ec656cdbfc305
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537438"
---
# <a name="wildcard-domains"></a>Jokertekens

Anders dan apex domeinen en subdomeinen, u ook een wildcard domeinnaam toewijzen aan uw lijst van frontend hosts of aangepaste domeinen van uw Front Door profiel. Het hebben van wildcarddomeinen in uw frontdoorconfig vereenvoudigt het gedrag van verkeersroutering voor meerdere subdomeinen voor een API, toepassing of website van dezelfde routeringsregel zonder dat u de configuratie hoeft te wijzigen om elk subdomein afzonderlijk toe te voegen en/of op te geven. Als voorbeeld u de `customer1.contoso.com`routeringsregel voor , `customer2.contoso.com`en `customerN.contoso.com` dezelfde routeringsregel gebruiken door een jokerdomein `*.contoso.com`toe te voegen.

Enkele van de belangrijkste scenario's die zijn opgelost met ondersteuning voor wildcarddomeinen zijn:

- Niet langer nodig om aan boord van elk subdomein op uw voordeur en vervolgens inschakelen HTTPS om een certificaat te binden voor elk subdomein.
- Als een toepassing een nieuw subdomein toevoegt, hoeft u uw productiefrontdeurconfiguratie niet meer te wijzigen. Anders moest eerder het subdomein worden toegevoegd, een certificaat aan het subdomein worden toegevoegd, een WAF-beleid (Web application firewall) worden toegevoegd en het domein aan verschillende routeringsregels worden toegevoegd.

> [!NOTE]
> Momenteel worden wildcarddomeinen alleen ondersteund via de API, PowerShell en CLI. Ondersteuning voor het toevoegen van het beheren van jokerdomeinen via Azure-portal is niet beschikbaar.

## <a name="adding-wildcard-domains"></a>Jokertekens toevoegen

U een wildcarddomein aan boord nemen onder de sectie Hostof Domeinen frontend. Net als bij subdomeinen valideert Front Door dat er ook een CNAME-toewijzing is voor uw wildcarddomein. Deze DNS-toewijzing kan een directe `*.contoso.com` CNAME-toewijzing zijn, zoals toegewezen aan `contoso.azurefd.net` of via de tijdelijke toewijzing van afdverify, zoals `afdverify.contoso.com` toegewezen aan `afdverify.contoso.azurefd.net` valideert CNAME-kaart voor wildcard en (Azure DNS ondersteunt wildcardrecords).

U ook zoveel subdomeinen op één niveau van het wildcard-domein toevoegen aan frontendhosts als ze de max niet raken. limiet van frontend hosts. Deze functionaliteit kan nodig zijn voor het definiëren van een andere route voor een subdomein dan de rest van de domeinen (van het wildcarddomein) of met een ander WAF-beleid voor een specifiek subdomein. Dus, `*.contoso.com` zal `foo.contoso.com` het toevoegen mogelijk te maken `foo.bar.contoso.com` zonder opnieuw te bewijzen domeineigendom, maar niet als dat is niet een enkel niveau subdomein van `*.contoso.com`. Als `foo.bar.contoso.com` u wilt toevoegen `*.bar.contosonews.com` zonder extra validatie van het domeineigendom, moet u worden toegevoegd.

### <a name="limitations"></a>Beperkingen

1. Als een wildcard-domein wordt toegevoegd in een bepaald frontdoorprofiel, kan hetzelfde niet worden toegevoegd aan een ander frontdoorprofiel. 
2. Als een wildcarddomein wordt toegevoegd in een bepaald Front Door-profiel, kunnen subdomeinen van dat wildcard-domein niet worden toegevoegd aan andere frontdoor of een Azure CDN van Microsoft-profiel
3. Als een subdomein van een wildcarddomein wordt toegevoegd in een Front Door-profiel of een Azure CDN van Microsoft-profiel, kan het wildcarddomein niet worden toegevoegd aan een ander Front Door-profiel. 
4. Als twee profielen (Front Door of Azure CDN van Microsoft) verschillende subdomeinen van een hoofddomein hebben, kunnen wildcarddomeinen niet worden toegevoegd aan een van de profielen.

## <a name="certificate-binding-for-wildcard-domains-and-its-subdomains"></a>Certificaatbinding voor jokerdomeinen en subdomeinen

Voor het accepteren van HTTPS-verkeer op uw wildcarddomein moet u HTTPS inschakelen op het wildcarddomein. Het certificaat binding voor wildcard domein vereist een wildcard certificaat, dat wil zeggen, de onderwerpnaam van het certificaat moet ook de wildcard domein.

> [!NOTE]
> Momenteel is alleen het gebruik van uw eigen aangepaste SSL-certificaatoptie beschikbaar voor het inschakelen van HTTPS voor jokerdomeinen. Doorbeheerde certificaten kunnen niet worden gebruikt voor jokerdomeinen. 

U ervoor kiezen om hetzelfde wildcardcertificaat uit uw Key Vault te gebruiken voor de subdomeinen, of het gebruik van Front Door Managed-certificaten voor subdomeinen wordt ook ondersteund.
Als een subdomein wordt toegevoegd voor een wildcarddomein en het wildcarddomein al een certificaat had, kan HTTPS voor dit subdomein niet worden uitgeschakeld. Het subdomein gebruikt standaard de certificaatbinding van het wildcarddomein, tenzij het wordt overschreven door een ander Key Vault-certificaat of door de voordeur beheerd certificaat.

## <a name="web-application-firewall-for-wildcard-domains-and-its-subdomains"></a>Firewall voor webtoepassingen voor jokerdomeinen en subdomeinen

WAF-beleid kan worden gekoppeld aan een wildcarddomein dat vergelijkbaar is met andere domeinen. Een ander WAF-beleid kan worden toegepast op een subdomein van een wildcarddomein. Voor de subdomeinen moet u expliciet het WAF-beleid opgeven dat moet worden gebruikt en zelfs als dit hetzelfde beleid is als het jokerdomein. Subdomeinen nemen het WAF-beleid **niet** automatisch over van het wildcarddomein.

Als u een scenario hebt waarin u niet wilt dat WAF wordt uitgevoerd voor een subdomein, u een leeg WAF-beleid maken zonder beheerde of aangepaste regelsets.

## <a name="routing-rules-for-wildcard-domains-and-its-subdomains"></a>Routeringsregels voor jokerdomeinen en subdomeinen

Wanneer u een routeringsregel configureert, u een jokerdomein selecteren als frontendhost. U ook ander routegedrag hebben voor wildcarddomein versus subdomeinen. Zoals beschreven in [hoe Front Door route matching doet,](front-door-route-matching.md)wordt de meest specifieke overeenkomst voor het domein over verschillende routeringsregels gekozen tijdens runtime.

> [!WARNING]
> Als u twee routeringsregels `*.foo.com/*` hebt, zoals **Route 1:** toegewezen `bar.foo.com/somePath/*` aan Backend Pool A en Route `bar.foo.com/anotherPath/*` **2**: toegewezen aan Backend Pool B en als er een aanvraag binnenkomt, dan zien uw klanten storingen omdat Front Door geen overeenkomst vindt over beide routes. Dit komt omdat Front Door volgens ons [algoritme voor het matchen van route](front-door-route-matching.md)2 selecteert op basis van een specifieker domein, maar alleen om te ontdekken dat er geen overeenkomende padpatronen zijn. 


## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Meer informatie over het [toevoegen van een aangepast domein op Front Door.](front-door-custom-domain.md)
- Meer informatie over het [inschakelen van HTTPS op een aangepast domein](front-door-custom-domain-https.md).
