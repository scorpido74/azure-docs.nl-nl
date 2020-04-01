---
title: Overzicht van IPv6 voor Het Virtuele Netwerk van Azure
titlesuffix: Azure Virtual Network
description: IPv6-beschrijving van IPv6-eindpunten en gegevenspaden in een virtueel Azure-netwerk.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.workload: infrastructure-services
ms.date: 03/31/2020
ms.author: kumud
ms.openlocfilehash: 03b279ead6b1f5d26ae92b63a8780a61dfd711bb
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80420573"
---
# <a name="what-is-ipv6-for-azure-virtual-network"></a>Wat is IPv6 voor Azure Virtual Network?

Met IPv6 voor Azure Virtual Network (VNet) u toepassingen in Azure hosten met IPv6- en IPv4-connectiviteit, zowel binnen een virtueel netwerk als van en naar internet. Door de uitputting van openbare IPv4-adressen worden nieuwe netwerken voor mobiliteit en Internet of Things (IoT) vaak gebouwd op IPv6. Zelfs lang gevestigde ISP en mobiele netwerken worden omgezet in IPv6. IPv4-only diensten kunnen zich in zowel bestaande als opkomende markten in een echt nadeel bevinden. Dual stack IPv4/IPv6-connectiviteit stelt azure-gehoste services in staat om deze technologiekloof te overbruggen met wereldwijd beschikbare, dual-stackservices die gemakkelijk verbinding maken met zowel de bestaande IPv4- als deze nieuwe IPv6-apparaten en -netwerken.

Dankzij de originele IPv6-connectiviteit van Azure is het eenvoudig om iPv4/IPv6-internetverbinding (Dual Stack) te bieden voor toepassingen die in Azure worden gehost. Het zorgt voor eenvoudige implementatie van VM's met load balanced IPv6-connectiviteit voor zowel inkomende als uitgaande gestarte verbindingen. Deze functie is nog steeds beschikbaar en meer informatie is [hier](../load-balancer/load-balancer-ipv6-overview.md)beschikbaar.
IPv6 voor het virtuele netwerk van Azure is veel vollediger: volledige IPv6-oplossingsarchitecturen kunnen worden geïmplementeerd in Azure.


In het volgende diagram wordt een eenvoudige implementatie van De Dubbele Stapel (IPv4/IPv6) in Azure weergegeven:

![IPv6-netwerkimplementatiediagram](./media/ipv6-support-overview/ipv6-sample-diagram.png)

## <a name="benefits"></a>Voordelen

IPv6 voor Azure VNET-voordelen:

- Helpt het bereik van uw azure-gehoste toepassingen uit te breiden naar de groeiende markten voor mobiele en internet of things.Helps expand the reach of your Azure-hosted applications into the growing mobile and Internet of Things markets.
- Dual stacked IPv4/IPv6 VM's bieden maximale flexibiliteit voor serviceimplementatie. Eén service-exemplaar kan verbinding maken met zowel IPv4- als IPv6-geschikte internetclients.
- Bouwt voort op reeds lang bestaande, stabiele Azure VM-to-Internet IPv6-connectiviteit.
- Standaard veilig, omdat IPv6-connectiviteit met internet alleen wordt vastgesteld wanneer u dit expliciet aanvraagt bij uw implementatie.

## <a name="capabilities"></a>Functionaliteit

IPv6 voor Azure VNet bevat de volgende mogelijkheden:

- Azure-klanten kunnen hun eigen IPv6-locatie voor virtuele netwerkadres definiëren om te voldoen aan de behoeften van hun toepassingen, klanten of naadloos te integreren in hun on-premises IP-ruimte.
- Dual stack (IPv4 en IPv6) virtuele netwerken met dual stack subnets stellen toepassingen in staat om verbinding te maken met zowel IPv4- als IPv6-bronnen in hun virtuele netwerk of - het internet.
    > [!IMPORTANT]
    > De subnetten voor IPv6 moeten precies /64 groot zijn.  Dit zorgt voor toekomstige compatibiliteit als u besluit om routering van het subnet naar een on-premises netwerk in te schakelen, omdat sommige routers alleen /64 IPv6-routes kunnen accepteren.  
- Bescherm uw resources met IPv6-regels voor netwerkbeveiligingsgroepen.
    - En de DDoS-beveiligingen (Distributed Denial of Service) van het Azure-platform worden uitgebreid naar openbare IP-beveiligingen met internet
- Pas de routering van IPv6-verkeer in uw virtuele netwerk aan met door de gebruiker gedefinieerde routes, vooral wanneer u gebruikmaakt van virtuele netwerkapparaten om uw toepassing uit te breiden.
- Virtuele Machines van Linux en Windows kunnen iPv6 allemaal gebruiken voor Azure VNET
- [Standaard IPv6 public Load Balancer-ondersteuning](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md) voor het maken van robuuste, schaalbare toepassingen, waaronder:
    - Optionele IPv6-statussonde om te bepalen welke backendpool-exemplaren status zijn en dus nieuwe stromen kunnen ontvangen.
    - Optionele uitgaande regels die volledige declaratieve controle bieden over uitgaande connectiviteit om deze mogelijkheid te schalen en af te stemmen op uw specifieke behoeften.
    - Optionele meerdere front-end configuraties waarmee een enkele load balancer meerdere IPv6 publieke IP-adressen kan gebruiken- hetzelfde frontend protocol en dezelfde poort kunnen worden hergebruikt op frontend adressen.
    - Optionele IPv6-poorten kunnen opnieuw worden gebruikt op backend-exemplaren met de *Floating IP-functie* van load-balancing-regels 
- [Standaard IPv6 interne Load Balancer-ondersteuning](ipv6-dual-stack-standard-internal-load-balancer-powershell.md) voor het maken van robuuste multi-tier toepassingen binnen Azure VNETs.  
- Basisondersteuning voor IPv6 public Load Balancer voor compatibiliteit met oudere implementaties
- [Gereserveerde Ip-adressen en adresbereiken van IPv6](ipv6-public-ip-address-prefix.md) bieden stabiele, voorspelbare IPv6-adressen die het whitelisten van uw azure-gehoste applicaties voor uw bedrijf en uw klanten vergemakkelijken.
- Openbaar IP op instantieniveau biedt IPv6-internetverbinding rechtstreeks naar afzonderlijke VM's.
- [IPv6 toevoegen aan bestaande IPv4-implementaties](ipv6-add-to-existing-vnet-powershell.md)- met deze functie u eenvoudig IPv6-connectiviteit toevoegen aan bestaande IPv4-implementaties zonder implementaties opnieuw te hoeven maken.  Het IPv4-netwerkverkeer wordt tijdens dit proces niet beïnvloed, dus afhankelijk van uw toepassing en besturingssysteem u mogelijk IPv6 zelfs toevoegen aan live services.    
- Laat internetclients naadloos toegang krijgen tot uw dual stack-toepassing met behulp van hun protocol naar keuze met Azure DNS-ondersteuning voor IPv6-records (AAAA). 
- Maak dual stack-toepassingen die automatisch schalen naar uw lading met virtuele machineschaalsets met IPv6.
- [Virtual Network (VNET) Peering](virtual-network-peering-overview.md) - zowel binnen-regionale als wereldwijde peering - stelt u in staat om seemlessly dual stack VNETs aan te sluiten- zowel de IPv4- als IPv6-eindpunten op VM's in de peered-netwerken kunnen met elkaar communiceren. U zelfs dual stack met IPv4-only VNETs peeren terwijl u uw implementaties overzet naar dual stack. 
- IPv6 Troubleshooting and Diagnostics zijn beschikbaar met load balancer metrics/alerting en Network Watcher functies zoals packet capture, NSG flow logs, connection troubleshooting en connection monitoring.   

## <a name="scope"></a>Bereik
IPv6 voor Azure VNET is een fundamentele functieset waarmee klanten dual stack-toepassingen (IPv4+IPv6) in Azure kunnen hosten.  We zijn van plan om IPv6-ondersteuning toe te voegen aan meer Azure-netwerkfuncties in de loop van de tijd en uiteindelijk dual stack-versies van Azure PaaS-services aan te bieden, maar in de tussentijd kunnen alle Azure PaaS-services worden geopend via de IPv4-eindpunten op virtuele machines met twee gestapelde services.   

## <a name="limitations"></a>Beperkingen
De huidige IPv6 voor Azure virtual network release heeft de volgende beperkingen:
- IPv6 voor azure virtueel netwerk is beschikbaar in alle wereldwijde Azure-regio's, maar alleen in Global Azure- nog niet in overheidsclouds.
- ExpressRoute- en VPN-gateways kunnen niet worden gebruikt in een VNET met IPv6 ingeschakeld, direct of peered met "UseRemoteGateway". 
- Het Azure-platform (AKS, enz.) biedt geen ondersteuning voor IPv6-communicatie voor containers.  

## <a name="pricing"></a>Prijzen

IPv6 Azure-resources en -bandbreedte worden in rekening gebracht tegen dezelfde tarieven als IPv4. Er zijn geen extra of andere kosten voor IPv6. U vindt hier meer informatie over de prijzen voor [openbare IP-adressen,](https://azure.microsoft.com/pricing/details/ip-addresses/) [netwerkbandbreedte](https://azure.microsoft.com/pricing/details/bandwidth/)of [Load Balancer.](https://azure.microsoft.com/pricing/details/load-balancer/)

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [implementeren van een IPv6 dual stack-toepassing met Azure PowerShell](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-powershell.md).
- Meer informatie over het [implementeren van een IPv6 dual stack-toepassing met Azure CLI](virtual-network-ipv4-ipv6-dual-stack-standard-load-balancer-cli.md).
- Meer informatie over het [implementeren van een IPv6 dual stack-toepassing met Behulp van Resource Manager Templates (JSON)](ipv6-configure-standard-load-balancer-template-json.md)
