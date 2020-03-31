---
title: Privéeindpunten gebruiken voor Azure App-configuratie
description: Uw App-configuratiewinkel beveiligen met privéeindpunten
services: azure-app-configuration
author: lisaguthrie
ms.service: azure-app-configuration
ms.topic: conceptual
ms.date: 3/12/2020
ms.author: lcozzens
ms.openlocfilehash: f18672b9e3a368a833fc8cba279d748dfe3c2a9e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366765"
---
# <a name="using-private-endpoints-for-azure-app-configuration"></a>Privéeindpunten gebruiken voor Azure App-configuratie

U [privéeindpunten](../private-link/private-endpoint-overview.md) voor Azure App-configuratie gebruiken om clients op een virtueel netwerk (VNet) veilig toegang te geven tot gegevens via een [privékoppeling.](../private-link/private-link-overview.md) Het privéeindpunt gebruikt een IP-adres uit de VNet-adresruimte voor uw App Configuration Store. Netwerkverkeer tussen de clients op de VNet en de App Configuration store doorkruist het VNet met behulp van een privékoppeling op het Microsoft-backbonenetwerk, waardoor blootstelling aan het openbare internet wordt geëlimineerd.

Als u privéeindpunten gebruikt voor uw App Configuration Store, u:
- Beveilig de configuratiegegevens van uw toepassing door de firewall te configureren om alle verbindingen met app-configuratie op het openbare eindpunt te blokkeren.
- Verhoog de beveiliging van het virtuele netwerk (VNet) zodat gegevens niet uit het VNet ontsnappen.
- Maak veilig verbinding met de App Configuration Store via on-premises netwerken die verbinding maken met het VNet via [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md) of [ExpressRoutes](../expressroute/expressroute-locations.md) met private-peering.

> [!NOTE]
> Azure App Configuration biedt het gebruik van privéeindpunten als openbare preview. Openbare preview-aanbiedingen stellen klanten in staat om te experimenteren met nieuwe functies voordat ze officieel worden uitgebracht.  Openbare preview-functies en services zijn niet bedoeld voor productiegebruik.

## <a name="conceptual-overview"></a>Conceptueel overzicht

Een privéeindpunt is een speciale netwerkinterface voor een Azure-service in uw [Virtual Network](../virtual-network/virtual-networks-overview.md) (VNet). Wanneer u een privéeindpunt maakt voor uw App Config-winkel, biedt dit veilige connectiviteit tussen clients op uw VNet en uw configuratiewinkel. Aan het privéeindpunt wordt een IP-adres toegewezen uit het IP-adresbereik van uw VNet. De verbinding tussen het privéeindpunt en het configuratiearchief maakt gebruik van een beveiligde privékoppeling.

Toepassingen in het VNet kunnen verbinding maken met het configuratiearchief via het privéeindpunt **met behulp van dezelfde verbindingstekenreeksen en autorisatiemechanismen die ze anders zouden gebruiken.** Privéeindpunten kunnen worden gebruikt met alle protocollen die worden ondersteund door de App Configuration Store.

Hoewel app-configuratie geen serviceeindpunten ondersteunt, kunnen privéeindpunten worden gemaakt in subnetten die [Service-eindpunten gebruiken.](../virtual-network/virtual-network-service-endpoints-overview.md) Clients in een subnet kunnen veilig verbinding maken met een App Configuration Store met behulp van het privéeindpunt terwijl ze serviceeindpunten gebruiken om toegang te krijgen tot anderen.  

Wanneer u een privéeindpunt maakt voor een service in uw VNet, wordt een toestemmingsaanvraag ter goedkeuring verzonden naar de eigenaar van het serviceaccount. Als de gebruiker die het privéeindpunt aanvraagt ook eigenaar is van het account, wordt deze toestemmingsaanvraag automatisch goedgekeurd.

