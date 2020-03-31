---
title: Veelgestelde vragen over azure virtual network
titlesuffix: Azure Virtual Network
description: Antwoorden op de meest gestelde vragen over virtuele Microsoft Azure-netwerken.
services: virtual-network
documentationcenter: na
author: KumudD
manager: twooley
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/12/2019
ms.author: kumud
ms.openlocfilehash: e45d5393833973889b28a95ec86b89593a091f99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79244808"
---
# <a name="azure-virtual-network-frequently-asked-questions-faq"></a>Veelgestelde vragen over virtuele Azure-netwerken (FAQ)

## <a name="virtual-network-basics"></a>Basisprincipes van virtueel netwerk

### <a name="what-is-an-azure-virtual-network-vnet"></a>Wat is een Azure Virtual Network (VNet)?
Een Azure Virtual Network (VNet) is een weergave van uw eigen netwerk in de cloud. Het is een logische isolatie van de Azure-cloud die is toegewezen aan uw abonnement. U VNets gebruiken voor het inrichten en beheren van virtual private networks (VPN's) in Azure en optioneel de VNets koppelen aan andere VNets in Azure, of aan uw on-premises IT-infrastructuur om hybride of cross-premises oplossingen te maken. Elk VNet dat u maakt heeft zijn eigen CIDR-blok en kan worden gekoppeld aan andere VNets en on-premises netwerken, zolang de CIDR-blokken elkaar niet overlappen. U hebt ook controle over DNS-serverinstellingen voor VNets en segmentatie van de VNet in subnetten.

Gebruik VNets om:

* Maak een speciale private cloud-only VNet. Soms hebt u geen cross-premises configuratie nodig voor uw oplossing. Wanneer u een VNet maakt, kunnen uw services en VM's binnen uw VNet direct en veilig met elkaar communiceren in de cloud. U nog steeds eindpuntverbindingen configureren voor de VM's en services waarvoor internetcommunicatie nodig is, als onderdeel van uw oplossing.

* Breid uw datacenter veilig uit. Met VNets u traditionele site-to-site (S2S) VPN's bouwen om uw datacentercapaciteit veilig te schalen. S2S VPN's gebruiken IPSEC om een veilige verbinding te bieden tussen uw zakelijke VPN-gateway en Azure.

* Schakel hybride cloudscenario's in. VNets bieden u de flexibiliteit om een reeks hybride cloudscenario's te ondersteunen. U cloudgebaseerde toepassingen veilig verbinden met elk type on-premises systeem, zoals mainframes en Unix-systemen.

### <a name="how-do-i-get-started"></a>Hoe ga ik aan de slag?
Ga naar de [virtuele netwerkdocumentatie](https://docs.microsoft.com/azure/virtual-network/) om aan de slag te gaan. Deze inhoud biedt overzichts- en implementatiegegevens voor alle VNet-functies.

### <a name="can-i-use-vnets-without-cross-premises-connectivity"></a>Kan ik VNets gebruiken zonder cross-premises connectiviteit?
Ja. U een VNet gebruiken zonder deze aan uw pand te koppelen. U bijvoorbeeld Microsoft Windows Server Active Directory-domeincontrollers en SharePoint-farms uitsluitend in een Azure VNet uitvoeren.

### <a name="can-i-perform-wan-optimization-between-vnets-or-a-vnet-and-my-on-premises-data-center"></a>Kan ik WAN-optimalisatie uitvoeren tussen VNets of een VNet en mijn on-premises datacenter?
Ja. U een [virtueel WAN-optimalisatienetwerk](https://azuremarketplace.microsoft.com/en-us/marketplace/?term=wan%20optimization) van verschillende leveranciers implementeren via de Azure Marketplace.

## <a name="configuration"></a>Configuratie

### <a name="what-tools-do-i-use-to-create-a-vnet"></a>Welke tools gebruik ik om een VNet te maken?
U de volgende gereedschappen gebruiken om een VNet te maken of te configureren:

* Azure Portal
* PowerShell
* Azure-CLI
* Een netwerkconfiguratiebestand (netcfg - alleen voor klassieke VNets). Zie het VNet configureren met behulp van een artikel [over netwerkconfiguratiebestanden.](virtual-networks-using-network-configuration-file.md)

### <a name="what-address-ranges-can-i-use-in-my-vnets"></a>Welke adresbereiken kan ik gebruiken in mijn VNets?
Elk IP-adresbereik gedefinieerd in [RFC 1918](https://tools.ietf.org/html/rfc1918). Bijvoorbeeld 10.0.0.0/16. U de volgende adresbereiken niet toevoegen:
* 224.0.0.0/4 (Multicast)
* 255.255.255.255/32 (Uitzending)
* 127.0.0.0/8 (Loopback)
* 169.254.0.0/16 (Link-lokaal)
* 168.63.129.16/32 (Interne DNS)

### <a name="can-i-have-public-ip-addresses-in-my-vnets"></a>Kan ik openbare IP-adressen in mijn VNets hebben?
Ja. Zie [Een virtueel netwerk maken](manage-virtual-network.md#create-a-virtual-network)voor meer informatie over openbare IP-adresbereiken. Openbare IP-adressen zijn niet direct toegankelijk vanaf het internet.

### <a name="is-there-a-limit-to-the-number-of-subnets-in-my-vnet"></a>Is er een limiet aan het aantal subnetten in mijn VNet?
Ja. Zie [Azure-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits) voor meer informatie. Subnetadresruimten kunnen elkaar niet overlappen.

### <a name="are-there-any-restrictions-on-using-ip-addresses-within-these-subnets"></a>Zijn er beperkingen op het gebruik van IP-adressen binnen deze subnetten?
Ja. In Azure worden vijf IP-adressen gereserveerd in elk subnet. These are x.x.x.0-x.x.x.3 and the last address of the subnet. x.x.x.1-x.x.x.3 is reserved in each subnet for Azure services.   
- x.x.x.0: Netwerkadres
- x.x.x.1: gereserveerd door Azure voor de standaardgateway
- x.x.x.2, x.x.x.3: Gereserveerd door Azure om de Azure DNS-IP's in kaart te brengen aan de VNet-ruimte
- x.x.x.255: Netwerkuitzendadres

### <a name="how-small-and-how-large-can-vnets-and-subnets-be"></a>Hoe klein en hoe groot kunnen VNets en subnetten zijn?
Het kleinste ondersteunde IPv4-subnet is /29 en het grootste is /8 (met behulp van CIDR-subnetdefinities).  IPv6-subnetten moeten precies /64 groot zijn.  

### <a name="can-i-bring-my-vlans-to-azure-using-vnets"></a>Kan ik mijn VLAN's naar Azure brengen met VNets?
Nee. VNets zijn Layer-3 overlays. Azure biedt geen ondersteuning voor Layer-2 semantiek.

### <a name="can-i-specify-custom-routing-policies-on-my-vnets-and-subnets"></a>Kan ik aangepaste routeringsbeleid opgeven op mijn VNets en subnetten?
Ja. U een routetabel maken en deze koppelen aan een subnet. Zie [Routeringsoverzicht](virtual-networks-udr-overview.md#custom-routes)voor meer informatie over routering in Azure.

### <a name="do-vnets-support-multicast-or-broadcast"></a>Ondersteunen VNets multicast of broadcast?
Nee. Multicast en uitzending worden niet ondersteund.

### <a name="what-protocols-can-i-use-within-vnets"></a>Welke protocollen kan ik gebruiken binnen VNets?
U TCP-, UDP- en ICMP TCP/IP-protocollen gebruiken binnen VNets. Unicast wordt ondersteund binnen VNets, met uitzondering van Dynamic Host Configuration Protocol (DHCP) via Unicast (source port UDP/68 / destination port UDP/67). Multicast-, broadcast-, IP-in-IP-ingekapselde pakketten en GRE-pakketten (Generic Routing Encapsulation) worden geblokkeerd binnen VNets. 

### <a name="can-i-ping-my-default-routers-within-a-vnet"></a>Kan ik mijn standaardrouters pingen in een VNet?
Nee.

### <a name="can-i-use-tracert-to-diagnose-connectivity"></a>Kan ik tracert gebruiken om de connectiviteit te diagnosticeren?
Nee.

### <a name="can-i-add-subnets-after-the-vnet-is-created"></a>Kan ik subnetten toevoegen nadat de VNet is gemaakt?
Ja. Subnetten kunnen op elk moment aan VNets worden toegevoegd, zolang het subnetadresbereik geen deel uitmaakt van een ander subnet en er ruimte overblijft in het adresbereik van het virtuele netwerk.

### <a name="can-i-modify-the-size-of-my-subnet-after-i-create-it"></a>Kan ik de grootte van mijn subnet wijzigen nadat ik het heb gemaakt?
Ja. U kunt een subnet toevoegen, verwijderen, uitbreiden of verkleinen als hierin geen VM's of services zijn geïmplementeerd.

### <a name="can-i-modify-subnets-after-i-created-them"></a>Kan ik subnetten wijzigen nadat ik ze heb gemaakt?
Ja. U de CIDR-blokken die door een VNet worden gebruikt, toevoegen, verwijderen en wijzigen.

### <a name="if-i-am-running-my-services-in-a-vnet-can-i-connect-to-the-internet"></a>Als ik mijn services in een VNet uitgebruik, kan ik dan verbinding maken met internet?
Ja. Alle services die binnen een VNet worden geïmplementeerd, kunnen uitgaand verbinding maken met het internet. Zie [Uitgaande verbindingen](../load-balancer/load-balancer-outbound-connections.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie over uitgaande internetverbindingen in Azure. Als u binnenkomend wilt aansluiten op een resource die is geïmplementeerd via ResourceManager, moet de resource een openbaar IP-adres hebben dat eraan is toegewezen. Zie Openbare IP-adressen voor meer informatie over openbare [IP-adressen.](virtual-network-public-ip-address.md) Elke Azure Cloud Service die in Azure wordt geïmplementeerd, heeft een openbare adresseerbare VIP toegewezen. U definieert invoereindpunten voor PaaS-rollen en eindpunten voor virtuele machines om deze services in staat te stellen verbindingen vanaf het internet te accepteren.

### <a name="do-vnets-support-ipv6"></a>Ondersteunen VNets IPv6?
Ja, VNets kunnen IPv4-only of dual stack (IPv4+IPv6) zijn.  Zie Overzicht [van IPv6 voor Azure Virtual Networks voor](./ipv6-overview.md)meer informatie.

### <a name="can-a-vnet-span-regions"></a>Kan een VNet-overspanningsregio's overspannen?
Nee. Een VNet is beperkt tot één regio. Een virtueel netwerk omvat echter beschikbaarheidszones. Zie [Overzicht van beschikbaarheidszones](../availability-zones/az-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor meer informatie over beschikbaarheidszones. U virtuele netwerken in verschillende regio's verbinden met virtuele netwerkpeering. Zie Overzicht [van virtuele netwerkpeering voor](virtual-network-peering-overview.md) meer informatie

### <a name="can-i-connect-a-vnet-to-another-vnet-in-azure"></a>Kan ik een VNet verbinden met een andere VNet in Azure?
Ja. U een VNet verbinden met een ander VNet met behulp van:
- **Virtueel netwerkpeering:** zie [VNet-peering-overzicht](virtual-network-peering-overview.md) voor meer informatie
- **Een Azure VPN-gateway:** zie [Een VNet-naar-VNet-verbinding configureren](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json)voor meer informatie. 

## <a name="name-resolution-dns"></a>Naamomzetting (DNS)

### <a name="what-are-my-dns-options-for-vnets"></a>Wat zijn mijn DNS-opties voor VNets?
Gebruik de beslissingstabel op de pagina [Naamresolutie voor VM's en Rolinstanties](virtual-networks-name-resolution-for-vms-and-role-instances.md) om u te begeleiden bij alle beschikbare DNS-opties.

### <a name="can-i-specify-dns-servers-for-a-vnet"></a>Kan ik DNS-servers opgeven voor een VNet?
Ja. U IP-adressen van DNS-servers opgeven in de VNet-instellingen. De instelling wordt toegepast als de standaard DNS-server(s) voor alle VM's in het VNet.

### <a name="how-many-dns-servers-can-i-specify"></a>Hoeveel DNS-servers kan ik opgeven?
[Azure-limieten nagaan](../azure-resource-manager/management/azure-subscription-service-limits.md?toc=%2fazure%2fvirtual-network%2ftoc.json#networking-limits).

### <a name="can-i-modify-my-dns-servers-after-i-have-created-the-network"></a>Kan ik mijn DNS-servers wijzigen nadat ik het netwerk heb gemaakt?
Ja. U de DNS-serverlijst voor uw VNet op elk gewenst moment wijzigen. Als u uw DNS-serverlijst wijzigt, moet u een DHCP-leaseverlenging uitvoeren op alle getroffen VM's in het VNet, om de nieuwe DNS-instellingen van kracht te laten worden. Voor VM's met Windows OS `ipconfig /renew` kunt u dit doen door rechtstreeks op de VM te typen. Raadpleeg voor andere besturingssysteemtypen de DHCP-leasevernieuwingsdocumentatie voor het specifieke besturingssysteemtype. 

### <a name="what-is-azure-provided-dns-and-does-it-work-with-vnets"></a>Wat is door Azure geleverde DNS en werkt het met VNets?
Azure-meegeleverde DNS is een DNS-service met meerdere tenant's die door Microsoft wordt aangeboden. Azure registreert al uw VM's en cloudservicerolexemplaren in deze service. Deze service biedt naamomzetting op hostnaam voor VM's en rolexemplaren die zich binnen dezelfde cloudservice bevinden, en door FQDN voor VM's en rolinstanties in hetzelfde VNet. Zie [Naamomzetting voor VM's en Cloud Services-rolinstanties voor](virtual-networks-name-resolution-for-vms-and-role-instances.md)meer informatie over DNS.

Er is een beperking tot de eerste 100 cloudservices in een VNet voor cross-tenantnaamomzetting met behulp van door Azure geleverde DNS. Als u uw eigen DNS-server gebruikt, is deze beperking niet van toepassing.

### <a name="can-i-override-my-dns-settings-on-a-per-vm-or-cloud-service-basis"></a>Kan ik mijn DNS-instellingen per VM of cloudservice overschrijven?
Ja. U DNS-servers per VM of cloudservice instellen om de standaardnetwerkinstellingen te overschrijven. Het is echter aan te raden om zoveel mogelijk DNS in het hele netwerk te gebruiken.

### <a name="can-i-bring-my-own-dns-suffix"></a>Kan ik mijn eigen DNS-achtervoegsel meenemen?
Nee. U geen aangepast DNS-achtervoegsel opgeven voor uw VNets.

## <a name="connecting-virtual-machines"></a>Virtuele machines aansluiten

### <a name="can-i-deploy-vms-to-a-vnet"></a>Kan ik VM's implementeren op een VNet?
Ja. Alle netwerkinterfaces (NIC) die zijn gekoppeld aan een VM die is geïmplementeerd via het implementatiemodel van Resource Manager, moeten zijn verbonden met een VNet. VM's die via het klassieke implementatiemodel worden geïmplementeerd, kunnen optioneel worden aangesloten op een VNet.

### <a name="what-are-the-different-types-of-ip-addresses-i-can-assign-to-vms"></a>Wat zijn de verschillende typen IP-adressen die ik aan VM's kan toewijzen?
* **Privé:** Toegewezen aan elke NIC binnen elke VM. Het adres wordt toegewezen met behulp van de statische of dynamische methode. Privé-IP-adressen worden toegewezen vanuit het bereik dat u hebt opgegeven in de subnetinstellingen van uw VNet. Resources die via het klassieke implementatiemodel worden geïmplementeerd, krijgen privé-IP-adressen toegewezen, zelfs als ze niet zijn verbonden met een VNet. Het gedrag van de toewijzingsmethode is anders, afhankelijk van of een resource is geïmplementeerd met het ResourceManager- of klassieke implementatiemodel: 

  - **Resourcebeheer:** een privé-IP-adres dat is toegewezen met de dynamische of statische methode, blijft toegewezen aan een virtuele machine (Resource Manager) totdat de bron is verwijderd. Het verschil is dat u het adres selecteert dat u wilt toewijzen bij het gebruik van statisch, en Azure kiest bij het gebruik van dynamisch. 
  - **Klassiek:** een privé-IP-adres dat is toegewezen aan de dynamische methode, kan veranderen wanneer een virtuele machine (klassieke) VM opnieuw wordt gestart nadat deze in de gestopte (deallocated) status is geweest. Als u ervoor moet zorgen dat het privé-IP-adres voor een resource die via het klassieke implementatiemodel wordt geïmplementeerd, nooit wordt gewijzigd, wijst u een privé-IP-adres toe met de statische methode.

* **Publiek:** Eventueel toegewezen aan NIC's die zijn gekoppeld aan VM's die zijn geïmplementeerd via het Azure Resource Manager-implementatiemodel. Het adres kan worden toegewezen met de statische of dynamische toewijzingsmethode. Alle VM's en Cloud Services-rolexemplaren die via het klassieke implementatiemodel worden geïmplementeerd, bestaan binnen een cloudservice, waaraan een *dynamisch*VIP-adres (Public Virtual) is toegewezen. Een openbaar *statisch* IP-adres, [een gereserveerd IP-adres](virtual-networks-reserved-public-ip.md)genoemd, kan optioneel als VIP worden toegewezen. U openbare IP-adressen toewijzen aan afzonderlijke VM's of Cloud Services-rolexemplaren die zijn geïmplementeerd via het klassieke implementatiemodel. Deze adressen worden openbaar IP op instantieniveau genoemd [(ILPIP-adressen](virtual-networks-instance-level-public-ip.md) en kunnen dynamisch worden toegewezen.

### <a name="can-i-reserve-a-private-ip-address-for-a-vm-that-i-will-create-at-a-later-time"></a>Kan ik een privé-IP-adres reserveren voor een vm die ik op een later tijdstip zal maken?
Nee. U geen privé-IP-adres reserveren. Als er een privé-IP-adres beschikbaar is, wordt het door de DHCP-server toegewezen aan een VM of rolinstantie. De VM kan wel of niet degene zijn waaraan het privé-IP-adres is toegewezen. U echter het privé-IP-adres van een reeds gemaakte VM wijzigen in elk beschikbaar privé-IP-adres.

### <a name="do-private-ip-addresses-change-for-vms-in-a-vnet"></a>Veranderen privé-IP-adressen voor VM's in een VNet?
Dat hangt ervan af. Als de VM is geïmplementeerd via Resourcemanager, nr, ongeacht of het IP-adres is toegewezen met de statische of dynamische toewijzingsmethode. Als de VM is geïmplementeerd via het klassieke implementatiemodel, kunnen dynamische IP-adressen worden gewijzigd wanneer een vm is gestart nadat deze in de gestopte status (deallocated) is geweest. Het adres wordt vrijgegeven van een VM die is geïmplementeerd via een implementatiemodel wanneer de VM wordt verwijderd.

### <a name="can-i-manually-assign-ip-addresses-to-nics-within-the-vm-operating-system"></a>Kan ik handmatig IP-adressen toewijzen aan NIC's binnen het VM-besturingssysteem?
Ja, maar het wordt niet aanbevolen, tenzij het nodig is, zoals bij het toewijzen van meerdere IP-adressen aan een virtuele machine. Zie Meerdere [IP-adressen toevoegen aan een virtuele machine voor](virtual-network-multiple-ip-addresses-portal.md#os-config)meer informatie. Als het IP-adres dat is toegewezen aan een Azure NIC dat is gekoppeld aan een VM-wijziging wijzigt en het IP-adres in het VM-besturingssysteem anders is, verliest u de verbinding met de VM.

### <a name="if-i-stop-a-cloud-service-deployment-slot-or-shutdown-a-vm-from-within-the-operating-system-what-happens-to-my-ip-addresses"></a>Wat gebeurt er met mijn IP-adressen als ik een implementatiesleuf voor cloudservice stop of een VM afsluit vanuit het besturingssysteem?
Niets. De IP-adressen (openbare VIP, openbaar en privé) blijven toegewezen aan de implementatiesleuf of vm voor de cloudservice.

### <a name="can-i-move-vms-from-one-subnet-to-another-subnet-in-a-vnet-without-redeploying"></a>Kan ik VM's van het ene subnet naar een ander subnet in een VNet verplaatsen zonder opnieuw te implementeren?
Ja. U meer informatie vinden in de [instantie Een virtuele machine of rol naar een ander subnetartikel](virtual-networks-move-vm-role-to-subnet.md) verplaatsen.

### <a name="can-i-configure-a-static-mac-address-for-my-vm"></a>Kan ik een statisch MAC-adres configureren voor mijn VM?
Nee. Een MAC-adres kan niet statisch worden geconfigureerd.

### <a name="will-the-mac-address-remain-the-same-for-my-vm-once-its-created"></a>Blijft het MAC-adres voor mijn VM hetzelfde als deze is gemaakt?
Ja, het MAC-adres blijft hetzelfde voor een VM die is geïmplementeerd via zowel de Resource Manager- als de klassieke implementatiemodellen totdat het is verwijderd. Voorheen werd het MAC-adres vrijgegeven als de VM is gestopt (deallocated), maar nu wordt het MAC-adres behouden, zelfs wanneer de VM zich in de deallocatie bevindt. Het MAC-adres blijft toegewezen aan de netwerkinterface totdat de netwerkinterface wordt verwijderd of het privé-IP-adres dat is toegewezen aan de primaire IP-configuratie van de primaire netwerkinterface wordt gewijzigd. 

### <a name="can-i-connect-to-the-internet-from-a-vm-in-a-vnet"></a>Kan ik verbinding maken met internet via een VM in een VNet?
Ja. Alle VM's en Cloud Services-rolexemplaren die binnen een VNet zijn geïmplementeerd, kunnen verbinding maken met internet.

## <a name="azure-services-that-connect-to-vnets"></a>Azure-services die verbinding maken met VNets

### <a name="can-i-use-azure-app-service-web-apps-with-a-vnet"></a>Kan ik Azure App Service Web Apps gebruiken met een VNet?
Ja. U Web Apps implementeren in een VNet met behulp van een ASE -serviceomgeving( App Service-omgeving), de back-end van uw apps verbinden met uw VNets met VNet-integratie en binnenkomend verkeer met serviceeindpunten vergrendelen. Raadpleeg voor meer informatie de volgende artikelen:

* [App Service-netwerkfuncties](../app-service/networking-features.md)
* [Web-apps maken in een app-serviceomgeving](../app-service/environment/app-service-web-how-to-create-a-web-app-in-an-ase.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Een app integreren met een virtueel Azure-netwerk](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
* [Beperkingen voor toegang voor App-service](../app-service/app-service-ip-restrictions.md)

### <a name="can-i-deploy-cloud-services-with-web-and-worker-roles-paas-in-a-vnet"></a>Kan ik Cloud Services implementeren met web- en werknemersrollen (PaaS) in een VNet?
Ja. U (optioneel) cloudservices-rolinstanties implementeren binnen VNets. Hiervoor geeft u de VNet-naam en de rol/subnettoewijzingen op in het gedeelte netwerkconfiguratie van uw serviceconfiguratie. U hoeft geen van uw binaire bestanden bij te werken.

### <a name="can-i-connect-a-virtual-machine-scale-set-to-a-vnet"></a>Kan ik een virtuele machineschaal instellen op een VNet?
Ja. U moet een virtuele machineschaal instellen op een VNet.

### <a name="is-there-a-complete-list-of-azure-services-that-can-i-deploy-resources-from-into-a-vnet"></a>Is er een volledige lijst met Azure-services waarmee ik resources kan implementeren in een VNet?
Ja, zie [Virtuele netwerkintegratie voor Azure-services voor](virtual-network-for-azure-services.md)meer informatie.

### <a name="how-can-i-restrict-access-to-azure-paas-resources-from-a-vnet"></a>Hoe kan ik de toegang tot Azure PaaS-bronnen vanaf een VNet beperken?

Resources die zijn geïmplementeerd via sommige Azure PaaS-services (zoals Azure Storage en Azure SQL Database), kunnen de netwerktoegang tot VNet beperken door het gebruik van eindpunten voor virtuele netwerkservices of Azure Private Link. Zie Overzicht [van eindpunten voor virtuele netwerkservices](virtual-network-service-endpoints-overview.md), overzicht van [Azure Private Link](../private-link/private-link-overview.md)

### <a name="can-i-move-my-services-in-and-out-of-vnets"></a>Kan ik mijn services in- en uitvnets verplaatsen?
Nee. U services niet in- en uitVNets verplaatsen. Als u een bron naar een andere VNet wilt verplaatsen, moet u de bron verwijderen en opnieuw implementeren.

## <a name="security"></a>Beveiliging

### <a name="what-is-the-security-model-for-vnets"></a>Wat is het beveiligingsmodel voor VNets?
VNets zijn geïsoleerd van elkaar en andere services die worden gehost in de Azure-infrastructuur. Een VNet is een vertrouwensgrens.

### <a name="can-i-restrict-inbound-or-outbound-traffic-flow-to-vnet-connected-resources"></a>Kan ik de inkomende of uitgaande verkeersstroom beperken naar VNet-verbonden resources?
Ja. U [netwerkbeveiligingsgroepen](security-overview.md) toepassen op afzonderlijke subnetten binnen een VNet, NIC's die zijn gekoppeld aan een VNet of beide.

### <a name="can-i-implement-a-firewall-between-vnet-connected-resources"></a>Kan ik een firewall implementeren tussen VNet-verbonden bronnen?
Ja. U een [virtueel firewallnetwerk](https://azure.microsoft.com/marketplace/?term=firewall) van verschillende leveranciers implementeren via de Azure Marketplace.

### <a name="is-there-information-available-about-securing-vnets"></a>Is er informatie beschikbaar over het beveiligen van VNets?
Ja. Zie overzicht [azure network security voor](../security/fundamentals/network-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)meer informatie.

## <a name="apis-schemas-and-tools"></a>API's, schema's en hulpprogramma's

### <a name="can-i-manage-vnets-from-code"></a>Kan ik VNets beheren vanuit code?
Ja. U REST-API's voor VNets gebruiken in de [Azure Resource Manager-](/rest/api/virtual-network) en [klassieke](https://go.microsoft.com/fwlink/?LinkId=296833) implementatiemodellen.

### <a name="is-there-tooling-support-for-vnets"></a>Is er tooling ondersteuning voor VNets?
Ja. Meer informatie over het gebruik van:
- De Azure-portal voor het implementeren van VNets via de [Azure Resource Manager](manage-virtual-network.md#create-a-virtual-network) en de [klassieke](virtual-networks-create-vnet-classic-pportal.md) implementatiemodellen.
- PowerShell voor het beheer van VNets die worden geïmplementeerd via de [Resource Manager-](/powershell/module/az.network) en [klassieke](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0) implementatiemodellen.
- De Azure command-line interface (CLI) voor het implementeren en beheren van VNets geïmplementeerd via de [Resource Manager](/cli/azure/network/vnet) en [klassieke](../virtual-machines/azure-cli-arm-commands.md?toc=%2fazure%2fvirtual-network%2ftoc.json#network-resources) implementatiemodellen.  

## <a name="vnet-peering"></a>VNet-peering

### <a name="what-is-vnet-peering"></a>Wat is VNet-peering?
VNet peering (of virtuele netwerk peering) stelt u in staat om virtuele netwerken te verbinden. Met een VNet-peeringverbinding tussen virtuele netwerken u verkeer tussen deze netwerken privé routeren via IPv4-adressen. Virtuele machines in de peered VNets kunnen met elkaar communiceren alsof ze zich binnen hetzelfde netwerk bevinden. Deze virtuele netwerken kunnen zich in dezelfde regio of in verschillende regio's bevinden (ook bekend als Global VNet Peering). VNet-peeringverbindingen kunnen ook worden gemaakt voor Azure-abonnementen.

### <a name="can-i-create-a-peering-connection-to-a-vnet-in-a-different-region"></a>Kan ik een peeringverbinding maken met een VNet in een andere regio?
Ja. Global VNet-peering stelt u in staat om vnets in verschillende regio's te peeren. Global VNet-peering is beschikbaar in alle azure public regions, China cloud regions en Government cloud regions. U niet globaal peeren van Azure-openbare regio's naar nationale cloudregio's.

### <a name="what-are-the-constraints-related-to-global-vnet-peering-and-load-balancers"></a>Wat zijn de beperkingen met betrekking tot Global VNet Peering en Load Balancers?
Als de twee virtuele netwerken in twee verschillende regio's worden peered over Global VNet Peering, u geen verbinding maken met resources die zich achter een Basic Load Balancer bevinden via het Front End IP van de Load Balancer. Deze beperking bestaat niet voor een Standaard Load Balancer.
De volgende resources kunnen Basic Load Balancers gebruiken, wat betekent dat u ze niet bereiken via het Front End IP van de Load Balancer via Global VNet Peering. U echter Global VNet-peering gebruiken om de bronnen rechtstreeks via hun privé VNet IP's te bereiken, indien toegestaan. 
- VM's achter Basic Load Balancers
- Virtuele machineschaalsets met Basic Load Balancers 
- Redis Cache 
- Application Gateway (v1) SKU
- Service Fabric
- SQL MI
- API Management
- Active Directory Domain Service (ADDS)
- Logic Apps
- HDInsight
-   Azure Batch
- App Service-omgeving

U verbinding maken met deze bronnen via ExpressRoute of VNet-to-VNet via VNet Gateways.

### <a name="can-i-enable-vnet-peering-if-my-virtual-networks-belong-to-subscriptions-within-different-azure-active-directory-tenants"></a>Kan ik VNet-peering inschakelen als mijn virtuele netwerken behoren tot abonnementen binnen verschillende Azure Active Directory-tenants?
Ja. Het is mogelijk om VNet Peering (lokaal of globaal) vast te stellen als uw abonnementen tot verschillende Azure Active Directory-tenants behoren. Dit kan via PowerShell of CLI. Portal wordt nog niet ondersteund.

### <a name="my-vnet-peering-connection-is-in-initiated-state-why-cant-i-connect"></a>Mijn VNet-peering-verbinding is *gestart,* waarom kan ik geen verbinding maken?
Als uw peering-verbinding zich in een gestarte status *bevindt,* betekent dit dat u slechts één koppeling hebt gemaakt. Er moet een bidirectionele koppeling worden gemaakt om een succesvolle verbinding tot stand te brengen. Om bijvoorbeeld VNet A naar VNet B te peeren, moet er een koppeling worden gemaakt van VNetA naar VNetB en van VNetB naar VNetA. Als u beide koppelingen maakt, wordt de status gewijzigd *in Verbonden*.

### <a name="my-vnet-peering-connection-is-in-disconnected-state-why-cant-i-create-a-peering-connection"></a>Mijn VNet-peeringverbinding is *verbroken,* waarom kan ik geen peeringverbinding maken?
Als uw VNet-peeringverbinding in de *verbroken* status staat is, betekent dit dat een van de koppelingen die zijn gemaakt, is verwijderd. Als u een peering-verbinding opnieuw wilt herstellen, moet u de koppeling verwijderen en opnieuw maken.

### <a name="can-i-peer-my-vnet-with-a-vnet-in-a-different-subscription"></a>Kan ik in een ander abonnement met een VNet op mijn VNet gekeken?
Ja. U vnets peeren in abonnementen en in verschillende regio's.

### <a name="can-i-peer-two-vnets-with-matching-or-overlapping-address-ranges"></a>Kan ik twee VNets met overeenkomende of overlappende adresbereiken peer?
Nee. Adresruimten mogen elkaar niet overlappen om VNet-peering in te schakelen.

### <a name="how-much-do-vnet-peering-links-cost"></a>Hoeveel kosten VNet peering links?
Er zijn geen kosten verbonden aan het maken van een VNet-peering-verbinding. Gegevensoverdracht tussen peeringverbindingen wordt in rekening gebracht. [Zie hier](https://azure.microsoft.com/pricing/details/virtual-network/).

### <a name="is-vnet-peering-traffic-encrypted"></a>Is VNet peering verkeer versleuteld?
Nee. Verkeer tussen resources in peered VNets is privé en geïsoleerd. Het blijft volledig op de Microsoft Backbone.

### <a name="why-is-my-peering-connection-in-a-disconnected-state"></a>Waarom is mijn peering-verbinding in de *verbroken* status?
VNet-peeringverbindingen gaan in *de verbroken* status wanneer één VNet-peeringkoppeling wordt verwijderd. U moet beide koppelingen verwijderen om een succesvolle peering-verbinding te herstellen.

### <a name="if-i-peer-vneta-to-vnetb-and-i-peer-vnetb-to-vnetc-does-that-mean-vneta-and-vnetc-are-peered"></a>Als ik VNetA peer naar VNetB en ik peer VNetB naar VNetC, betekent dat dan vneta en VNetC worden peered?
Nee. Transitief peering wordt niet ondersteund. U moet peer VNetA en VNetC om dit te laten plaatsvinden.

### <a name="are-there-any-bandwidth-limitations-for-peering-connections"></a>Zijn er bandbreedtebeperkingen voor peering-verbindingen?
Nee. VNet peering, lokaal of globaal, legt geen bandbreedtebeperkingen op. Bandbreedte wordt alleen beperkt door de VM of de compute resource.

### <a name="how-can-i-troubleshoot-vnet-peering-issues"></a>Hoe kan ik problemen met VNet Peering oplossen?
Hier is een [probleemoplosser gids](https://support.microsoft.com/en-us/help/4486956/troubleshooter-for-virtual-network-peering-issues) die u proberen.

## <a name="virtual-network-tap"></a>Virtual Network TAP

### <a name="which-azure-regions-are-available-for-virtual-network-tap"></a>Welke Azure-regio's zijn beschikbaar voor het virtuele netwerk TAP?
Virtuele netwerk TAP preview is beschikbaar in alle Azure-regio's. De bewaakte netwerkinterfaces, de TAP-bron voor virtuele netwerken en de verzamel- of analyseoplossing moeten in dezelfde regio worden geïmplementeerd.

### <a name="does-virtual-network-tap-support-any-filtering-capabilities-on-the-mirrored-packets"></a>Ondersteunt Virtual Network TAP filtermogelijkheden op de gespiegelde pakketten?
Filtermogelijkheden worden niet ondersteund met de tap-preview van het virtuele netwerk. Wanneer een TAP-configuratie wordt toegevoegd aan een netwerkinterface, wordt een diepe kopie van al het binnendringende en uitgaande verkeer op de netwerkinterface gestreamd naar de TAP-bestemming.

### <a name="can-multiple-tap-configurations-be-added-to-a-monitored-network-interface"></a>Kunnen meerdere TAP-configuraties worden toegevoegd aan een bewaakte netwerkinterface?
Een bewaakte netwerkinterface kan slechts één TAP-configuratie hebben. Neem contact op met de individuele [partneroplossing](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions) voor de mogelijkheid om meerdere kopieën van het TAP-verkeer te streamen naar de analysetools van uw keuze.

### <a name="can-the-same-virtual-network-tap-resource-aggregate-traffic-from-monitored-network-interfaces-in-more-than-one-virtual-network"></a>Kan hetzelfde virtuele netwerk TAP-bronverkeer verzamelen van bewaakte netwerkinterfaces in meer dan één virtueel netwerk?
Ja. Dezelfde TAP-bron voor virtueel netwerk kan worden gebruikt om gespiegeld verkeer te aggregeren van bewaakte netwerkinterfaces in gepeerde virtuele netwerken in hetzelfde abonnement of een ander abonnement. De TAP-bron voor virtueel netwerk en de doellast-balancer of de netwerkinterface voor bestemming moeten in hetzelfde abonnement zitten. Alle abonnementen moeten onder dezelfde Azure Active Directory-tenant staan.

### <a name="are-there-any-performance-considerations-on-production-traffic-if-i-enable-a-virtual-network-tap-configuration-on-a-network-interface"></a>Zijn er prestatieoverwegingen voor het productieverkeer als ik een tap-configuratie voor virtueel netwerk op een netwerkinterface inschakel?

Virtueel netwerk TAP is in preview. Tijdens de preview is er geen servicelevelovereenkomst. De mogelijkheid mag niet worden gebruikt voor productieworkloads. Wanneer een netwerkinterface voor virtuele machines is ingeschakeld met een TAP-configuratie, worden dezelfde resources op de Azure-host die aan de virtuele machine zijn toegewezen om het productieverkeer te verzenden, gebruikt om de spiegelfunctie uit te voeren en de gespiegelde pakketten te verzenden. Selecteer de juiste virtuele machinegrootte van [Linux](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [Windows](../virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-network%2ftoc.json) om ervoor te zorgen dat er voldoende resources beschikbaar zijn voor de virtuele machine om het productieverkeer en het gespiegelde verkeer te verzenden.

### <a name="is-accelerated-networking-for-linux-or-windows-supported-with-virtual-network-tap"></a>Wordt versnelde netwerken voor [Linux](create-vm-accelerated-networking-cli.md) of [Windows](create-vm-accelerated-networking-powershell.md) ondersteund met virtueel netwerk TAP?

U een TAP-configuratie toevoegen aan een netwerkinterface die is gekoppeld aan een virtuele machine die is ingeschakeld met versnelde netwerken. Maar de prestaties en latentie op de virtuele machine worden beïnvloed door het toevoegen van TAP-configuratie, omdat de offload voor het spiegelen van verkeer momenteel niet wordt ondersteund door Azure-versnelde netwerken.

## <a name="virtual-network-service-endpoints"></a>Service-eindpunten voor virtueel netwerk

### <a name="what-is-the-right-sequence-of-operations-to-set-up-service-endpoints-to-an-azure-service"></a>Wat is de juiste reeks bewerkingen om serviceeindpunten in te stellen voor een Azure-service?
Er zijn twee stappen om een Azure-servicebron te beveiligen via serviceeindpunten:
1. Schakel serviceeindpunten in voor de Azure-service.
2. VNet-ACL's instellen voor de Azure-service.

De eerste stap is een netwerkzijde en de tweede stap is een serviceresourcebewerking. Beide stappen kunnen worden uitgevoerd door dezelfde beheerder of verschillende beheerders op basis van de RBAC-machtigingen die aan de beheerdersrol zijn verleend. We raden u aan eerst serviceeindpunten voor uw virtuele netwerk in te schakelen voordat u VNet-ACL's aan de Azure-servicezijde instelt. Daarom moeten de stappen worden uitgevoerd in de bovenstaande volgorde om VNet-serviceeindpunten in te stellen.

>[!NOTE]
> Zowel de hierboven beschreven bewerkingen moeten worden voltooid voordat u de azure-servicetoegang tot het toegestane VNet en subnet beperken. Alleen het inschakelen van serviceeindpunten voor de Azure-service aan de netwerkzijde biedt u geen beperkte toegang. Bovendien moet u ook VNet-ACL's instellen aan de Azure-servicezijde.

Bepaalde services (zoals SQL en CosmosDB) staan uitzonderingen op de bovenstaande reeks toe via de **ignoremissingvnetServiceEndpoint-vlag.** Zodra de vlag is ingesteld op **True,** kunnen VNet-ACL's worden ingesteld aan de Azure-servicezijde voordat de serviceeindpunten aan de netwerkzijde worden ingesteld. Azure-services bieden deze vlag om klanten te helpen in gevallen waarin de specifieke IP-firewalls zijn geconfigureerd op Azure-services en het inschakelen van de serviceeindpunten aan de netwerkzijde kan leiden tot een verbindingsdaling sinds de bron-IP-wijzigingen van een openbaar IPv4-adres naar een privéadres. Als u VNet-ACL's instelt aan de Azure-servicezijde voordat serviceeindpunten aan de netwerkzijde worden ingesteld, u voorkomen dat de connectiviteit wordt neergeslagen.

### <a name="do-all-azure-services-reside-in-the-azure-virtual-network-provided-by-the-customer-how-does-vnet-service-endpoint-work-with-azure-services"></a>Bevinden alle Azure-services zich in het virtuele Azure-netwerk van de klant? Hoe werkt VNet-serviceeindpunt met Azure-services?

Nee, niet alle Azure-services bevinden zich in het virtuele netwerk van de klant. De meeste Azure-gegevensservices, zoals Azure Storage, Azure SQL en Azure Cosmos DB, zijn multitenantservices die toegankelijk zijn via openbare IP-adressen. Hier vindt u meer informatie over virtuele netwerkintegratie voor [Azure-services.](virtual-network-for-azure-services.md) 

Wanneer u de functie VNet-serviceeindpunten gebruikt (vnet-serviceeindpunt inschakelen aan de netwerkzijde en de juiste VNet-ACL's instelt aan de Azure-servicezijde), is de toegang tot een Azure-service beperkt tot een toegestaan VNet en subnet.

### <a name="how-does-vnet-service-endpoint-provide-security"></a>Hoe biedt VNet-serviceeindpunt beveiliging?

De vnet-serviceeindpuntfunctie (het inschakelen van VNet-serviceeindpunt aan de netwerkzijde en het instellen van geschikte VNet-ACL's aan de Azure-servicezijde) beperkt de Azure-servicetoegang tot het toegestane VNet en subnet, waardoor een beveiliging op netwerkniveau wordt geboden en isolatie van het Azure-serviceverkeer. Al het verkeer met VNet-serviceeindpunten stroomt over microsoft backbone, waardoor een andere isolatielaag van het openbare internet wordt geboden. Bovendien kunnen klanten ervoor kiezen om openbare internettoegang tot de Azure-servicebronnen volledig te verwijderen en alleen verkeer uit hun virtuele netwerk toe te staan via een combinatie van IP-firewall en VNet-ACL's, waardoor de Azure-servicebronnen worden beschermd tegen onbevoegde Toegang.      

### <a name="what-does-the-vnet-service-endpoint-protect---vnet-resources-or-azure-service"></a>Wat beschermt het Eindpunt van de VNet-service - VNet-resources of Azure-service?
VNet-serviceeindpunten helpen Azure-serviceresources te beschermen. VNet-resources worden beschermd via Network Security Groups (NSGs).

### <a name="is-there-any-cost-for-using-vnet-service-endpoints"></a>Zijn er kosten verbonden aan het gebruik van VNet-serviceeindpunten?

Nee, er zijn geen extra kosten voor het gebruik van VNet-serviceeindpunten.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-subscriptions"></a>Kan ik VNet-serviceeindpunten inschakelen en VNet-ACL's instellen als het virtuele netwerk en de Azure-serviceresources tot verschillende abonnementen behoren?

Ja, dat is mogelijk. Virtuele netwerken en Azure-serviceresources kunnen in dezelfde of verschillende abonnementen zijn. De enige vereiste is dat zowel de virtuele netwerk- als Azure-serviceresources onder dezelfde AD-tenant (Active Directory) moeten staan.

### <a name="can-i-turn-on-vnet-service-endpoints-and-set-up-vnet-acls-if-the-virtual-network-and-the-azure-service-resources-belong-to-different-ad-tenants"></a>Kan ik VNet-serviceeindpunten inschakelen en VNet-ACL's instellen als het virtuele netwerk en de Azure-serviceresources tot verschillende AD-tenants behoren?
Nee, VNet-serviceeindpunten en VNet-ACL's worden niet ondersteund voor AD-tenants.

### <a name="can-an-on-premises-devices-ip-address-that-is-connected-through-azure-virtual-network-gateway-vpn-or-expressroute-gateway-access-azure-paas-service-over-vnet-service-endpoints"></a>Kan het IP-adres van een on-premises apparaat dat is verbonden via Azure Virtual Network gateway (VPN) of ExpressRoute-gateway toegang krijgen tot Azure PaaS-service via VNet-serviceeindpunten?
Standaard zijn Azure-serviceresources die zijn beveiligd naar virtuele netwerken, niet bereikbaar vanaf on-premises netwerken. Als u verkeer van on-premises wilt toestaan, moet u ook openbare (meestal NAT)IP-adressen toestaan vanuit uw on-premises of ExpressRoute. Deze IP-adressen kunnen worden toegevoegd via de IP-firewallconfiguratie voor de Azure-servicebronnen.

### <a name="can-i-use-vnet-service-endpoint-feature-to-secure-azure-service-to-multiple-subnets-within-a-virtual-network-or-across-multiple-virtual-networks"></a>Kan ik de VNet Service Endpoint-functie gebruiken om Azure-service te beveiligen voor meerdere subnetten binnen een virtueel netwerk of in meerdere virtuele netwerken?
Als u Azure-services wilt beveiligen op meerdere subnetten binnen een virtueel netwerk of in meerdere virtuele netwerken, schakelt u serviceeindpunten aan de netwerkzijde aan elk van de subnetten onafhankelijk in en beveiligt u azure-servicebronnen voor alle subnetten door het instellen van serviceeindpunten aan de netwerkzijde aan elk van de subnetten en beveiligt u azure-servicebronnen voor alle subnetten door geschikte VNet-ACL's aan de Azure-servicezijde.
 
### <a name="how-can-i-filter-outbound-traffic-from-a-virtual-network-to-azure-services-and-still-use-service-endpoints"></a>Hoe kan ik uitgaand verkeer filteren van een virtueel netwerk naar Azure-services en nog steeds serviceeindpunten gebruiken?
Als u het verkeer dat is bestemd voor een Azure-service vanaf het virtuele netwerk, wilt controleren of filteren, kunt u een virtueel netwerkapparaat implementeren binnen het virtuele netwerk. Vervolgens u serviceeindpunten toepassen op het subnet waar het virtuele netwerktoestel wordt geïmplementeerd en Azure-serviceresources alleen via VNet-ACL's op dit subnet beveiligen. Dit scenario kan ook handig zijn als u azure-servicetoegang vanuit uw virtuele netwerk alleen wilt beperken tot specifieke Azure-bronnen met behulp van netwerkfilterapparatuur voor virtuele apparaten. Zie [Egress with network virtual appliances](https://docs.microsoft.com/azure/architecture/reference-architectures/dmz/nva-ha) (Uitgaand verkeer met virtueel-netwerkapparaten) voor meer informatie.

### <a name="what-happens-when-you-access-an-azure-service-account-that-has-a-virtual-network-access-control-list-acl-enabled-from-outside-the-vnet"></a>Wat gebeurt er als u toegang hebt tot een Azure-serviceaccount waarvoor een acl-lijst (Virtual Network Access Control List) is ingeschakeld van buiten het VNet?
De fout HTTP 403 of HTTP 404 wordt geretourneerd.

### <a name="are-subnets-of-a-virtual-network-created-in-different-regions-allowed-to-access-an-azure-service-account-in-another-region"></a>Hebben subnetten van een virtueel netwerk dat in verschillende regio's is gemaakt, toegang tot een Azure-serviceaccount in een andere regio? 
Ja, voor de meeste Azure-services hebben virtuele netwerken die in verschillende regio's zijn gemaakt, toegang tot Azure-services in een andere regio via de VNet-serviceeindpunten. Als een Azure Cosmos DB-account zich bijvoorbeeld in West US of East US bevindt en virtuele netwerken zich in meerdere regio's bevinden, heeft het virtuele netwerk toegang tot Azure Cosmos DB. Opslag en SQL zijn uitzonderingen en zijn regionaal van aard en zowel het virtuele netwerk als de Azure-service moeten zich in dezelfde regio bevinden.
  
### <a name="can-an-azure-service-have-both-a-vnet-acl-and-an-ip-firewall"></a>Kan een Azure-service zowel een VNet ACL als een IP-firewall hebben?
Ja, een VNet ACL en een IP-firewall kunnen naast elkaar bestaan. Beide functies vullen elkaar aan om isolatie en beveiliging te garanderen.
 
### <a name="what-happens-if-you-delete-a-virtual-network-or-subnet-that-has-service-endpoint-turned-on-for-azure-service"></a>Wat gebeurt er als u een virtueel netwerk of subnet verwijdert waarop serviceeindpunt is ingeschakeld voor Azure-service?
Verwijdering van VNets en subnetten zijn onafhankelijke bewerkingen en worden ondersteund, zelfs wanneer serviceeindpunten zijn ingeschakeld voor Azure-services. In gevallen waarin vnet-url's van De Azure-services zijn ingesteld, wordt voor vnets en subnetten de VNet ACL-gegevens die aan die Azure-service zijn gekoppeld, uitgeschakeld wanneer een VNet- of subnet waarvoor VNet-serviceeindpunt is ingeschakeld, wordt verwijderd.
 
### <a name="what-happens-if-an-azure-service-account-that-has-a-vnet-service-endpoint-enabled-is-deleted"></a>Wat gebeurt er als een Azure-serviceaccount waarvoor een VNet Service-eindpunt is ingeschakeld, wordt verwijderd?
Het verwijderen van een Azure-serviceaccount is een onafhankelijke bewerking en wordt ondersteund, zelfs wanneer het serviceeindpunt aan de netwerkzijde is ingeschakeld en VNet-ACL's aan azure-servicezijde zijn ingesteld. 

### <a name="what-happens-to-the-source-ip-address-of-a-resource-like-a-vm-in-a-subnet-that-has-vnet-service-endpoint-enabled"></a>Wat gebeurt er met het bron-IP-adres van een resource (zoals een VM in een subnet) waarvoor VNet-serviceeindpunt is ingeschakeld?
Wanneer eindpunten voor virtuele netwerkservices zijn ingeschakeld, worden de bron-IP-adressen van de bronnen in het subnet van uw virtuele netwerk overschakelt van het gebruik van openbare IPV4-adressen naar de privé-IP-adressen van het Virtuele Azure-netwerk voor verkeer naar Azure-service. Houd er rekening mee dat dit ertoe kan leiden dat specifieke IP-firewalls die eerder zijn ingesteld op een openbaar IPV4-adres op de Azure-services mislukken. 

### <a name="does-the-service-endpoint-route-always-take-precedence"></a>Heeft de eindpuntroute van de service altijd voorrang?
Serviceeindpunten voegen een systeemroute toe die voorrang heeft op BGP-routes en een optimale routering biedt voor het serviceeindpuntverkeer. Serviceeindpunten brengen serviceverkeer altijd rechtstreeks van uw virtuele netwerk naar de service op het Microsoft Azure-backbonenetwerk. Zie [Azure Virtual-netwerkverkeerroutering](virtual-networks-udr-overview.md)voor meer informatie over hoe Azure een route selecteert.
 
### <a name="how-does-nsg-on-a-subnet-work-with-service-endpoints"></a>Hoe werkt NSG op een subnet met serviceeindpunten?
Om de Azure-service te bereiken, moeten NSG's uitgaande connectiviteit toestaan. Als uw NSG's worden geopend voor al het uitgaande internetverkeer, moet het eindpuntverkeer van de service werken. U het uitgaande verkeer ook beperken tot het bedienen van IP's alleen met behulp van de Service-tags.  
 
### <a name="what-permissions-do-i-need-to-set-up-service-endpoints"></a>Welke machtigingen heb ik nodig om serviceeindpunten in te stellen?
Serviceeindpunten kunnen onafhankelijk van elkaar worden geconfigureerd op een virtueel netwerk door een gebruiker met schrijftoegang tot het virtuele netwerk. Als u Azure-serviceresources wilt beveiligen voor een VNet, moet de gebruiker **microsoft.Network/virtualNetworks/subnetten/joinViaServiceEndpoint/action** hebben voor de subnetten die worden toegevoegd. Deze machtiging is standaard opgenomen in de ingebouwde functie voor servicebeheerders en kan worden gewijzigd door aangepaste rollen te maken. Meer informatie over ingebouwde rollen en het toewijzen van specifieke machtigingen voor [aangepaste rollen](https://docs.microsoft.com/azure/role-based-access-control/custom-roles?toc=%2fazure%2fvirtual-network%2ftoc.json).
 

### <a name="can-i-filter-virtual-network-traffic-to-azure-services-allowing-only-specific-azure-service-resources-over-vnet-service-endpoints"></a>Kan ik virtueel netwerkverkeer filteren op Azure-services, waardoor alleen specifieke azure-serviceresources via VNet-serviceeindpunten worden toegestaan? 

Met het eindpuntbeleid voor het virtuele netwerk (VNet) u virtueel netwerkverkeer filteren op Azure-services, waardoor alleen specifieke Azure-serviceresources over de eindpunten van de service kunnen worden toegestaan. Endpoint-beleid biedt gedetailleerd toegangscontrole van het virtuele netwerkverkeer naar de Azure-services. Hier vindt u meer informatie over het [eindpuntbeleid](virtual-network-service-endpoint-policies-overview.md)voor service.

### <a name="does-azure-active-directory-azure-ad-support-vnet-service-endpoints"></a>Ondersteunt Azure Active Directory (Azure AD) VNet-serviceeindpunten?

Azure Active Directory (Azure AD) ondersteunt serviceeindpunten niet native. De volledige lijst met Azure Services-ondersteuning voor VNet-serviceeindpunten is [hier](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview)te zien. Houd er rekening mee dat de tag 'Microsoft.AzureActiveDirectory' die wordt vermeld onder serviceeindpunten voor services wordt gebruikt voor het ondersteunen van serviceeindpunten voor ADLS Gen 1. Voor ADLS Gen 1 maakt virtuele netwerkintegratie voor Azure Data Lake Storage Gen1 gebruik van de endpoint-beveiliging van de virtuele netwerkservice tussen uw virtuele netwerk en Azure AD (Azure AD) om extra beveiligingsclaims in het toegangstoken te genereren. Deze claims worden vervolgens gebruikt om het virtuele netwerk te verifiëren bij het Data Lake Storage Gen1-account en toegang toe te staan. Meer informatie over [Azure Data Lake Store Gen 1 VNet-integratie](../data-lake-store/data-lake-store-network-security.md?toc=%2fazure%2fvirtual-network%2ftoc.json)

### <a name="are-there-any-limits-on-how-many-vnet-service-endpoints-i-can-set-up-from-my-vnet"></a>Zijn er beperkingen op het aantal VNet-serviceeindpunten dat ik vanuit mijn VNet kan instellen?
Er is geen limiet op het totale aantal VNet-serviceeindpunten in een virtueel netwerk. Voor een Azure-servicebron (zoals een Azure Storage-account) kunnen services limieten afdwingen voor het aantal subnetten dat wordt gebruikt voor het beveiligen van de bron. In de volgende tabel worden enkele voorbeeldlimieten weergegeven: 

|||
|---|---|
|Azure-service| Beperkingen op VNet-regels|
|Azure Storage| 100|
|Azure SQL| 128|
|Azure SQL Data Warehouse|  128|
|Azure KeyVault|    127|
|Azure Cosmos DB|   64|
|Azure Event Hub|   128|
|Azure Service Bus| 128|
|Azure Data Lake Store V1|  100|
 
>[!NOTE]
> De limieten worden onderworpen aan wijzigingen naar goeddunken van de Azure-service. Raadpleeg de desbetreffende servicedocumentatie voor servicegegevens. 




  



