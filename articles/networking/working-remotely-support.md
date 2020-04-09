---
title: Op afstand werken met Azure-netwerkservices
description: Op deze pagina wordt beschreven hoe u Azure-netwerkservices gebruiken die beschikbaar zijn om werken op afstand in te schakelen en hoe u verkeersproblemen beperken als gevolg van een toename van het aantal mensen dat op afstand werkt.
services: networking
author: rambk
ms.service: virtual-network
ms.topic: article
ms.date: 03/26/2020
ms.author: rambala
ms.openlocfilehash: e0e5806ec59cd2d75111db213d8511488d043eec
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/09/2020
ms.locfileid: "80982851"
---
# <a name="working-remotely-using-azure-networking-services"></a>Op afstand werken met Azure-netwerkservices

>[!NOTE]
> In dit artikel wordt beschreven hoe u Azure-netwerkservices, microsoft-netwerk en het Azure-partnerecosysteem gebruiken om op afstand te werken en netwerkproblemen te beperken waarmee u mogelijk te maken zou kunnen hebben als gevolg van de COVID-19-crisis.

In dit artikel worden de opties beschreven die beschikbaar zijn voor organisaties om externe toegang voor hun gebruikers in te stellen of om hun bestaande oplossingen aan te vullen met extra capaciteit tijdens perioden van piekgebruik. Netwerkarchitecten staan voor de volgende uitdagingen:

- Een toename van het netwerkgebruik aanpakken.
- Zorg voor betrouwbare connectiviteit voor meer medewerkers van hun bedrijf en klanten.
- Bied connectiviteit met externe locaties over de hele wereld.

Niet alle netwerken (bijvoorbeeld particuliere WAN- en bedrijfskernnetwerken) ondervinden congestie van piekbelasting van externe werknemers. De knelpunten worden vaak alleen gemeld in breedbandnetwerken thuis en VPN-gateways van on-premises netwerken van bedrijven.

Netwerkplanners kunnen helpen de knelpunten te verlichten en de netwerkcongestie te verlichten door in gedachten te houden dat verschillende verkeerstypen verschillende netwerkbehandelingsprioriteiten en door een slimme taakomleiding/distributie nodig hebben. Bijvoorbeeld, real-time tele-medecine verkeer van arts-patiënt interactie is van groot belang en vertraging / jitter gevoelig. Terwijl replicatie van hetzelfde verkeer tussen opslagen niet vertraginggevoelig is. Het voormalige verkeer moet worden omgeleid via het meest optimale netwerkpad met een hogere kwaliteit van de dienstverlening; terwijl het aanvaardbaar is om het latere verkeer via een suboptimale route te leiden.



## <a name="sharing-our-best-practices---azure-network-is-designed-for-elasticity-and-high-availability"></a>Onze best practices delen - Azure-netwerk is ontworpen voor elasticiteit en hoge beschikbaarheid

Azure is ontworpen om plotselinge veranderingen in het gebruik van de resources te weerstaan en kan enorm helpen tijdens perioden van piekgebruik. Microsoft onderhoudt en exploiteert ook een van 's werelds grootste netwerken. Het netwerk van Microsoft is ontworpen voor hoge beschikbaarheid die bestand is tegen verschillende soorten storingen: van één netwerkelement dat niet in een hele regio valt.

Het Microsoft-netwerk is ontworpen om aan de vereisten te voldoen en optimale prestaties te bieden voor verschillende soorten netwerkverkeer, waaronder vertragingsgevoelig multimediaverkeer voor Skype en Teams, CDN, realtime big data-analyse, Azure-opslag, Bing en Xbox. Om optimale prestaties te bieden voor verschillende soorten verkeer, trekt het Microsoft-netwerk al het verkeer aan dat bestemd is om zijn resources zo dicht mogelijk bij de oorsprong van het verkeer te willen doorsturen of door te willen reizen.

>[!NOTE] 
>Met behulp van de Azure-netwerkfuncties die hieronder worden beschreven, wordt gebruikgemaakt van het gedrag van de verkeersaantrekkelijkheid van het wereldwijde Microsoft-netwerk om een betere netwerkervaring voor klanten te bieden. Het gedrag van de verkeersaantrekkelijkheid van het Netwerk van Microsoft helpt weg ladend verkeer zo spoedig mogelijk van de eerste/laatste mijlnetwerken die congestie tijdens periodes van piekgebruik kunnen ervaren.
>

## <a name="enable-employees-to-work-remotely"></a>Werknemers in staat stellen op afstand te werken

Azure VPN-gateway ondersteunt zowel Point-to-Site (P2S) als Site-to-Site (S2S) VPN-verbindingen. Met de Azure VPN-gateway u de verbindingen van uw werknemer schalen om veilig toegang te krijgen tot zowel uw azure geïmplementeerde resources als uw on-premises resources. Zie [Gebruikers in staat stellen op afstand te werken voor](../vpn-gateway/work-remotely-support.md)meer informatie. 

Als u Secure Sockets Tunneling Protocol (SSTP) gebruikt, is het aantal gelijktijdige verbindingen beperkt tot 128. Om een hoger aantal verbindingen te krijgen, raden we u aan over te stappen op OpenVPN of IKEv2. Zie [Overgang naar OpenVPN-protocol of IKEv2 van SSTP voor](../vpn-gateway/ikev2-openvpn-from-sstp.md
)meer informatie.

Om toegang te krijgen tot uw resources die in Azure zijn geïmplementeerd, kunnen externe ontwikkelaars azure bastion-oplossing gebruiken, in plaats van een VPN-verbinding om beveiligde shell-toegang (RDP of SSH) te krijgen zonder dat openbare IP's op de VM's worden geopend. Zie [Op afstand werken met Azure Bastion voor](../bastion/work-remotely-support.md)meer informatie.

Voor het aggregeren van grootschalige VPN-verbinding, om alle verbindingen tussen bronnen in verschillende on-premises wereldwijde locaties te ondersteunen, in verschillende regionale hub's en gesproken virtuele netwerken, en om het gebruik van meerdere breedbandnetwerken thuis te optimaliseren, u Azure Virtual WAN gebruiken. Voor meer informatie, zie [Worstelen om tegemoet te komen aan het werk vanuit huis behoeften? Hier is waar Azure Virtual WAN kan helpen.](../virtual-wan/work-remotely-support.md)

Een andere manier om een extern personeelsbestand te ondersteunen, is door een VDI (Virtual Desktop Infrastructure) te implementeren die wordt gehost in uw virtuele Azure-netwerk, beveiligd met een Azure Firewall. Windows Virtual Desktop (WVD) is bijvoorbeeld een virtualisatieservice voor bureaublad en app die wordt uitgevoerd in Azure. Met Windows Virtual Desktop u een schaalbare en flexibele omgeving instellen in uw Azure-abonnement zonder dat u extra gatewayservers hoeft uit te voeren. U bent alleen verantwoordelijk voor de Virtuele WVD-machines in uw virtuele netwerk. Zie [Ondersteuning voor extern werken voor Azure Firewall](../firewall/remote-work-support.md)voor meer informatie . 

Azure heeft ook een rijke set eco-systeempartners. Onze partners Network Virtual Appliances op Azure kunnen ook helpen bij het schalen van VPN-connectiviteit. Zie [NVA-overwegingen (Network Virtual Appliance) voor werkzaamheden op afstand voor](../vpn-gateway/nva-work-remotely-support.md)meer informatie.

## <a name="extend-employees-connection-to-access-globally-distributed-resources"></a>De verbinding van werknemers uitbreiden tot toegang tot wereldwijd gedistribueerde bronnen

Met de volgende Azure-services kunnen werknemers toegang krijgen tot uw wereldwijd gedistribueerde bronnen. Uw resources kunnen zich in een van de Azure-regio's, on-premises netwerken of zelfs in andere openbare of privéclouds bevinden. 

- **Azure virtual network peering:** Als u uw resources implementeert in meer dan één Azure-regio en/of als u de connectiviteit van op afstand werkende werknemers samenvoegt met meerdere virtuele netwerken, u verbinding maken tussen de meerdere Virtuele Azure-netwerken met behulp van virtuele netwerkpeering. Zie [Virtueel netwerkpeeren][VNet-peer]voor meer informatie.

- **Azure VPN-gebaseerde oplossing:** Voor uw externe werknemers die via P2S of S2S VPN met Azure zijn verbonden, u toegang tot on-premises netwerken inschakelen door S2S VPN te configureren tussen uw on-premises netwerken en Azure VPN-gateway. Zie [Een site-naar-site-verbinding maken][S2S]voor meer informatie .

- **ExpressRoute:** met expressroute privé-peering u privéconnectiviteit tussen uw Azure-implementaties en on-premises infrastructuur of uw infrastructuur inschakelen in een colocatiefaciliteit. ExpressRoute, via Microsoft peering, maakt ook toegang tot openbare eindpunten in Microsoft vanuit uw on-premises netwerk. ExpressRoute-verbindingen gaan niet via het openbare internet. Ze bieden veilige connectiviteit, betrouwbaarheid, hogere doorvoer, met lagere en consistente latencies dan typische verbindingen via het internet. Raadpleeg [Overzicht van ExpressRoute][ExR] voor meer informatie. Door gebruik te maken van uw bestaande netwerkprovider die al deel uitmaakt van ons [ExpressRoute-partnerecosysteem,][ExR-eco] u de tijd verkorten om grote bandbreedteverbindingen met Microsoft te krijgen.  Met [ExpressRoute Direct][ExR-D] u uw on-premises netwerk rechtstreeks verbinden met de Microsoft-backbone. ExpressRoute Direct biedt twee verschillende line-rate opties van dual 10 Gbps of 100 Gbps. 