Eigenaren van service-account kunnen toestemmingsaanvragen `Private Endpoints` en privéeindpunten beheren via het tabblad van de config-winkel in de [Azure-portal.](https://portal.azure.com)

### <a name="private-endpoints-for-app-configuration"></a>Privéeindpunten voor app-configuratie 

Wanneer u een privéeindpunt maakt, moet u het app-configuratiearchief opgeven waarmee het wordt verbonden. Als u meerdere app-configuratie-exemplaren in een account hebt, hebt u voor elke winkel een apart privéeindpunt nodig.

### <a name="connecting-to-private-endpoints"></a>Verbinding maken met privéeindpunten

Azure vertrouwt op DNS-resolutie om verbindingen van het VNet naar het configuratiearchief over een privékoppeling te routeren. U snel verbindingstekenreeksen vinden in de Azure-portal door uw App Configuration Store te selecteren en vervolgens **Toegangssleutels instellingen** > **te**selecteren.  

> [!IMPORTANT]
> Gebruik dezelfde verbindingstekenreeks om verbinding te maken met uw App Configuration Store met behulp van privéeindpunten als u zou gebruiken voor een openbaar eindpunt. Maak geen verbinding met het `privatelink` opslagaccount via de URL van het subdomein.

## <a name="dns-changes-for-private-endpoints"></a>DNS-wijzigingen voor privéeindpunten

Wanneer u een privéeindpunt maakt, wordt de DNS CNAME-bronrecord voor het configuratiearchief `privatelink`bijgewerkt naar een alias in een subdomein met het voorvoegsel . Azure maakt ook een [privé-DNS-zone](../dns/private-dns-overview.md) die overeenkomt met het `privatelink` subdomein, met de DNS A-bronrecords voor de privéeindpunten.

Wanneer u de URL van het eindpunt van buiten het VNet oplost, wordt deze opgelost tot het openbare eindpunt van de winkel. Wanneer het privéeindpunt wordt opgelost vanuit het VNet dat het privéeindpunt host, wordt de URL van het eindpunt opgelost tot het privéeindpunt.

U de toegang voor clients buiten het VNet beheren via het openbare eindpunt met behulp van de Azure Firewall-service.

Deze aanpak maakt toegang tot de winkel **met dezelfde verbindingstekenreeks** mogelijk voor clients op de VNet die de privéeindpunten hosten en clients buiten het VNet.

Als u een aangepaste DNS-server in uw netwerk gebruikt, moeten clients de volledig gekwalificeerde domeinnaam (FQDN) voor het serviceeindpunt kunnen oplossen naar het privé-eindpunt-IP-adres. Configureer uw DNS-server om uw subdomein voor privékoppelingen te delegeren `AppConfigInstanceA.privatelink.azconfig.io` aan de privé-DNS-zone voor de VNet of configureer de A-records voor met het privé-eindpunt-IP-adres.

> [!TIP]
> Wanneer u een aangepaste of on-premises DNS-server gebruikt, moet u `privatelink` uw DNS-server configureren om de winkelnaam in het subdomein op te lossen naar het privé-eindpunt-IP-adres. U dit doen door `privatelink` het subdomein te delegeren naar de privé-DNS-zone van de VNet, of door de DNS-zone op uw DNS-server te configureren en de DNS A-records toe te voegen.

## <a name="pricing"></a>Prijzen

Voor het inschakelen van privéeindpunten is een App-configuratiearchief [standaardlaag](https://azure.microsoft.com/pricing/details/app-configuration/) vereist.  Zie [Azure Private Link-prijzen](https://azure.microsoft.com/pricing/details/private-link)voor meer informatie over prijsgegevens voor privékoppelingen.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor het maken van een privéeindpunt voor uw app-configuratiearchief:

- [Een privéeindpunt maken met het Private Link Center in de Azure-portal](../private-link/create-private-endpoint-portal.md)
- [Een privéeindpunt maken met Azure CLI](../private-link/create-private-endpoint-cli.md)
- [Een privéeindpunt maken met Azure PowerShell](../private-link/create-private-endpoint-powershell.md)

Meer informatie over het configureren van uw DNS-server met privéeindpunten:

- [Naamomzetting voor resources in virtuele Azure-netwerken](/azure/virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances#name-resolution-that-uses-your-own-dns-server)
- [DNS-configuratie voor privéeindpunten](/azure/private-link/private-endpoint-overview#dns-configuration)
