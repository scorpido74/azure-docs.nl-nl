---
title: Azure Virtual Network | Microsoft Docs
description: Meer informatie over concepten en functies van Azure Virtual Network.
services: virtual-network
documentationcenter: na
author: anavinahar
tags: azure-resource-manager
Customer intent: As someone with a basic network background that is new to Azure, I want to understand the capabilities of Azure Virtual Network, so that my Azure resources such as VMs, can securely communicate with each other, the internet, and my on-premises resources.
ms.service: virtual-network
ms.devlang: na
ms.topic: overview
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/19/2019
ms.author: anavin
ms.openlocfilehash: 967d391d4ac9a9704688dce9636d9a71b2002549
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2020
ms.locfileid: "80879357"
---
# <a name="what-is-azure-virtual-network"></a>Wat is Azure Virtual Network?

Azure Virtual Network (VNet) is de fundamentele bouwsteen voor uw privénetwerk in Azure. Met VNet kunnen veel typen Azure-resources, zoals Virtual Azure Machines (VM), veilig met elkaar, internet en on-premises netwerken communiceren. VNet is vergelijkbaar met een traditioneel netwerk dat u in uw eigen datacenter zou beheren, maar brengt extra voordelen met zich mee van de infrastructuur van Azure, zoals schaal, beschikbaarheid en isolatie.

## <a name="vnet-concepts"></a>VNet-concepten

- **Adresruimte:** Bij het maken van een VNet moet u een aangepaste privé-IP-adresruimte opgeven met behulp van openbare en private (RFC 1918) adressen. Azure wijst resources in een virtueel netwerk een persoonlijk IP-adres toe op basis van de adresruimte die u toewijst. Als u bijvoorbeeld een VM implementeert in een VNet met adresruimte, 10.0.0.0/16, krijgt de VM een privé-IP toegewezen zoals 10.0.0.4.
- **Subnetten:** Subnetten stellen u in staat om het virtuele netwerk te segmenteren in een of meer subnetwerken en een deel van de adresruimte van het virtuele netwerk aan elk subnet toe te wijzen. U azure-resources vervolgens implementeren in een specifiek subnet. Net als in een traditioneel netwerk u met subnetten uw VNet-adresruimte segmenteren in segmenten die geschikt zijn voor het interne netwerk van de organisatie. Dit verbetert ook de efficiëntie van de adrestoewijzing. U bronnen binnen subnetten beveiligen met netwerkbeveiligingsgroepen. Zie [Beveiligingsgroepen voor](security-overview.md)meer informatie.
- **Regio's**: VNet is scoped to a single region/location; Meerdere virtuele netwerken uit verschillende regio's kunnen echter met elkaar worden verbonden met behulp van Virtual Network Peering.
- **Abonnement:** VNet heeft een abonnement. U kunt meerdere virtuele netwerken binnen elk Azure-[abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) en elke Azure-[regio](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#region) implementeren.

## <a name="best-practices"></a>Aanbevolen procedures

Wanneer u uw netwerk in Azure bouwt, is het belangrijk om rekening te houden met de volgende universele ontwerpprincipes:

- Zorg voor niet-overlappende adresruimten. Zorg ervoor dat uw VNet-adresruimte (CIDR-blok) niet overlapt met de andere netwerkbereiken van uw organisatie.
- Uw subnetten mogen niet de volledige adresruimte van de VNet bestrijken. Plan vooruit en reserveer wat adresruimte voor de toekomst.
- Het wordt aanbevolen dat u minder grote VNets hebt dan meerdere kleine VNets. Dit voorkomt het beheer overhead.
- Beveilig uw VNet's door Netwerkbeveiligingsgroepen (NSG's) toe te wijs aan de subnetten eronder.

## <a name="communicate-with-the-internet"></a>Communiceren met internet

Alle bronnen in een VNet kunnen standaard uitgaand communiceren naar het internet. U kunt binnenkomend communiceren met een resource door er een openbaar IP-adres of een openbare Load Balancer aan toe te wijzen. U kunt ook een openbaar IP-adres of een openbare Load Balancer gebruiken voor het beheren van uw uitgaande verbindingen.  Zie [Uitgaande verbindingen](../load-balancer/load-balancer-outbound-connections.md), [Openbare IP-adressen](virtual-network-public-ip-address.md) en [Load Balancer](../load-balancer/load-balancer-overview.md) voor meer informatie over uitgaande verbindingen in Azure.

