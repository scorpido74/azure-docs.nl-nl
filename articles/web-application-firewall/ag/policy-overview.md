---
title: Overzicht van het beleid voor Azure Web Application firewall (WAF)
description: Dit artikel bevat een overzicht van het globale Web Application firewall (WAF), per site-en per-URI-beleid.
services: web-application-firewall
ms.topic: article
author: winthrop28
ms.service: web-application-firewall
ms.date: 02/01/2020
ms.author: victorh
ms.openlocfilehash: 10a90a7f94633fac52086953697eb90a98d9509d
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86143840"
---
# <a name="azure-web-application-firewall-waf-policy-overview"></a>Overzicht van het beleid voor Azure Web Application firewall (WAF)

Web Application firewall-beleid bevat alle WAF-instellingen en-configuraties. Dit omvat uitsluitingen, aangepaste regels, beheerde regels, enzovoort. Deze beleids regels worden vervolgens gekoppeld aan een toepassings gateway (globaal), een listener (per site) of een op een pad gebaseerde regel (per URI) die ze van kracht kunnen laten worden.

> [!NOTE]
> Azure Web Application firewall (WAF) per URI-beleid bevindt zich in de open bare preview.
> 
> Deze openbare preview-versie wordt aangeboden zonder serviceovereenkomst en moet niet worden gebruikt voor productieworkloads. Bepaalde functies worden mogelijk niet ondersteund, zijn mogelijk beperkt of zijn mogelijk niet beschikbaar op alle Azure-locaties. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Er is geen limiet voor het aantal beleids regels dat u kunt maken. Wanneer u een beleid maakt, moet dit zijn gekoppeld aan een toepassings gateway om van kracht te worden. Het kan worden gekoppeld aan elke combi natie van toepassings gateways, listeners en regels op basis van een pad.

## <a name="global-waf-policy"></a>Beleid voor globale WAF

Wanneer u een WAF-beleid globaal koppelt, wordt elke site achter uw Application Gateway WAF beveiligd met dezelfde beheerde regels, aangepaste regels, uitsluitingen en andere geconfigureerde instellingen.

Als u één beleid wilt Toep assen op alle sites, kunt u het beleid koppelen aan de toepassings gateway. Zie voor meer informatie [Web Application firewall-beleids regels maken voor Application Gateway](create-waf-policy-ag.md) om een WAF-beleid te maken en toe te passen met behulp van de Azure Portal. 

## <a name="per-site-waf-policy"></a>WAF-beleid per site

Met WAF-beleid per site kunt u meerdere sites beveiligen met verschillende beveiligings behoeften achter één WAF met behulp van beleid per site. Als er bijvoorbeeld vijf sites achter uw WAF zijn, kunt u vijf afzonderlijke WAF-beleids regels (één voor elke listener) hebben om de uitsluitingen, aangepaste regels, beheerde regel sets en alle andere WAF-instellingen voor elke site aan te passen.

Stel dat er op uw toepassings gateway een globaal beleid is toegepast. Vervolgens past u een ander beleid toe op een listener op die toepassings gateway. Het beleid van de listener wordt nu alleen van kracht voor die listener. Het globale beleid van de toepassings gateway is nog steeds van toepassing op alle andere listeners en op pad gebaseerde regels waaraan geen specifiek beleid is toegewezen.

## <a name="per-uri-policy"></a>Beleid per URI

Voor nog meer aanpassing van het URI-niveau, kunt u een WAF-beleid koppelen aan een op pad gebaseerde regel. Als er bepaalde pagina's binnen één site zijn waarvoor verschillende beleids regels zijn vereist, kunt u wijzigingen aanbrengen in het WAF-beleid dat alleen van invloed is op een opgegeven URI. Dit kan van toepassing zijn op een betaling of aanmeldings pagina, of andere Uri's die een nog specifiek WAF-beleid nodig hebben dan de andere sites achter uw WAF.

Net als bij een WAF per site worden minder specifieke beleids regels genegeerd. Dit betekent dat een beleid per URI op een URL-pad-map overschrijft elk per site of globaal WAF-beleid hierboven.

## <a name="example"></a>Voorbeeld

Stel dat u drie sites hebt: contoso.com, fabrikam.com en adatum.com die zich achter dezelfde toepassings gateway bevindt. U wilt dat een WAF wordt toegepast op alle drie de sites, maar u hebt extra beveiliging nodig met adatum.com, omdat klanten producten bezoeken, zoeken en kopen.

U kunt een globaal beleid Toep assen op de WAF, met enkele basis instellingen, uitsluitingen of aangepaste regels, indien nodig om te voor komen dat bepaalde valse positieven verkeer blok keren. In dit geval zijn er geen globale SQL-injectie regels actief, omdat fabrikam.com en contoso.com statische pagina's zijn zonder SQL-back-end. Zodat u deze regels in het globale beleid kunt uitschakelen.

Dit globale beleid is geschikt voor contoso.com en fabrikam.com, maar u moet voorzichtig zijn met adatum.com waar aanmeldings gegevens en-betalingen worden afgehandeld. U kunt een beleid per site Toep assen op de adatum-listener en de SQL-regels blijven actief. Stel dat er een cookie is die een bepaald verkeer blokkeert. u kunt dus een uitsluiting voor die cookie maken om de fout-positieve waarde te stoppen. 

De adatum.com/payments-URI is waar u voorzichtig moet zijn. Pas een ander beleid op die URI toe en zorg ervoor dat alle regels ingeschakeld blijven, en verwijder ook alle uitsluitingen.

In dit voor beeld hebt u een globaal beleid dat van toepassing is op twee sites. U hebt een beleid per site dat van toepassing is op één site en vervolgens per URI-beleid dat van toepassing is op één specifieke regel op basis van een pad. Zie (koppeling hier invoegen als deze bestaat) instructies: per site en per URI-beleid maken voor de bijbehorende Power shell voor dit voor beeld.

## <a name="existing-waf-configurations"></a>Bestaande WAF-configuraties

Alle nieuwe WAF-instellingen van de firewall voor webtoepassingen (aangepaste regels, configuraties van beheerde regel sets, uitsluitingen, enzovoort) bestaan in een WAF-beleid. Als u een bestaande WAF hebt, zijn deze instellingen mogelijk nog aanwezig in de WAF-configuratie. Voor meer informatie over het overstappen op het nieuwe WAF-beleid, [migreert u WAF config naar een WAF-beleid](https://docs.microsoft.com/azure/web-application-firewall/ag/migrate-policy). 


## <a name="next-steps"></a>Volgende stappen

Maak per-site-en per-URI-beleid met behulp van Azure PowerShell.
