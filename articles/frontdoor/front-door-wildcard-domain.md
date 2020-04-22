---
title: Azure Front Door - Ondersteuning voor wildcarddomeinen
description: In dit artikel u begrijpen hoe Azure Front Door het toewijzen en beheren van wildcarddomeinen in de lijst met aangepaste domeinen ondersteunt.
services: frontdoor
author: sharad4u
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/10/2020
ms.author: sharadag
ms.openlocfilehash: 6d8a6d6f0b05b9b7fd0144959c82b6a2c9e659a3
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81768315"
---
# <a name="wildcard-domains"></a>Jokertekens

Anders dan topdomeinen en subdomeinen u een wildcard-domeinnaam toewijzen aan uw lijst met front-endhosts of aangepaste domeinen in uw Azure Front Door-profiel. Het hebben van wildcarddomeinen in uw Azure Front Door-configuratie vereenvoudigt het gedrag van verkeersroutering voor meerdere subdomeinen voor een API, toepassing of website van dezelfde routeringsregel. U hoeft de configuratie niet te wijzigen om elk subdomein afzonderlijk toe te voegen of op te geven. Als voorbeeld u de `customer1.contoso.com`routeringsroute definiëren voor , `customer2.contoso.com`en `customerN.contoso.com` door `*.contoso.com`dezelfde routeringsregel te gebruiken en het jokerdomein toe te voegen.

Belangrijke scenario's die zijn verbeterd met ondersteuning voor jokerdomeinen zijn:

- U hoeft niet aan elk subdomein in uw Azure Front Door-profiel te worden ingeschakeld en vervolgens HTTPS in te schakelen om een certificaat voor elk subdomein te binden.
- U hoeft uw configuratie van de productie-Azure Front Door niet langer te wijzigen als een toepassing een nieuw subdomein toevoegt. Voorheen moest u het subdomein toevoegen, er een certificaat aan binden, een WAF-beleid (Web application firewall) koppelen en vervolgens het domein toevoegen aan verschillende routeringsregels.

> [!NOTE]
> Momenteel worden wildcarddomeinen alleen ondersteund via API, PowerShell en azure cli. Ondersteuning voor het toevoegen en beheren van jokerdomeinen in de Azure-portal is niet beschikbaar.

## <a name="adding-wildcard-domains"></a>Jokertekens toevoegen

U een wildcarddomein toevoegen onder de sectie voor front-endhosts of -domeinen. Net als bij subdomeinen valideert Azure Front Door dat er CNAME-recordtoewijzing is voor uw wildcarddomein. Deze DNS-toewijzing kan een directe `*.contoso.com` CNAME-recordtoewijzing zijn, zoals toegewezen aan `contoso.azurefd.net`. Of u gebruik maken van afdverify tijdelijke mapping. Bijvoorbeeld `afdverify.contoso.com` toegewezen aan `afdverify.contoso.azurefd.net` valideert de CNAME-recordkaart voor de wildcard.

> [!NOTE]
> Azure DNS ondersteunt recordsets met jokertekens.

U zoveel subdomeinen op één niveau van het jokerdomein toevoegen aan front-endhosts, tot aan de limiet van de front-endhosts. Deze functionaliteit is mogelijk vereist voor:

- Een andere route definiëren voor een subdomein dan de rest van de domeinen (vanuit het wildcarddomein).

- Het hebben van een ander WAF-beleid voor een specifiek subdomein. Hiermee kunt `*.contoso.com` u `foo.contoso.com` bijvoorbeeld toevoegen zonder dat u het eigendom van het domein opnieuw hoeft te bewijzen. Maar het staat `foo.bar.contoso.com` niet toe omdat het niet een `*.contoso.com`enkel niveau subdomein van . Als `foo.bar.contoso.com` u wilt toevoegen `*.bar.contosonews.com` zonder extra validatie van domeineigendom, moet worden toegevoegd.

U wildcarddomeinen en hun subdomeinen toevoegen met bepaalde beperkingen:

