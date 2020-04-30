---
title: Wat is er nieuw in Azure-toepassing gateway
description: Ontdek wat er nieuw is in Azure-toepassing gateway, zoals de nieuwste opmerkingen bij de release, bekende problemen, fout oplossingen, afgeschafte functionaliteit en aanstaande wijzigingen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 03/24/2020
ms.author: victorh
ms.openlocfilehash: 70014b5ab37a07e01eaa2db3d729b7d8af520842
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81311763"
---
# <a name="whats-new-in-azure-application-gateway"></a>Wat is er nieuw in Azure-toepassing gateway?

Azure-toepassing gateway wordt doorlopend bijgewerkt. Dit artikel bevat informatie over het volgende om te blijven werken met de meest recente ontwikkelingen:

- De nieuwste releases
- Bekende problemen
- Opgeloste fouten
- Afgeschafte functionaliteit

## <a name="new-features"></a>Nieuwe functies

|Functie  |Beschrijving  |Toegevoegd op  |
|---------|---------|---------|
| Door de gebruiker gedefinieerde routes (UDR) in v2 (preview-versie) |Door de gebruiker gedefinieerde routes worden nu in sommige scenario's ondersteund op Application Gateway v2-Sku's. Zie [Application Gateway configuratie-overzicht](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet)voor meer informatie. |Maart 2020 |
|Cookie wijzigingen in affiniteit |Wanneer affiniteit op basis van cookies is ingeschakeld, wordt door Application Gateway een andere identieke cookie met de naam *ApplicationGatewayAffinityCORS* geïnjecteerd naast de bestaande ApplicationGatewayAffinity cookie. Er zijn twee extra kenmerken aan *ApplicationGatewayAffinityCORS* toegevoegd (*SameSite = none; Veilig*) zodat plak sessies zelfs voor cross-Origin-aanvragen worden bewaard. Zie [Application Gateway affiniteit op basis van cookies](configuration-overview.md#cookie-based-affinity) voor meer informatie. |Februari 2020 |
|Test uitbreidingen |Met de aangepaste test verbeteringen in Application Gateway v2 SKU hebben we een vereenvoudigde [test configuratie](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#create-probe-for-application-gateway-v2-sku), de [back-upstatus testen op aanvraag](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#test-backend-health-with-the-probe) en [meer diagnostische informatie](https://docs.microsoft.com/azure/application-gateway/application-gateway-backend-health-troubleshooting#error-messages) toegevoegd om u te helpen bij het oplossen van problemen met de back-end.  |Oktober 2019 |
|Meer meet waarden |We hebben de volgende nieuwe metrische gegevens toegevoegd om u te helpen bij het bewaken van uw Application Gateway v2-SKU: [metrische gegevens met betrekking tot timing](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics), reactie status van back-end, ontvangen bytes, verzonden bytes, client TLS-protocol en huidige reken eenheden. Zie [metrische gegevens die worden ondersteund door de SKU van Application Gateway v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku). |Augustus 2019 |
|Aangepaste WAF-regels |Application Gateway WAF_v2 ondersteunt nu het maken van aangepaste regels. Zie [Application Gateway aangepaste regels](custom-waf-rules-overview.md). |Juni 2019 |
|Automatisch schalen, zone redundantie, statische VIP-ondersteuning GA |Algemene Beschik baarheid voor v2-SKU, die ondersteuning biedt voor automatisch schalen, zone redundantie, prestaties verbeteren, statische Vip's, Key Vault, header herschrijven. Zie [Application Gateway documentatie](application-gateway-autoscaling-zone-redundant.md)voor automatisch schalen. |April 2019 |
|Integratie van Key Vault |Application Gateway ondersteunt nu integratie met Key Vault (in open bare preview) voor server certificaten die zijn gekoppeld aan listeners waarvoor HTTPS is ingeschakeld. Zie [TLS-beëindiging met Key Vault certificaten](key-vault-certs.md). |April 2019 |
|RUWE koptekst/herschrijf bewerkingen     |U kunt nu HTTP-headers herschrijven. Zie [zelf studie: een toepassings gateway maken en HTTP-headers herschrijven](tutorial-http-header-rewrite-powershell.md) voor meer informatie.|December 2018|
|WAF-configuratie en uitsluitings lijst     |We hebben meer opties toegevoegd om u te helpen uw WAF te configureren en valse positieven te verminderen. Zie voor meer informatie [Web Application firewall-aanvraag grootte limieten en uitsluitings lijsten](application-gateway-waf-configuration.md).|December 2018|
|Automatisch schalen, zone redundantie, ondersteuning van statische VIP      |Met de v2-SKU zijn er veel verbeteringen, zoals automatisch schalen, betere prestaties en meer. Zie [Wat is Azure-toepassing gateway?](overview.md) voor meer informatie.|September 2018|
|Verwerkingsstop voor verbindingen     |Met het verbreken van de verbinding kunt u leden zonder problemen verwijderen uit uw back-endservers. Zie [verbindings afvoer](features.md#connection-draining)voor meer informatie.|September 2018|
|Aangepaste foutpagina's     |Met aangepaste fout pagina's kunt u een fout pagina maken in de indeling van de rest van uw websites. Zie [Application Gateway aangepaste fout pagina's maken](custom-error.md)om dit in te scha kelen.|September 2018|
|Verbeteringen voor metrische gegevens     |U kunt een beter beeld krijgen van de status van uw Application Gateway met uitgebreide metrische gegevens. Als u metrische gegevens op uw Application Gateway wilt inschakelen, raadpleegt u de status van de [back-end, Diagnostische logboeken en metrische gegevens voor Application Gateway](application-gateway-diagnostics.md).|Juni 2018|

## <a name="next-steps"></a>Volgende stappen

Zie [Wat is Azure-toepassing gateway?](overview.md) voor meer informatie over Azure-toepassing gateway.