- **Azure Virtual WAN**: Azure Virtual WAN maakt naadloze interoperabiliteit tussen uw VPN-verbindingen en ExpressRoute-circuits mogelijk. Zoals eerder vermeld, ondersteunt Azure Virtual WAN ook alle verbindingen tussen resources in verschillende on-prem globale locaties, in verschillende regionale hub's en sprak virtuele netwerken

## <a name="scale-customer-connectivity-to-frontend-resources"></a>Klantconnectiviteit schalen naar frontend-resources

In tijden waarin meer mensen online gaan, ervaren veel zakelijke websites het aantal gebruikersverkeer. Azure Application Gateway kan helpen bij het beheren van deze verhoogde frontend-werkbelasting. Zie [Application Gateway met ondersteuning voor veel verkeer](../application-gateway/high-traffic-support.md)voor meer informatie.

## <a name="microsoft-support-for-multi-cloud-traffic"></a>Microsoft-ondersteuning voor multi-cloudverkeer

Voor uw implementaties in andere openbare clouds kan Microsoft wereldwijde connectiviteit bieden. Azure Virtual WAN, VPN of ExpressRoute kan hierbij helpen. Als u de connectiviteit wilt uitbreiden van Azure naar andere clouds, u S2S VPN tussen de twee clouds configureren. U ook connectiviteit van Azure naar andere openbare clouds tot stand brengen met ExpressRoute. Oracle cloud maakt deel uit van het ExpressRoute partnerecosysteem. U [een directe koppeling tussen Azure en Oracle Cloud Infrastructure instellen.][Az-OCI] De meeste serviceproviders die deel uitmaken van het ExpressRoute-partnerecosysteem bieden ook privéconnectiviteit met andere openbare clouds. Door gebruik te maken van deze serviceproviders, u privéconnectiviteit tot stand brengen tussen uw implementaties in Azure en andere clouds via ExpressRoute.

## <a name="next-steps"></a>Volgende stappen

In de volgende artikelen wordt besproken hoe verschillende Azure-netwerkfuncties kunnen worden gebruikt om gebruikers te schalen om op afstand te werken:

| **Artikel** | **Beschrijving** |
| --- | --- |
| [Gebruikers in staat stellen op afstand te werken](../vpn-gateway/work-remotely-support.md) | Bekijk de beschikbare opties voor het instellen van externe toegang voor gebruikers of om hun bestaande oplossingen aan te vullen met extra capaciteit voor uw organisatie.|
| [Moeite om tegemoet te komen aan het werk vanuit huis behoeften? Hier is waar Azure Virtual WAN kan helpen](../virtual-wan/work-remotely-support.md) | Gebruik Azure Virtual WAN om tegemoet te komen aan de externe connectiviteitsbehoeften van uw organisatie.|
| [Ondersteuning voor intensief verkeer in Application Gateway](../application-gateway/high-traffic-support.md) | Gebruik Application Gateway with Web Application Firewall (WAF) voor een schaalbare en veilige manier om verkeer naar uw webtoepassingen te beheren. |
| [Network Virtual Appliance (NVA) overwegingen voor werk op afstand](../vpn-gateway/nva-work-remotely-support.md)|Bekijk de richtlijnen voor het gebruik van NVA's in Azure om oplossingen voor externe toegang te bieden. |
| [Overgang naar OpenVPN-protocol of IKEv2 van SSTP](https://go.microsoft.com/fwlink/?linkid=2124112) | Overwin de 128 gelijktijdige verbindingslimiet van SSTP door over te stappen op OpenVPN-protocol of IKEv2.|
| [Op afstand werken met Azure Bastion](../bastion/work-remotely-support.md) | Bied veilige en naadloze RDP/SSH-connectiviteit met virtuele machines binnen het virtuele Azure-netwerk, rechtstreeks in de Azure-portal, zonder het gebruik van een openbaar IP-adres. |
| [Azure ExpressRoute gebruiken om hybride connectiviteit te maken om externe gebruikers te ondersteunen](../expressroute/work-remotely-support.md) | Gebruik ExpressRoute voor hybride connectiviteit om gebruikers in uw organisatie in staat te stellen op afstand te werken.|
| [Ondersteuning voor extern werken op Azure Firewall](../firewall/remote-work-support.md)|Bescherm uw virtuele Azure-netwerkbronnen met Azure Firewall. |

<!--Link References-->
[VNet-peer]: https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview
[S2S]: https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal
[ExR]: https://docs.microsoft.com/azure/expressroute/expressroute-introduction
[ExR-eco]: https://docs.microsoft.com/azure/expressroute/expressroute-locations
[ExR-D]: https://docs.microsoft.com/azure/expressroute/expressroute-erdirect-about
[Az-OCI]: https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-azure-oci-networking