>[!NOTE]
>Wanneer u alleen een interne [Standard Load Balancer](../load-balancer/load-balancer-standard-overview.md) gebruikt, is uitgaande connectiviteit niet beschikbaar totdat u definieert hoe u wilt dat [uitgaande verbindingen](../load-balancer/load-balancer-outbound-connections.md) werken met een op exemplaarniveau openbaar IP-adres of een openbare Load Balancer.

## <a name="communicate-between-azure-resources"></a>Communicatie tussen Azure-resources

Azure-resources communiceren veilig met elkaar op een van de volgende manieren:

- **Via een virtueel netwerk**: u kunt virtuele machines en diverse andere soorten Azure-resources implementeren op een virtueel netwerk, zoals Azure App Service-omgevingen, de Azure Kubernetes Service (AKS) en Azure Virtual Machine Scale Sets. Zie [Integratie van virtuele netwerkservices](virtual-network-for-azure-services.md) voor een volledige lijst met Azure-resources die u in een virtueel netwerk kunt implementeren.
- **Via een service-eindpunt voor een virtueel netwerk**: breid de openbare adresruimte van uw virtuele netwerk en de identiteit van uw virtuele netwerk uit naar Azure-serviceresources, zoals Azure Storage-accounts en Azure SQL-databases, via een directe verbinding. Met service-eindpunten kunt u uw kritieke Azure-serviceresources alleen beveiligen naar een virtueel netwerk. Zie [Overzicht van service-eindpunten voor virtuele netwerken](virtual-network-service-endpoints-overview.md) voor meer informatie.
- **Via VNet Peering**: U virtuele netwerken met elkaar verbinden, waardoor resources in een virtueel netwerk met elkaar kunnen communiceren met behulp van virtuele netwerkpeering. De virtuele netwerken die u met elkaar verbindt, kunnen zich in dezelfde of verschillende Azure-regio's bevinden. Zie [Peering van virtuele netwerken](virtual-network-peering-overview.md) voor meer informatie.

## <a name="communicate-with-on-premises-resources"></a>Communiceren met on-premises resources

U kunt uw on-premises computers en netwerken verbinden met een virtueel netwerk via een combinatie van de volgende opties:

