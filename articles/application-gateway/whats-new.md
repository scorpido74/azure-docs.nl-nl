---
title: Nieuwe functies in Azure Application Gateway
description: Ontdek wat er nieuw is in Azure Application Gateway, zoals de laatste opmerkingen bij de release, bekende problemen, opgeloste problemen, verminderde functionaliteit en aankomende wijzigingen.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: overview
ms.date: 06/10/2020
ms.author: victorh
ms.openlocfilehash: dd6ec2057ede076511b567c013fc1b6fa12d8281
ms.sourcegitcommit: eeba08c8eaa1d724635dcf3a5e931993c848c633
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/10/2020
ms.locfileid: "84669729"
---
# <a name="whats-new-in-azure-application-gateway"></a>Wat zijn de nieuwe functies in Azure Application Gateway?

Azure Application Gateway wordt voortdurend bijgewerkt. Om u op de hoogte te houden van de nieuwste ontwikkelingen, biedt dit artikel u informatie over:

- De nieuwste releases
- Bekende problemen
- Opgeloste fouten
- Afgeschafte functionaliteit

## <a name="new-features"></a>Nieuwe functies

|Functie  |Beschrijving  |Datum toegevoegd  |
|---------|---------|---------|
| AGIC (Application Gateway Ingress Controller) als AKS-invoegtoepassing (preview) |Application Gateway Ingress Controller kan nu met één regel worden geïmplementeerd als een systeemeigen AKS-invoegtoepassing via Azure CLI. Omdat AGIC een AKS-invoegtoepassing is, kan het een volledig beheerde service worden, terwijl het nog steeds wordt uitgevoerd in het AKS-cluster van de klant. Zie [Verschillen tussen AGIC-invoegtoepassingen](ingress-controller-overview.md#difference-between-helm-deployment-and-aks-add-on) voor meer informatie. |Juni 2020 |
| UDR (door de gebruiker gedefinieerde routes) in v2 (preview) |Door de gebruiker gedefinieerde routes worden nu ondersteund in sommige scenario's in Application Gateway v2 SKU's. Zie [Overzicht van Application Gateway-configuratie](configuration-overview.md#user-defined-routes-supported-on-the-application-gateway-subnet) voor meer informatie. |Maart 2020 |
|Wijzigingen in affiniteit op basis van cookies |Wanneer affiniteit op basis van cookies is ingeschakeld, wordt via Application Gateway een andere identieke cookie geplaatst met de naam *ApplicationGatewayAffinityCORS*, naast de bestaande cookie ApplicationGatewayAffinity. Aan *ApplicationGatewayAffinityCORS* zijn nog twee kenmerken toegevoegd (*SameSite=None; Secure*) zodat tijdelijke sessies behouden blijven, zelfs voor oorsprongoverschrijdende aanvragen. Zie [Affiniteit op basis van Application Gateway-cookie](configuration-overview.md#cookie-based-affinity) voor meer informatie. |Februari 2020 |
|Testuitbreidingen |Met de aangepaste testuitbreidingen in de Application Gateway v2 SKU is [testconfiguratie](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#create-probe-for-application-gateway-v2-sku) vereenvoudigd, zijn [back-end-statustests](https://docs.microsoft.com/azure/application-gateway/application-gateway-create-probe-portal#test-backend-health-with-the-probe) mogelijk, en is [meer diagnostische informatie](https://docs.microsoft.com/azure/application-gateway/application-gateway-backend-health-troubleshooting#error-messages) toegevoegd om u te helpen bij het oplossen van problemen met de back-end-status.  |Oktober 2019 |
|Meer metrische gegevens |We hebben de volgende nieuwe metrische gegevens toegevoegd om u te helpen uw Application Gateway v2 SKU te bewaken: [Metrische gegevens met betrekking tot timing](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#timing-metrics), reactiestatus van back-end, ontvangen bytes, verzonden bytes, TLS-protocol van de client, en huidige rekeneenheden. Zie [Metrische gegevens die worden ondersteund in Application Gateway v2 SKU](https://docs.microsoft.com/azure/application-gateway/application-gateway-metrics#metrics-supported-by-application-gateway-v2-sku). |Augustus 2019 |
|Aangepaste WAF-regels |Application Gateway WAF_v2 ondersteunt nu het maken van aangepaste regels. Zie [Aangepaste regels voor Application Gateway](custom-waf-rules-overview.md). |Juni 2019 |
|Automatisch schalen, zoneredundantie, statische VIP-ondersteuning GA |Algemene beschikbaarheid voor v2 SKU, die ondersteuning biedt voor automatisch schalen, zoneredundantie, prestatieverbetering, statische VIP's, Key Vault, opnieuw schrijven van headers. Raadpleeg de [documentatie over automatisch schalen in Application Gateway](application-gateway-autoscaling-zone-redundant.md). |April 2019 |
|Key Vault-integratie |Application Gateway biedt nu ondersteuning voor integratie met Key Vault (in de openbare preview-versie) voor servercertificaten die zijn gekoppeld aan listeners waarvoor HTTPS is ingeschakeld. Zie [TLS-beëindiging met Key Vault-certificaten](key-vault-certs.md). |April 2019 |
|CRUD/herschrijfbewerkingen voor headers     |U kunt HTTP-headers nu opnieuw schrijven. Zie [Zelfstudie: Een toepassingsgateway maken en HTTP-headers opnieuw schrijven ](tutorial-http-header-rewrite-powershell.md) voor meer informatie.|December 2018|
|WAF-configuratie en uitsluitingslijst     |We hebben meer opties toegevoegd om u te helpen uw WAF te configureren en fout-positieven te verminderen. Zie [Limieten voor aanvraaggrootten en uitsluitingslijsten in Web Application Firewall](application-gateway-waf-configuration.md) voor meer informatie.|December 2018|
|Automatisch schalen, zoneredundantie, ondersteuning voor statische VIP      |De v2 SKU bevat veel verbeteringen, zoals automatisch schalen, verbeterde prestaties, en meer. Zie [Wat is Azure Application Gateway?](overview.md) voor meer informatie.|September 2018|
|Verwerkingsstop voor verbindingen     |Met verwerkingsstop voor verbindingen kunt u zonder problemen leden verwijderen uit uw back-end-pools. Zie [Verwerkingsstop voor verbindingen](features.md#connection-draining) voor meer informatie.|September 2018|
|Aangepaste foutpagina's     |Met aangepaste foutpagina's kunt u een foutpagina maken in de indeling van de rest van uw websites. Zie [Aangepaste foutpagina's maken voor Application Gateway](custom-error.md) om dit in te schakelen.|September 2018|
|Verbeteringen voor metrische gegevens     |U kunt een beter beeld krijgen van de status van uw toepassingsgateway met behulp van uitgebreide metrische gegevens. Zie [Back-end-status, diagnostische logboeken, en metrische gegevens voor Application Gateway](application-gateway-diagnostics.md) als u metrische gegevens wilt inschakelen voor uw toepassingsgateway.|Juni 2018|

## <a name="next-steps"></a>Volgende stappen

Zie [Wat is Azure Application Gateway?](overview.md) voor meer informatie over Azure Application Gateway.
