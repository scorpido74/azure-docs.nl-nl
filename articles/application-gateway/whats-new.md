---
title: Wat is er nieuw in Azure-toepassing gateway
description: Ontdek wat er nieuw is in Azure-toepassing gateway, zoals de nieuwste opmerkingen bij de release, bekende problemen, fout oplossingen, afgeschafte functionaliteit en aanstaande wijzigingen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 4/30/2019
ms.author: victorh
ms.openlocfilehash: 6755825aa123b3d7d1eed23291790ff3dc477ac5
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/05/2019
ms.locfileid: "70382470"
---
# <a name="whats-new-in-azure-application-gateway"></a>Wat is er nieuw in Azure-toepassing gateway?

Azure-toepassing gateway wordt doorlopend bijgewerkt. Als u wilt bijhouden met de meest recente ontwikkelingen, vindt in dit artikel u informatie over:

- De meest recente versies
- Bekende problemen
- Opgeloste fouten
- Afgeschafte functies

## <a name="new-features"></a>Nieuwe functies

|Functie  |Description  |Datum toegevoegd  |
|---------|---------|---------|
|Meer meet waarden |We hebben de volgende nieuwe metrische gegevens toegevoegd om u te helpen uw toepassing-gateway v2-SKU te bewaken: [Metrische gegevens met betrekking tot timing](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics), status van back-upantwoord, ontvangen bytes, verzonden bytes, TLS-protocol van client en huidige reken eenheden. Zie [metrische gegevens die worden ondersteund door de SKU van Application Gateway v2](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku). |2019 augustus |
|Aangepaste WAF-regels |Toepassing gateway WAF_v2 ondersteunt nu het maken van aangepaste regels. Zie [Application Gateway aangepaste regels](custom-waf-rules-overview.md). |Juni 2019 |
|Automatisch schalen, zone redundantie, statische VIP-ondersteuning GA |Algemene Beschik baarheid voor v2 SKU, die ondersteuning biedt voor automatisch schalen, zone redundantie, betere prestaties, statische Vip's, Key Vault, header herschrijven. Zie [Application Gateway documentatie](application-gateway-autoscaling-zone-redundant.md)voor automatisch schalen. |April 2019 |
|Integratie van Key Vault |Application Gateway ondersteunt nu integratie met Key Vault (in open bare preview) voor server certificaten die zijn gekoppeld aan listeners waarvoor HTTPS is ingeschakeld. Zie [SSL-beëindiging met Key Vault certificaten](key-vault-certs.md). |April 2019 |
|RUWE koptekst/herschrijf bewerkingen     |U kunt nu HTTP-headers herschrijven. Zie [zelf studie: Maak een toepassings gateway en herschrijf http-](tutorial-http-header-rewrite-powershell.md) headers voor meer informatie.|December 2018|
|WAF-configuratie en uitsluitings lijst     |We hebben meer opties toegevoegd om u te helpen uw WAF te configureren en valse positieven te verminderen. Zie de limieten voor de grootte van de [Web Application firewall-aanvraag en uitsluitings lijsten](application-gateway-waf-configuration.md) voor meer informatie.|December 2018|
|Automatisch schalen, zone redundantie, ondersteuning van statische VIP      |Met de v2-SKU zijn er veel verbeteringen, zoals automatisch schalen, betere prestaties en meer. Zie [Wat is Azure-toepassing gateway?](overview.md) voor meer informatie.|September 2018|
|Verwerkingsstop voor verbindingen     |Met het verbreken van de verbinding kunt u leden zonder problemen verwijderen uit uw back-endservers. Zie [verbindings afvoer](overview.md#connection-draining)voor meer informatie.|September 2018|
|Aangepaste foutpagina's     |Met aangepaste fout pagina's kunt u een fout pagina maken in de indeling van de rest van uw websites. Zie [Application Gateway aangepaste fout pagina's maken](custom-error.md)om dit in te scha kelen.|September 2018|
|Verbeteringen voor metrische gegevens     |U kunt een beter beeld krijgen van de status van uw Application Gateway met uitgebreide metrische gegevens. Als u metrische gegevens op uw Application Gateway wilt inschakelen, raadpleegt u de status van de [back-end, Diagnostische logboeken en metrische gegevens voor Application Gateway](application-gateway-diagnostics.md).|Juni 2018|

## <a name="next-steps"></a>Volgende stappen

Zie [Wat is Azure-toepassing gateway?](overview.md) voor meer informatie over Azure-toepassing gateway.