- **Point-to-site virtueel particulier netwerk (VPN):** wordt tot stand gebracht tussen een virtueel netwerk en een enkele computer in uw netwerk. Elke computer die verbinding wil met een virtueel netwerk moet de verbinding hiervoor configureren. Dit verbindingstype is handig als u net aan de slag gaat met Azure of voor ontwikkelaars, omdat hiervoor weinig of geen wijzigingen in uw bestaande netwerk nodig zijn. De communicatie tussen uw computer en een virtueel netwerk wordt verzonden via een gecodeerde tunnel via internet. Zie [Point-to-site VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#P2S) voor meer informatie.
- **Site-to-site VPN:** wordt tot stand gebracht tussen uw on-premises VPN-apparaat en een Azure VPN Gateway die is geïmplementeerd in een virtueel netwerk. Met dit verbindingstype krijgen alle on-premises resource die u toestemming geeft toegang tot een virtueel netwerk. De communicatie tussen uw on-premises VPN-apparaat en een Azure VPN Gateway wordt verzonden via een gecodeerde tunnel via internet. Zie [Site-to-site VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) voor meer informatie.
- **Azure ExpressRoute:** wordt tot stand gebracht tussen uw netwerk en Azure, via een ExpressRoute-partner. Deze verbinding is een privéverbinding. Verkeer gaat niet via internet. Zie [ExpressRoute](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#ExpressRoute) voor meer informatie.

## <a name="filter-network-traffic"></a>Netwerkverkeer filteren

U kunt netwerkverkeer filteren tussen subnetten met behulp van een of beide van de volgende opties:

- **Beveiligingsgroepen:** Netwerkbeveiligingsgroepen en toepassingsbeveiligingsgroepen kunnen meerdere binnenkomende en uitgaande beveiligingsregels bevatten waarmee u verkeer van en naar resources filteren op bron- en bestemmings-IP-adres, -poort en-protocol. Zie [Netwerkbeveiligingsgroepen](security-overview.md#network-security-groups) of [Toepassingsbeveiligingsgroepen](security-overview.md#application-security-groups) voor meer informatie.
- **Virtuele apparaten:** een virtueel netwerkapparaat is een virtuele machine die een netwerkfunctie uitvoert, zoals een firewall, WAN-optimalisatie of een andere netwerkfunctie. Zie [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?page=1&subcategories=appliances) voor meer informatie over het bekijken van een lijst met beschikbare virtuele netwerkapparaten die u in een virtueel netwerk kunt implementeren.

## <a name="route-network-traffic"></a>Netwerkverkeer routeren

Azure routeert verkeer standaard tussen subnetten, verbonden virtuele netwerken, on-premises netwerken en internet. U kunt een of beide van de volgende opties implementeren om de standaardroutes die Azure maakt te onderdrukken:

- **Routetabellen:** u kunt aangepaste routetabellen maken met routes die bepalen waar verkeer naartoe wordt doorgestuurd voor elk subnet. Meer informatie over [routetabellen](virtual-networks-udr-overview.md#user-defined).
- **BGP-routes (Border Gateway Protocol):** als u uw virtuele netwerk verbindt met uw on-premises netwerk via een Azure VPN Gateway- of ExpressRoute-verbinding, kunt u uw lokale BGP-routes doorgegeven aan uw virtuele netwerken. Lees meer over het gebruik van BGP met [Azure VPN Gateway](../vpn-gateway/vpn-gateway-bgp-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) en [ExpressRoute](../expressroute/expressroute-routing.md?toc=%2fazure%2fvirtual-network%2ftoc.json#dynamic-route-exchange).

## <a name="virtual-network-integration-for-azure-services"></a>Integratie van virtuele netwerken voor Azure-services

Als u Azure-services integreert in een virtueel Azure-netwerk, u privétoegang tot de service maken vanaf virtuele machines of rekenbronnen in het virtuele netwerk.
U Azure-services in uw virtuele netwerk integreren met de volgende opties:
- Het implementeren van [speciale exemplaren van de service](virtual-network-for-azure-services.md) in een virtueel netwerk. De diensten zijn dan privé toegankelijk binnen het virtuele netwerk en via on-premises netwerken.
- Met Behulp van [Private Link](../private-link/private-link-overview.md) om privé toegang te krijgen tot een specifiek exemplaar van de service vanuit uw virtuele netwerk en vanaf on-premises netwerken.
- U de service ook openen via openbare eindpunten door een virtueel netwerk uit te breiden naar de service, via [serviceeindpunten.](virtual-network-service-endpoints-overview.md) Met serviceeindpunten kunnen serviceresources worden beveiligd voor het virtuele netwerk.
 

## <a name="azure-vnet-limits"></a>Azure VNet-limieten

Er zijn bepaalde limieten rond het aantal Azure-resources dat u implementeren. De meeste Azure-netwerklimieten zijn maximaal waarden. U echter [bepaalde netwerklimieten verhogen](../azure-portal/supportability/networking-quota-requests.md) zoals opgegeven op de [pagina VNet-limieten.](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits) 

## <a name="pricing"></a>Prijzen

Er zijn geen kosten verbonden aan het gebruik van Azure VNet, het is gratis. Standaardkosten zijn van toepassing op resources, zoals Virtuele Machines (VM's) en andere producten. Zie [VNet-prijzen](https://azure.microsoft.com/pricing/details/virtual-network/) en de [azure-prijscalculator](https://azure.microsoft.com/pricing/calculator/)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

 Als u aan de slag wilt gaan met een virtueel netwerk, maakt u een virtueel netwerk, implementeert u een paar VM's en laat u de virtuele machines met elkaar communiceren. Zie de snelstart [Een virtueel netwerk maken](quick-create-portal.md) voor meer informatie.
