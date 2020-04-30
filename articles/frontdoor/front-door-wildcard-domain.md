---
title: Azure front-deur-ondersteuning voor Joker teken domeinen
description: Dit artikel helpt u inzicht te krijgen in de manier waarop Azure front-deur het toewijzen en beheren van joker tekens in de lijst met aangepaste domeinen ondersteunt.
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
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81768315"
---
# <a name="wildcard-domains"></a>Joker tekens domeinen

Met uitzonde ring van Apex-domeinen en subdomeinen kunt u een domein naam voor joker tekens toewijzen aan de lijst met front-end-hosts of aangepaste domeinen in uw Azure front-deur profiel. Het gebruik van joker tekens in uw Azure front-deur configuratie vereenvoudigt het routeren van verkeer voor meerdere subdomeinen voor een API, toepassing of website uit dezelfde routerings regel. U hoeft de configuratie niet te wijzigen om elk subdomein afzonderlijk toe te voegen of op te geven. U kunt bijvoorbeeld het bewerkings plan voor, `customer1.contoso.com` `customer2.contoso.com`en `customerN.contoso.com` opgeven met behulp van dezelfde routerings regel om het domein `*.contoso.com`met het Joker teken toe te voegen.

De belangrijkste scenario's die zijn verbeterd met ondersteuning voor joker tekens zijn onder andere:

- U hoeft niet alle subdomeinen in uw Azure front deur-profiel uit te voeren en vervolgens HTTPS in te scha kelen om een certificaat voor elk subdomein te binden.
- U hoeft niet langer de configuratie van de Azure-productie front-deur te wijzigen als een toepassing een nieuw subdomein toevoegt. Voorheen moest u het subdomein toevoegen, een certificaat binden, een Web Application Firewall-beleid (WAF) koppelen en het domein vervolgens toevoegen aan verschillende routerings regels.

> [!NOTE]
> Domein-joker tekens worden momenteel alleen ondersteund via API, Power shell en de Azure CLI. Ondersteuning voor het toevoegen en beheren van joker tekens in de Azure Portal is niet beschikbaar.

## <a name="adding-wildcard-domains"></a>Joker tekens toevoegen aan domeinen

U kunt een Joker teken domein toevoegen onder de sectie voor front-end-hosts of-domeinen. Net als bij subdomeinen valideert Azure front-deur dat er CNAME-record toewijzing is voor uw Joker teken domein. Deze DNS-toewijzing kan een directe CNAME-record toewijzing `*.contoso.com` zijn, `contoso.azurefd.net`zoals toegewezen aan. U kunt ook afdverify tijdelijke toewijzing gebruiken. Zo wordt bijvoorbeeld `afdverify.contoso.com` gekoppeld om `afdverify.contoso.azurefd.net` de CNAME-record toewijzing voor het Joker teken te valideren.

> [!NOTE]
> Azure DNS ondersteunt recordsets met jokertekens.

U kunt meerdere subdomeinen met één niveau van het Joker teken domein in front-end-hosts toevoegen, tot aan de limiet van de front-end-hosts. Deze functionaliteit is mogelijk vereist voor:

- Het definiëren van een andere route voor een subdomein dan de rest van de domeinen (van het Joker teken domein).

- Een ander WAF-beleid hebben voor een specifiek subdomein. `*.contoso.com` Hiermee kan bijvoorbeeld worden toegevoegd `foo.contoso.com` zonder dat opnieuw een domein eigendom moet worden bewezen. Maar dit is niet `foo.bar.contoso.com` toegestaan omdat het geen subdomein van één niveau `*.contoso.com`is van. Om toe `foo.bar.contoso.com` te voegen zonder de validatie `*.bar.contosonews.com` van de domein eigenaar, moet worden toegevoegd.

U kunt Joker teken domeinen en de bijbehorende subdomeinen toevoegen met bepaalde beperkingen:

- Als een Joker teken domein wordt toegevoegd aan een Azure front deur-profiel:
  - Het Joker teken domein kan niet worden toegevoegd aan een ander Azure front deur-profiel.
  - Subdomeinen van het eerste niveau van het Joker teken domein kunnen niet worden toegevoegd aan een ander profiel voor een Azure-front-deur of een Azure Content Delivery Network-profiel.
- Als een subdomein van een domein met Joker tekens wordt toegevoegd aan een Azure front deur-profiel of Azure Content Delivery Network-profiel, kan het Joker teken niet worden toegevoegd aan andere profielen van de Azure-front-deur.
- Als twee profielen (Azure front-deur of Azure Content Delivery Network) verschillende subdomeinen van een hoofd domein hebben, kunnen joker tekens niet aan een van de profielen worden toegevoegd.

## <a name="certificate-binding"></a>Certificaat binding

Voor het accepteren van HTTPS-verkeer op uw Joker teken domein, moet u HTTPS inschakelen voor het Joker teken domein. Voor de certificaat binding voor een Joker teken domein is een Joker teken vereist. Dat wil zeggen dat de onderwerpnaam van het certificaat ook het domein met het Joker teken moet bevatten.

> [!NOTE]
> Op dit moment is alleen het gebruik van uw eigen aangepaste SSL-certificaat optie beschikbaar voor het inschakelen van HTTPS voor joker tekens domeinen. Azure front-deur beheerde certificaten kunnen niet worden gebruikt voor Joker teken domeinen.

U kunt ervoor kiezen om hetzelfde Joker teken te gebruiken van Azure Key Vault of van Azure front-deur beheerde certificaten voor subdomeinen.

Als er een subdomein wordt toegevoegd voor een Joker teken domein waaraan al een certificaat is gekoppeld, kan HTTPS voor het subdomein niet worden uitgeschakeld. Het subdomein gebruikt de certificaat binding voor het domein met Joker tekens, tenzij een andere Key Vault of een door de Azure front-deur beheerd certificaat overschrijft.

## <a name="waf-policies"></a>WAF-beleid

WAF-beleid kan worden gekoppeld aan Joker teken domeinen, vergelijkbaar met andere domeinen. Een ander WAF-beleid kan worden toegepast op een subdomein van een Joker teken domein. Voor de subdomeinen moet u het WAF-beleid opgeven dat moet worden gebruikt, zelfs als het hetzelfde beleid is als het domein met het Joker teken. Subdomeinen nemen het WAF-beleid *niet* automatisch over van het Joker teken domein.

Als u niet wilt dat een WAF-beleid voor een subdomein wordt uitgevoerd, kunt u een leeg WAF-beleid maken zonder beheerde of aangepaste rules.

## <a name="routing-rules"></a>Regels voor doorsturen

Wanneer u een routerings regel configureert, kunt u een Joker teken domein als een front-end-host selecteren. U kunt ook een ander route gedrag hebben voor joker tekens en subdomeinen. Zoals beschreven in de [manier waarop Azure front-deur een route overeenkomst](front-door-route-matching.md)gebruikt, wordt de meest specifieke overeenkomst voor het domein in verschillende routerings regels gekozen tijdens runtime.

> [!IMPORTANT]
> U moet overeenkomende paden hebben in de regels voor door sturen of als uw clients fouten zien. U hebt bijvoorbeeld twee routerings regels als route 1 (`*.foo.com/*` toegewezen aan de back-end-groep a) en de route`bar.foo.com/somePath/*` 2 (toegewezen aan de back-end-pool B). Vervolgens ontvangt een aanvraag voor `bar.foo.com/anotherPath/*`. Azure front deur selecteert route 2 op basis van een specifiekere domein overeenkomst, alleen om geen overeenkomende paden te vinden in de routes.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [maken van een Azure front deur-profiel](quickstart-create-front-door.md).
- Meer informatie over het [toevoegen van een aangepast domein aan de voor deur van Azure](front-door-custom-domain.md).
- Meer informatie over het [inschakelen van HTTPS op een aangepast domein](front-door-custom-domain-https.md).
