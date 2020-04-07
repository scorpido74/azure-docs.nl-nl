---
title: Privé verbinding maken met een web-app met Azure Private Endpoint
description: Privé verbinding maken met een web-app met Azure Private Endpoint
author: ericgre
ms.assetid: 2dceac28-1ba6-4904-a15d-9e91d5ee162c
ms.topic: article
ms.date: 03/18/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.custom: fasttrack-edit
ms.openlocfilehash: 4d139cfa50afa94621066995314737fac70bbafe
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2020
ms.locfileid: "80756272"
---
# <a name="using-private-endpoints-for-azure-web-app-preview"></a>Privéeindpunten gebruiken voor Azure Web App (voorbeeld)

> [!Note]
> De preview is beschikbaar in de regio's Oost-VS en West-VS 2 voor alle PremiumV2 Windows- en Linux Web Apps en Elastic Premium-functies. 

U Privéeindpunt voor uw Azure Web App gebruiken om clients in uw privénetwerk veilig toegang te geven tot de app via Private Link. Het privéeindpunt gebruikt een IP-adres uit uw Azure VNet-adresruimte. Netwerkverkeer tussen een client op uw privénetwerk en de web-app loopt via het VNet en een privékoppeling op het Microsoft-backbonenetwerk, waardoor blootstelling van het openbare internet wordt geëlimineerd.

Met Privéeindpunt voor uw web-app u:

- Beveilig uw web-app door het privéeindpunt te configureren, waardoor openbare blootstelling wordt geëlimineerd.
- Maak veilig verbinding met Web App van on-premises netwerken die verbinding maken met het VNet via een VPN of ExpressRoute private peering.

Als u alleen een veilige verbinding nodig hebt tussen uw VNet en uw Web App, is een Service Endpoint de eenvoudigste oplossing. Als u de web-app ook on-premises moet bereiken via een Azure-gateway, een regionaal getouwerde VNet of een wereldwijd peered VNet, is Private Endpoint de oplossing.  

Zie [Serviceeindpunten voor][serviceendpoint]meer informatie.

## <a name="conceptual-overview"></a>Conceptueel overzicht

Een privéeindpunt is een speciale netwerkinterface (NIC) voor uw Azure Web App in een subnet in uw virtuele netwerk (VNet).
Wanneer u een privéeindpunt voor uw web-app maakt, biedt het veilige connectiviteit tussen clients in uw privénetwerk en uw web-app. Aan het privéeindpunt wordt een IP-adres toegewezen uit het IP-adresbereik van uw VNet.
De verbinding tussen het privéeindpunt en de web-app maakt gebruik van een beveiligde [privékoppeling.][privatelink] Privéeindpunt wordt alleen gebruikt voor binnenkomende stromen naar uw web-app. Uitgaande stromen gebruiken dit privéeindpunt niet, maar u uitgaande stromen naar uw netwerk injecteren in een ander subnet via de [VNet-integratiefunctie.][vnetintegrationfeature]

Het subnet waar u het Privéeindpunt aansluit, kan andere bronnen hebben, u hebt geen speciaal leeg subnet nodig.
U het privéeindpunt ook implementeren in een andere regio dan de web-app. 

> [!Note]
>De VNet-integratiefunctie kan niet hetzelfde subnet gebruiken als Private Endpoint, dit is een beperking van de VNet-integratiefunctie.

Vanuit een veiligheidsperspectief:

- Wanneer u Privéeindpunten inschakelt voor uw web-app, schakelt u alle openbare toegang uit.
- U meerdere privéeindpunten inschakelen in andere VNets en Subnetten, waaronder VNets in andere regio's.
- Het IP-adres van de Private Endpoint NIC moet dynamisch zijn, maar blijft hetzelfde totdat u het privéeindpunt verwijdert.
- De NIC van het privéeindpunt kan geen NSG-gekoppeld hebben.
- In het subnet met het privéeindpunt kan een NSG zijn gekoppeld, maar moet u de handhaving van het netwerkbeleid voor het privéeindpunt uitschakelen: zie [Netwerkbeleid uitschakelen voor privéeindpunten][disablesecuritype]. Als gevolg hiervan u de toegang tot uw privéeindpunt niet door een NSG filteren.
- Wanneer u Privéeindpunt inschakelt voor uw web-app, wordt de configuratie van de [toegangsbeperkingen][accessrestrictions] van de web-app niet geëvalueerd.
- U het risico op gegevensexfiltratie van het VNet verminderen door alle NSG-regels te verwijderen waarbij de bestemming internet of Azure-services tagt. Maar als u een privéeindpunt voor web-app's toevoegt in uw subnet, bereikt u elke web-app die in dezelfde implementatiestempel wordt gehost en wordt blootgesteld aan internet.

In de HTTP-logboeken van uw webapp vindt u het IP-adres van de clientbron. Dit wordt geïmplementeerd met behulp van het TCP Proxy-protocol en stuurt de IP-eigenschap van de client door naar de web-app. Zie [Verbindingsgegevens opvragen met TCP Proxy v2][tcpproxy]voor meer informatie.


  > [!div class="mx-imgBorder"]
  > ![Algemeen overzicht van Web App Private Endpoint](media/private-endpoint/global-schema-web-app.png)

## <a name="dns"></a>DNS

Aangezien deze functie in preview is, wijzigen we de DNS-vermelding niet tijdens de preview. U moet de DNS-vermelding in uw privé-DNS-server of Azure DNS-privézone zelf beheren.
Als u een aangepaste DNS-naam moet gebruiken, moet u de aangepaste naam toevoegen aan uw web-app. Tijdens het voorbeeld moet de aangepaste naam worden gevalideerd zoals elke aangepaste naam, met behulp van openbare DNS-resolutie. Zie [aangepaste DNS-validatie][dnsvalidation] voor meer informatie.

## <a name="pricing"></a>Prijzen

Zie [Azure Private Link-prijzen][pricing]voor prijsdetails.

## <a name="limitations"></a>Beperkingen

We verbeteren de Private Link-functie en private eindpunt regelmatig, controleer [dit artikel][pllimitations] voor actuele informatie over beperkingen.

## <a name="next-steps"></a>Volgende stappen

Privéeindpunt voor uw web-app implementeren via de portal zie [Hoe u privé verbinding maakt met een web-app][howtoguide]




<!--Links-->
[serviceendpoint]: https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview
[privatelink]: https://docs.microsoft.com/azure/private-link/private-link-overview
[vnetintegrationfeature]: https://docs.microsoft.com/azure/app-service/web-sites-integrate-with-vnet
[disablesecuritype]: https://docs.microsoft.com/azure/private-link/disable-private-endpoint-network-policy
[accessrestrictions]: https://docs.microsoft.com/azure/app-service/app-service-ip-restrictions
[tcpproxy]: ../../private-link/private-link-service-overview.md#getting-connection-information-using-tcp-proxy-v2
[dnsvalidation]: https://docs.microsoft.com/azure/app-service/app-service-web-tutorial-custom-domain
[pllimitations]: https://docs.microsoft.com/azure/private-link/private-endpoint-overview#limitations
[pricing]: https://azure.microsoft.com/pricing/details/private-link/
[howtoguide]: https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal
