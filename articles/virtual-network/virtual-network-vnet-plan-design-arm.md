---
title: Azure-virtuele netwerken plannen | Microsoft Documenten
description: Leer hoe u virtuele netwerken plant op basis van de vereisten met betrekking tot isolatie, connectiviteit en locatie.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 3a4a9aea-7608-4d2e-bb3c-40de2e537200
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/16/2018
ms.author: kumud
ms.openlocfilehash: 6e066d28afc4b0959b15284378cde682fbc05615
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77190467"
---
# <a name="plan-virtual-networks"></a>Virtuele netwerken plannen

Het maken van een virtueel netwerk om mee te experimenteren is eenvoudig genoeg, maar de kans is groot dat u in de loop van de tijd meerdere virtuele netwerken implementeert om de productiebehoeften van uw organisatie te ondersteunen. Met enige planning u virtuele netwerken implementeren en de resources die u nodig hebt effectiever verbinden. De informatie in dit artikel is zeer nuttig als u al bekend bent met virtuele netwerken en enige ervaring hebt met het werken met hen. Als u niet bekend bent met virtuele netwerken, is het raadzaam dat u [virtueel netwerkoverzicht](virtual-networks-overview.md)leest.

## <a name="naming"></a>Naamgeving

Alle Azure-bronnen hebben een naam. De naam moet uniek zijn binnen een bereik, dat kan variëren voor elk resourcetype. De naam van een virtueel netwerk moet bijvoorbeeld uniek zijn binnen een [resourcegroep,](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#resource-group)maar kan worden gedupliceerd binnen een [abonnement](../azure-glossary-cloud-terminology.md?toc=%2fazure%2fvirtual-network%2ftoc.json#subscription) of [Azure-gebied.](https://azure.microsoft.com/regions/#services) Het definiëren van een naamgevingsconventie die u consistent gebruiken bij het benoemen van resources is handig bij het beheren van verschillende netwerkbronnen in de loop van de tijd. Zie [Naamgevingsconventies voor](../azure-resource-manager/management/resource-name-rules.md#microsoftnetwork)suggesties.

## <a name="regions"></a>Regio's

Alle Azure-resources worden gemaakt in een Azure-regio en -abonnement. Een resource kan alleen worden gemaakt in een virtueel netwerk dat in dezelfde regio bestaat en een abonnement afsluit als de resource. U echter wel virtuele netwerken verbinden die in verschillende abonnementen en regio's bestaan. Zie [connectiviteit](#connectivity)voor meer informatie. Wanneer u beslist in welke regio(s) resources moeten worden ingezet, moet u overwegen waar de consumenten van de resources zich fysiek bevinden:

- Consumenten van resources willen doorgaans de laagste netwerklatentie voor hun resources. Zie [Relatieve latencies weergeven](../network-watcher/view-relative-latencies.md?toc=%2fazure%2fvirtual-network%2ftoc.json)als u relatieve latencies wilt bepalen tussen een opgegeven locatie en Azure-gebieden.
- Heeft u vereisten voor gegevensresidentie, soevereiniteit, naleving of tolerantie? Als dat het zo is, is het van cruciaal belang om de regio te kiezen die aansluit bij de vereisten. Zie [Azure-regio's](https://azure.microsoft.com/global-infrastructure/geographies/)voor meer informatie .
- Heeft u tolerantie nodig voor azure-beschikbaarheidszones binnen dezelfde Azure-regio voor de resources die u implementeert? U resources, zoals virtuele machines (VM) implementeren in verschillende beschikbaarheidszones binnen hetzelfde virtuele netwerk. Niet alle Azure-regio's ondersteunen echter beschikbaarheidszones. Zie [Beschikbaarheidszones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over beschikbaarheidszones en de regio's die deze ondersteunen.

## <a name="subscriptions"></a>Abonnementen

U zoveel virtuele netwerken implementeren als vereist binnen elk abonnement, tot het [uiterste.](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) Sommige organisaties hebben bijvoorbeeld verschillende abonnementen voor verschillende afdelingen. Zie [Abonnementsbeheer](/azure/cloud-adoption-framework/reference/migration-with-enterprise-scaffold#define-your-hierarchy)voor meer informatie en overwegingen rond abonnementen .

## <a name="segmentation"></a>Segmentatie

U meerdere virtuele netwerken maken per abonnement en per regio. U meerdere subnetten maken binnen elk virtueel netwerk. De overwegingen die volgen helpen u bij het bepalen hoeveel virtuele netwerken en subnetten u nodig hebt:

### <a name="virtual-networks"></a>Virtuele netwerken

Een virtueel netwerk is een virtueel, geïsoleerd gedeelte van het openbare Azure-netwerk. Elk virtueel netwerk is gewijd aan uw abonnement. Dingen om rekening mee te houden bij het kiezen of je één virtueel netwerk wilt maken, of meerdere virtuele netwerken in een abonnement:

- Bestaan er organisatorische beveiligingsvereisten voor het isoleren van verkeer naar afzonderlijke virtuele netwerken? U ervoor kiezen om virtuele netwerken te verbinden of niet. Als u virtuele netwerken aansluit, u een virtueel netwerktoestel implementeren, zoals een firewall, om de verkeersstroom tussen de virtuele netwerken te regelen. Zie [beveiliging](#security) en [connectiviteit](#connectivity)voor meer informatie.
- Bestaan er organisatorische vereisten voor het isoleren van virtuele netwerken in afzonderlijke [abonnementen](#subscriptions) of [regio's?](#regions)
- Met een [netwerkinterface](virtual-network-network-interface.md) kan een VM communiceren met andere bronnen. Aan elke netwerkinterface zijn een of meer privé-IP-adressen toegewezen. Hoeveel netwerkinterfaces en [privé IP-adressen](virtual-network-ip-addresses-overview-arm.md#private-ip-addresses) hebt u nodig in een virtueel netwerk? Er zijn [grenzen](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) aan het aantal netwerkinterfaces en privé-IP-adressen dat u binnen een virtueel netwerk hebben.
- Wilt u het virtuele netwerk verbinden met een ander virtueel netwerk of on-premises netwerk? U ervoor kiezen om bepaalde virtuele netwerken met elkaar of on-premises netwerken te verbinden, maar niet met andere. Zie [connectiviteit](#connectivity)voor meer informatie. Elk virtueel netwerk dat u verbinding maakt met een ander virtueel netwerk of on-premises netwerk, moet een unieke adresruimte hebben. Elk virtueel netwerk heeft een of meer openbare of private adresbereiken toegewezen aan de adresruimte. Een adresbereik wordt opgegeven in de CIDR-indeling (Classless Internet Domain Routing), zoals 10.0.0.0/16. Meer informatie over [adresbereiken](manage-virtual-network.md#add-or-remove-an-address-range) voor virtuele netwerken.
- Heeft u organisatorische administratieve vereisten voor resources in verschillende virtuele netwerken? Als dat het zo is, u resources scheiden in een afzonderlijk virtueel netwerk om [de toewijzing van machtigingen](#permissions) aan personen in uw organisatie te vereenvoudigen of om verschillende beleidsregels toe te wijzen aan verschillende virtuele netwerken.
- Wanneer u sommige Azure-serviceresources implementeert in een virtueel netwerk, maken ze hun eigen virtuele netwerk. Zie informatie voor elke [Azure-service die kan worden geïmplementeerd in een virtueel netwerk](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)om te bepalen of een Azure-service een eigen virtueel netwerk maakt.

### <a name="subnets"></a>Subnetten

Een virtueel netwerk kan tot aan de [limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits)worden gesegmenteerd in een of meer subnetten. Dingen om rekening mee te houden bij het kiezen of u één subnet wilt maken of meerdere virtuele netwerken in een abonnement:

- Elk subnet moet een uniek adresbereik hebben, gespecificeerd in CIDR-indeling, binnen de adresruimte van het virtuele netwerk. Het adresbereik kan niet overlappen met andere subnetten in het virtuele netwerk.
- Als u van plan bent bepaalde Azure-serviceresources in een virtueel netwerk te implementeren, vereisen of maken ze mogelijk hun eigen subnet, zodat er voldoende niet-toegewezen ruimte voor hen moet zijn om dit te doen. Zie informatie voor elke [Azure-service die kan worden geïmplementeerd in een virtueel netwerk](virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network)om te bepalen of een Azure-service een eigen subnet maakt. Als u bijvoorbeeld een virtueel netwerk verbindt met een on-premises netwerk met behulp van een Azure VPN-gateway, moet het virtuele netwerk een speciaal subnet voor de gateway hebben. Meer informatie over [gateway-subnetten](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md?toc=%2fazure%2fvirtual-network%2ftoc.json#gwsub).
- Azure leidt netwerkverkeer standaard tussen alle subnetten in een virtueel netwerk. U de standaardroutering van Azure overschrijven om Azure-routering tussen subnetten te voorkomen of om verkeer tussen subnetten te routeren via bijvoorbeeld een virtueel netwerktoestel. Als u vereist dat verkeer tussen resources in hetzelfde virtuele netwerk via een netwerk virtueel toestel (NVA) stroomt, implementeert u de resources naar verschillende subnetten. Meer informatie over [beveiliging](#security).
- U de toegang tot Azure-bronnen, zoals een Azure-opslagaccount of Azure SQL-database, beperken tot specifieke subnetten met een eindpunt voor virtuele netwerkservice. Verder u de toegang tot de bronnen van het internet weigeren. U meerdere subnetten maken en een serviceeindpunt inschakelen voor sommige subnetten, maar andere niet. Meer informatie over [serviceeindpunten](virtual-network-service-endpoints-overview.md)en de Azure-resources waarvoor u ze inschakelen.
- U nul of één netwerkbeveiligingsgroep koppelen aan elk subnet in een virtueel netwerk. U hetzelfde of een andere netwerkbeveiligingsgroep aan elk subnet koppelen. Elke netwerkbeveiligingsgroep bevat regels die verkeer van en naar bronnen en bestemmingen toestaan of weigeren. Meer informatie over [netwerkbeveiligingsgroepen](#traffic-filtering).

## <a name="security"></a>Beveiliging

U netwerkverkeer van en naar bronnen in een virtueel netwerk filteren met behulp van netwerkbeveiligingsgroepen en virtuele netwerkapparaten. U bepalen hoe Azure verkeer van subnetten routeert. U ook beperken wie in uw organisatie met resources in virtuele netwerken kan werken.

### <a name="traffic-filtering"></a>Verkeer filteren

- U netwerkverkeer filteren tussen bronnen in een virtueel netwerk met behulp van een netwerkbeveiligingsgroep, een NVA die netwerkverkeer filtert of beide. Zie de [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/networking?subcategories=appliances&page=1)om een NVA, zoals een firewall, te implementeren om netwerkverkeer te filteren. Bij het gebruik van een NVA maakt u ook aangepaste routes om verkeer van subnetten naar de NVA te leiden. Meer informatie over [verkeersroutering](#traffic-routing).
- Een netwerkbeveiligingsgroep bevat verschillende standaardbeveiligingsregels die verkeer van of naar resources toestaan of weigeren. Een netwerkbeveiligingsgroep kan worden gekoppeld aan een netwerkinterface, het subnet waarin de netwerkinterface zich bevindt of beide. Om het beheer van beveiligingsregels te vereenvoudigen, wordt aanbevolen dat u een netwerkbeveiligingsgroep zoveel mogelijk koppelt aan afzonderlijke subnetten, in plaats van afzonderlijke netwerkinterfaces binnen het subnet.
- Als verschillende VM's binnen een subnet verschillende beveiligingsregels nodig hebben die op deze regels worden toegepast, u de netwerkinterface in de VM koppelen aan een of meer toepassingsbeveiligingsgroepen. Een beveiligingsregel kan een toepassingsbeveiligingsgroep opgeven in de bron, bestemming of beide. Die regel is dan alleen van toepassing op de netwerkinterfaces die lid zijn van de toepassingsbeveiligingsgroep. Meer informatie over [netwerkbeveiligingsgroepen](security-overview.md) en [toepassingsbeveiligingsgroepen](security-overview.md#application-security-groups).
- Azure maakt verschillende standaardbeveiligingsregels binnen elke netwerkbeveiligingsgroep. Met één standaardregel kan al het verkeer tussen alle bronnen in een virtueel netwerk stromen. Als u dit gedrag wilt negeren, gebruikt u netwerkbeveiligingsgroepen, aangepaste routering om verkeer naar een NVA te leiden of beide. Het wordt aanbevolen dat u vertrouwd bent met alle [standaardbeveiligingsregels](security-overview.md#default-security-rules) van Azure en begrijpt hoe regels voor netwerkbeveiligingsgroepen worden toegepast op een bron.

U voorbeeldontwerpen voor het implementeren van een perimeternetwerk (ook wel DMZ) tussen Azure en internet bekijken met behulp van een [NVA.](/azure/architecture/reference-architectures/dmz/secure-vnet-dmz?toc=%2Fazure%2Fvirtual-network%2Ftoc.json)

### <a name="traffic-routing"></a>Verkeersroutering

Azure maakt verschillende standaardroutes voor uitgaand verkeer van een subnet. U de standaardroutering van Azure overschrijven door een routetabel te maken en deze aan een subnet te koppelen. Veelvoorkomende redenen voor het overschrijven van de standaardroutering van Azure zijn:
- Omdat u wilt dat het verkeer tussen subnetten door een NVA stroomt. Meer informatie over het [configureren van routetabellen om verkeer via een NVA te forceren.](tutorial-create-route-table-portal.md)
- Omdat u al het internetgebonden verkeer via een NVA of on-premises wilt afdwingen via een Azure VPN-gateway. Het afdwingen van internetverkeer ter plaatse voor inspectie en houtkap wordt vaak aangeduid als gedwongen tunneling. Meer informatie over het configureren [van gedwongen tunneling](../vpn-gateway/vpn-gateway-forced-tunneling-rm.md?toc=%2Fazure%2Fvirtual-network%2Ftoc.json).

Als u aangepaste routering moet implementeren, wordt aanbevolen dat u vertrouwd bent met [routering in Azure.](virtual-networks-udr-overview.md)

## <a name="connectivity"></a>Connectiviteit

U een virtueel netwerk verbinden met andere virtuele netwerken via virtuele netwerkpeering of met uw on-premises netwerk, met behulp van een Azure VPN-gateway.

### <a name="peering"></a>Peering

Bij het gebruik van [virtuele netwerkpeering](virtual-network-peering-overview.md)kunnen de virtuele netwerken zich in dezelfde of verschillende ondersteunde Azure-regio's bevinden. De virtuele netwerken kunnen zich in dezelfde of verschillende Azure-abonnementen bevinden (zelfs abonnementen die behoren tot verschillende Azure Active Directory-tenants). Voordat u een peering maakt, wordt aanbevolen dat u vertrouwd bent met alle vereisten [en beperkingen](virtual-network-manage-peering.md#requirements-and-constraints)voor peering. Bandbreedte tussen resources in virtuele netwerken die in dezelfde regio zijn peered, is hetzelfde als wanneer de resources zich in hetzelfde virtuele netwerk bevinden.

### <a name="vpn-gateway"></a>VPN-gateway

U een Azure [VPN-gateway](../vpn-gateway/vpn-gateway-vnet-vnet-rm-ps.md?toc=%2fazure%2fvirtual-network%2ftoc.json) gebruiken om een virtueel netwerk met uw on-premises netwerk te verbinden met behulp van een [site-to-site VPN](../vpn-gateway/vpn-gateway-tutorial-vpnconnection-powershell.md?toc=%2fazure%2fvirtual-network%2ftoc.json)of met behulp van een speciale verbinding met Azure [ExpressRoute.](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

U peering en een VPN-gateway combineren om [hub- en spaaknetwerken](/azure/architecture/reference-architectures/hybrid-networking/hub-spoke?toc=%2fazure%2fvirtual-network%2ftoc.json)te maken, waar gesproken virtuele netwerken verbinding maken met een virtueel hubnetwerk en de hub maakt bijvoorbeeld verbinding met een on-premises netwerk.

### <a name="name-resolution"></a>Naamomzetting

Bronnen in één virtueel netwerk kunnen de namen van resources in een peered virtueel netwerk niet oplossen met behulp van de [ingebouwde DNS](virtual-networks-name-resolution-for-vms-and-role-instances.md)van Azure. Als u namen in een gepeerd virtueel netwerk wilt oplossen, [implementeert u uw eigen DNS-server](virtual-networks-name-resolution-for-vms-and-role-instances.md#name-resolution-that-uses-your-own-dns-server)of gebruikt u Azure [DNS-privédomeinen.](../dns/private-dns-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) Voor het oplossen van namen tussen bronnen in een virtueel netwerk en on-premises netwerken moet u ook uw eigen DNS-server implementeren.

## <a name="permissions"></a>Machtigingen

Azure maakt gebruik [van rbac (role based access control)](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor resources. Machtigingen worden toegewezen aan een [bereik](../role-based-access-control/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json#scope) in de volgende hiërarchie: beheergroep, abonnement, resourcegroep en afzonderlijke resource. Zie [Uw resources ordenen](../azure-resource-manager/management-groups-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over de hiërarchie . Als u wilt werken met virtuele Azure-netwerken en al hun gerelateerde mogelijkheden, zoals peering, netwerkbeveiligingsgroepen, serviceeindpunten en routetabellen, u leden van uw organisatie toewijzen aan de ingebouwde [eigenaar,](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#owner) [inzender](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#contributor)of [netwerkbijdragen van inzenderen](../role-based-access-control/built-in-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-contributor) en de rol vervolgens toewijzen aan het juiste bereik. Als u specifieke machtigingen wilt toewijzen voor een subset van virtuele netwerkmogelijkheden, maakt u een [aangepaste rol](../role-based-access-control/custom-roles.md?toc=%2fazure%2fvirtual-network%2ftoc.json) en wijst u de specifieke machtigingen toe die nodig zijn voor [virtuele netwerken,](manage-virtual-network.md#permissions) [subnetten en serviceeindpunten,](virtual-network-manage-subnet.md#permissions) [netwerkinterfaces,](virtual-network-network-interface.md#permissions) [peering-,](virtual-network-manage-peering.md#permissions) [netwerk- en toepassingsbeveiligingsgroepen](manage-network-security-group.md#permissions)of [routetabellen](manage-route-table.md#permissions) naar de rol.

## <a name="policy"></a>Beleid

Met Azure Policy u beleidsdefinities maken, toewijzen en beheren. Beleidsdefinities dwingen verschillende regels af ten opzichte van uw resources, zodat de resources in overeenstemming blijven met uw organisatiestandaarden en serviceniveauovereenkomsten. Azure Policy voert een evaluatie van uw resources uit en scant naar bronnen die niet voldoen aan de beleidsdefinities die u hebt. U bijvoorbeeld een beleid definiëren en toepassen waarmee virtuele netwerken alleen in een specifieke resourcegroep of -regio kunnen worden gemaakt. Een ander beleid kan vereisen dat elk subnet een netwerkbeveiligingsgroep heeft die eraan is gekoppeld. Het beleid wordt vervolgens geëvalueerd bij het maken en bijwerken van resources.

Beleidsregels worden toegepast op de volgende hiërarchie: beheergroep, abonnementsgroep en resourcegroep. Meer informatie over [Azure-beleid](../governance/policy/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of implementeren van een aantal voorbeelden van virtuele [netwerkbeleidssjablonen.](policy-samples.md)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over alle taken, instellingen en opties voor een [virtueel netwerk,](manage-virtual-network.md) [subnet en serviceeindpunt,](virtual-network-manage-subnet.md) [netwerkinterface,](virtual-network-network-interface.md) [peering,](virtual-network-manage-peering.md) [netwerk- en toepassingsbeveiligingsgroep](manage-network-security-group.md)of [routetabel](manage-route-table.md).
