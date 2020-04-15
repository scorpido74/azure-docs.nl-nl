---
title: Nieuwe nieuwe in Azure Application Gateway
description: Ontdek wat er nieuw is met Azure Application Gateway, zoals de nieuwste releasenotes, bekende problemen, bugfixes, afgeschafte functionaliteit en aankomende wijzigingen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 03/24/2020
ms.author: victorh
ms.openlocfilehash: 70014b5ab37a07e01eaa2db3d729b7d8af520842
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2020
ms.locfileid: "81311763"
---
# <a name="whats-new-in-azure-application-gateway"></a>Wat is er nieuw in Azure Application Gateway?

Azure Application Gateway wordt voortdurend bijgewerkt. Om op de hoogte te blijven van de meest recente ontwikkelingen, biedt dit artikel u informatie over:

- De nieuwste releases
- Bekende problemen
- Opgeloste fouten
- Afgeschafte functionaliteit

## <a name="new-features"></a>Nieuwe functies

|Functie  |Beschrijving  |Datum toegevoegd  |
|---------|---------|---------|
| Door de gebruiker gedefinieerde routes (UDR) op v2 (voorbeeld) |Door de gebruiker gedefinieerde routes worden nu ondersteund in sommige scenario's op Application Gateway v2 SKU's. Zie [Configuratieoverzicht Application Gateway](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet)voor meer informatie. |Maart 2020 |
|Affiniteitscookiewijzigingen |Wanneer cookie-based affinity is ingeschakeld, injecteert Application Gateway een andere identieke cookie genaamd *ApplicationGatewayAffinityCORS* naast de bestaande ApplicationGatewayAffinity cookie. *ApplicationGatewayAffinityCORS* heeft nog twee kenmerken toegevoegd (*SameSite=None; Veilig*), zodat plakkerige sessies worden gehandhaafd, zelfs voor cross-origin verzoeken. Zie [Application Gateway Cookie gebaseerde affiniteit](configuration-overview.md#cookie-based-affinity) voor meer informatie. |Februari 2020 |
|Sondeverbeteringen |Met de aangepaste sondeverbeteringen in Application Gateway v2 SKU hebben we [de configuratie van de sonde](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#create-probe-for-application-gateway-v2-sku)vereenvoudigd, [on-demand backend-statustests](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#test-backend-health-with-the-probe) vergemakkelijkt en [meer diagnostische informatie](https://docs.microsoft.com/azure/application-gateway/application-gateway-backend-health-troubleshooting#error-messages) toegevoegd om backend-statusproblemen op te lossen.  |Oktober 2019 |
|Meer statistieken |We hebben de volgende nieuwe statistieken toegevoegd om u te helpen uw Application Gateway v2 SKU: [Timing-gerelateerde statistieken,](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics)Backend-responsstatus, ontvangen bytes, verzonden bytes, client TLS-protocol en huidige compute units te controleren. Zie [Statistieken die worden ondersteund door Application Gateway V2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku). |Augustus 2019 |
|Aangepaste WAF-regels |Application Gateway WAF_v2 ondersteunt nu het maken van aangepaste regels. Zie [aangepaste regels voor toepassingsgateway .](custom-waf-rules-overview.md) |Juni 2019 |
|Autoscaling, zone redundantie, statische VIP-ondersteuning GA |Algemene beschikbaarheid voor v2 SKU, die automatisch schalen, zoneredundantie, verbetering van de prestaties, statische VIP's, Key Vault, Header herschrijven ondersteunt. Zie [Documentatie voor automatisch schalen van application gateway](application-gateway-autoscaling-zone-redundant.md). |April 2019 |
|Key Vault-integratie |Application Gateway ondersteunt nu integratie met Key Vault (in openbare preview) voor servercertificaten die zijn gekoppeld aan https-listeners. Zie [TLS-beëindiging met Key Vault-certificaten](key-vault-certs.md). |April 2019 |
|Header CRUD/Herschrijft     |U nu HTTP-headers herschrijven. Zie [Zelfstudie: Maak een toepassingsgateway en herschrijf HTTP-headers](tutorial-http-header-rewrite-powershell.md) voor meer informatie.|December 2018|
|WAF-configuratie- en uitsluitingslijst     |We hebben meer opties toegevoegd om u te helpen uw WAF te configureren en fout-positieven te verminderen. Zie Voor meer informatie: [limieten voor de grootte van webtoepassingen en uitsluitingslijsten](application-gateway-waf-configuration.md)voor meer informatie .|December 2018|
|Autoscaling, zone redundantie, statische VIP-ondersteuning      |Met de v2 SKU zijn er veel verbeteringen zoals Autoscaling, verbeterde prestaties en meer. Zie [Wat is Azure Application Gateway?](overview.md)|September 2018|
|Verwerkingsstop voor verbindingen     |Met het aftappen van verbindingen u leden op een elegante manier uit uw backendpools verwijderen. Zie [Verbinding aftappen](features.md#connection-draining)voor meer informatie.|September 2018|
|Aangepaste foutpagina's     |Met aangepaste foutpagina's u een foutpagina maken in de indeling van de rest van uw websites. Zie [Aangepaste foutpagina's van application gateway maken](custom-error.md)om dit in te schakelen.|September 2018|
|Verbeteringen in statistieken     |U een beter beeld krijgen van de status van uw application gateway met verbeterde statistieken. Zie [Back-endstatus, diagnostische logboeken en statistieken voor Application Gateway voor](application-gateway-diagnostics.md)het inschakelen van statistieken op uw application gateway.|Juni 2018|

## <a name="next-steps"></a>Volgende stappen

Zie Wat is Azure Application Gateway voor meer informatie over Azure Application [Gateway?](overview.md)