- Als een wildcarddomein wordt toegevoegd aan een Azure Front Door-profiel:
  - Het wildcarddomein kan niet worden toegevoegd aan een ander Azure Front Door-profiel.
  - Subdomeinen op het eerste niveau van het wildcarddomein kunnen niet worden toegevoegd aan een ander Azure Front Door-profiel of een Azure Content Delivery Network-profiel.
- Als een subdomein van een wildcarddomein wordt toegevoegd aan een Azure Front Door-profiel of Azure Content Delivery Network-profiel, kan het wildcarddomein niet worden toegevoegd aan andere Azure Front Door-profielen.
- Als twee profielen (Azure Front Door of Azure Content Delivery Network) verschillende subdomeinen van een hoofddomein hebben, kunnen wildcarddomeinen niet aan een van de profielen worden toegevoegd.

## <a name="certificate-binding"></a>Certificaatbinding

Voor het accepteren van HTTPS-verkeer op uw wildcarddomein moet u HTTPS inschakelen op het wildcarddomein. Voor de certificaatbinding voor een wildcarddomein is een wildcardcertificaat vereist. Dat wil zeggen, de onderwerpnaam van het certificaat moet ook het wildcarddomein hebben.

> [!NOTE]
> Momenteel is alleen het gebruik van uw eigen aangepaste SSL-certificaatoptie beschikbaar voor het inschakelen van HTTPS voor jokerdomeinen. Azure Front Door managed certificates kunnen niet worden gebruikt voor wildcarddomeinen.

U ervoor kiezen om hetzelfde wildcardcertificaat te gebruiken uit Azure Key Vault of uit Azure Front Door managed certificates voor subdomeinen.

Als er een subdomein wordt toegevoegd voor een wildcarddomein waar al een certificaat aan is gekoppeld, kan HTTPS voor het subdomein niet worden uitgeschakeld. Het subdomein gebruikt de certificaatbinding voor het wildcarddomein, tenzij een ander Key Vault- of Azure Front Door-beheerd certificaat het certificaat overschrijft.

## <a name="waf-policies"></a>WAF-beleid

WAF-beleid kan worden gekoppeld aan wildcarddomeinen, vergelijkbaar met andere domeinen. Een ander WAF-beleid kan worden toegepast op een subdomein van een wildcarddomein. Voor de subdomeinen moet u het WAF-beleid opgeven dat moet worden gebruikt, zelfs als dit hetzelfde beleid is als het jokerdomein. Subdomeinen erven het WAF-beleid *niet* automatisch van het wildcarddomein.

Als u niet wilt dat een WAF-beleid wordt uitgevoerd voor een subdomein, u een leeg WAF-beleid maken zonder beheerde of aangepaste regelsets.

## <a name="routing-rules"></a>Regels voor doorsturen

Wanneer u een routeringsregel configureert, u een jokerdomein selecteren als front-endhost. U ook ander routegedrag hebben voor jokerdomeinen en subdomeinen. Zoals beschreven in [Hoe Azure Front Door routematching uitvoert,](front-door-route-matching.md)wordt de meest specifieke overeenkomst voor het domein voor verschillende routeringsregels gekozen tijdens runtime.

> [!IMPORTANT]
> U moet overeenkomende padpatronen hebben in uw routeringsregels of uw klanten zien fouten. U hebt bijvoorbeeld twee routeringsregels`*.foo.com/*` zoals Route 1 (toegewezen aan back-endpool A) en Route 2 (toegewezen`bar.foo.com/somePath/*` aan back-endpool B). Dan komt er `bar.foo.com/anotherPath/*`een verzoek binnen voor . Azure Front Door selecteert Route 2 op basis van een meer specifieke domeinovereenkomst, alleen om geen overeenkomende padpatronen over de routes te vinden.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [maken van een Azure Front Door-profiel](quickstart-create-front-door.md).
- Meer informatie over het [toevoegen van een aangepast domein op Azure Front Door](front-door-custom-domain.md).
- Meer informatie over het [inschakelen van HTTPS op een aangepast domein](front-door-custom-domain-https.md).
