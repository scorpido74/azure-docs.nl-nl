---
title: Overzicht van het Beleid van Azure Web Application Firewall (WAF)
description: Dit artikel is een overzicht van het wereldwijde beleid van Web Application Firewall (WAF) per site en per URI.
services: web-application-firewall
ms.topic: article
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/01/2020
ms.author: victorh
ms.openlocfilehash: fff4fb5707c07098fd7fac261a36909224365cdf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060275"
---
# <a name="azure-web-application-firewall-waf-policy-overview"></a>Overzicht van het Beleid van Azure Web Application Firewall (WAF)

WebApplication Firewall-beleid bevat alle WAF-instellingen en -configuraties. Dit omvat uitsluitingen, aangepaste regels, beheerde regels, enzovoort. Deze beleidsregels worden vervolgens gekoppeld aan een toepassingsgateway (globaal), een listener (per site) of een op paden gebaseerde regel (per URI) om deze regels van kracht te laten worden.

> [!NOTE]
> Azure Web Application Firewall (WAF) per site en per URI-beleid staan in Public Preview.
> 
> Deze openbare preview-versie wordt aangeboden zonder serviceovereenkomst en moet niet worden gebruikt voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar op alle Azure-locaties. Zie [Aanvullende gebruiksvoorwaarden voor Microsoft Azure Previews voor](https://azure.microsoft.com/support/legal/preview-supplemental-terms/)meer informatie.

Er is geen limiet aan het aantal beleidsregels dat u maken. Wanneer u een beleid maakt, moet het worden gekoppeld aan een toepassingsgateway om van kracht te worden. Het kan worden gekoppeld aan elke combinatie van toepassingsgateways, listeners en regels op basis van paden.

## <a name="global-waf-policy"></a>Wereldwijd WAF-beleid

Wanneer u een WAF-beleid wereldwijd koppelt, wordt elke site achter uw Application Gateway WAF beveiligd met dezelfde beheerde regels, aangepaste regels, uitsluitingen en andere geconfigureerde instellingen.

Als u wilt dat één beleid van toepassing is op alle sites, u het beleid koppelen aan de toepassingsgateway. Zie Voor meer informatie [het beleid voor webtoepassingsfirewall maken voor Application Gateway](create-waf-policy-ag.md) om een WAF-beleid te maken en toe te passen met behulp van de Azure-portal. 

## <a name="per-site-waf-policy"></a>WAF-beleid per locatie

Met waf-beleid per site u meerdere sites met verschillende beveiligingsbehoeften achter één WAF beschermen met behulp van beleid per site. Als er bijvoorbeeld vijf sites achter uw WAF staan, u vijf afzonderlijke WAF-beleidsregels (één voor elke listener) hebben om de uitsluitingen, aangepaste regels, beheerde regelsets en alle andere WAF-instellingen voor elke site aan te passen.

Stel dat uw toepassingsgateway een globaal beleid heeft dat erop wordt toegepast. Vervolgens past u een ander beleid toe op een listener op die toepassingsgateway. Het beleid van de luisteraar wordt nu van kracht voor alleen die luisteraar. Het globale beleid van de toepassingsgateway is nog steeds van toepassing op alle andere luisteraars en op paden gebaseerde regels die geen specifiek beleid aan hen hebben toegewezen.

## <a name="per-uri-policy"></a>Beleid per URI

Voor nog meer aanpassing tot op het URI-niveau u een WAF-beleid koppelen aan een op paden gebaseerde regel. Als er bepaalde pagina's binnen één site zijn waarvoor een ander beleid vereist is, u wijzigingen aanbrengen in het WAF-beleid dat alleen van invloed is op een bepaalde URI. Dit kan van toepassing zijn op een betaal- of aanmeldingspagina of op andere URI's die een nog specifieker WAF-beleid nodig hebben dan de andere sites achter uw WAF.

Net als bij WAF-beleid per site overschrijft specifieker beleid minder specifieke beleidsregels. Dit betekent dat een per-URI-beleid op een URL-padkaart een per-site of globaal WAF-beleid erboven overschrijft.

## <a name="example"></a>Voorbeeld

Stel dat u drie sites hebt: contoso.com, fabrikam.com en adatum.com allemaal achter dezelfde toepassingsgateway. U wilt dat een WAF wordt toegepast op alle drie de sites, maar u hebt extra beveiliging nodig bij adatum.com, want dat is waar klanten producten bezoeken, bladeren en kopen.

U een globaal beleid toepassen op de WAF, met enkele basisinstellingen, uitsluitingen of aangepaste regels, indien nodig om te voorkomen dat sommige false positives het verkeer blokkeren. In dit geval is het niet nodig om globale SQL-injectieregels te hebben uitgevoerd, omdat fabrikam.com en contoso.com statische pagina's zijn zonder SQL-backend. U deze regels dus uitschakelen in het wereldwijde beleid.

Dit globale beleid is geschikt voor contoso.com en fabrikam.com, maar u moet voorzichtiger zijn met adatum.com waar inloggegevens en betalingen worden afgehandeld. U een beleid per site toepassen op de adatumlistener en de SQL-regels laten draaien. Ga er ook van uit dat er een cookie is die verkeer blokkeert, zodat u een uitsluiting voor die cookie maken om het fout-positieve te stoppen. 

De adatum.com/payments URI is waar je voorzichtig moet zijn. Dus pas een ander beleid op dat URI en laat alle regels ingeschakeld, en verwijder ook alle uitsluitingen.

In dit voorbeeld hebt u een globaal beleid dat van toepassing is op twee sites. U hebt een beleid per site dat van toepassing is op één site en vervolgens een per-URI-beleid dat van toepassing is op één specifieke padregel. Zie (link hier invoegen wanneer deze bestaat) how-to-create per-site en per-URI-beleid voor de bijbehorende PowerShell voor dit voorbeeld.

## <a name="existing-waf-configurations"></a>Bestaande WAF-configuraties

Alle waf-instellingen van de nieuwe Web Application Firewall (aangepaste regels, beheerde regelsetconfiguraties, uitsluitingen, enzovoort.) bestaan in een WAF-beleid. Als u een bestaande WAF hebt, kunnen deze instellingen nog steeds bestaan in uw WAF-configuratie. Voor meer informatie over de overgang naar het nieuwe WAF-beleid, [Migreer WAF Config naar een WAF-beleid](https://docs.microsoft.com/azure/web-application-firewall/ag/migrate-policy). 


## <a name="next-steps"></a>Volgende stappen

Maak beleid per site en per URI met Azure PowerShell.
