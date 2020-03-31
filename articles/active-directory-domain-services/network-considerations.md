---
title: Netwerkplanning en -verbindingen voor Azure AD-domeinservices | Microsoft Documenten
description: Meer informatie over enkele overwegingen en bronnen voor het ontwerpen van virtuele netwerken die worden gebruikt voor connectiviteit wanneer u Azure Active Directory Domain Services uitvoert.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 01/21/2020
ms.author: iainfou
ms.openlocfilehash: e00ec8448739ac30950877a2ae196aa78cde750c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79264191"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-ad-domain-services"></a>Overwegingen voor virtueel netwerkontwerp en configuratieopties voor Azure AD Domain Services

Aangezien Azure Active Directory Domain Services (AD DS) verificatie- en beheerservices biedt voor andere toepassingen en workloads, is netwerkconnectiviteit een belangrijk onderdeel. Zonder correct geconfigureerde virtuele netwerkbronnen kunnen toepassingen en workloads niet communiceren met en de functies van Azure AD DS gebruiken. Plan uw virtuele netwerkvereisten om ervoor te zorgen dat Azure AD DS uw toepassingen en workloads kan weergeven als dat nodig is.

In dit artikel worden ontwerpoverwegingen en vereisten beschreven voor een virtueel Azure-netwerk ter ondersteuning van Azure AD DS.

## <a name="azure-virtual-network-design"></a>Azure virtueel netwerkontwerp

Als u netwerkconnectiviteit wilt bieden en toepassingen en services wilt laten verifiëren tegen Azure AD DS, gebruikt u een virtueel Azure-netwerk en subnet. In het ideale leven moet Azure AD DS worden geïmplementeerd in zijn eigen virtuele netwerk. U een apart subnet van toepassingen opnemen in hetzelfde virtuele netwerk om de werkbelasting van uw beheer-VM of lichte toepassing te hosten. Een apart virtueel netwerk voor grotere of complexe toepassingsworkloads, die zijn gekeken naar het virtuele Azure AD DS-netwerk, is meestal het meest geschikte ontwerp. Andere ontwerpkeuzes zijn geldig, mits u voldoet aan de vereisten die in de volgende secties voor het virtuele netwerk en subnet worden beschreven.

Bij het ontwerpen van het virtuele netwerk voor Azure AD DS zijn de volgende overwegingen van toepassing:

* Azure AD DS moet worden geïmplementeerd in dezelfde Azure-regio als uw virtuele netwerk.
    * Op dit moment u slechts één door Azure AD DS beheerd domein implementeren per Azure AD-tenant. Het beheerde Azure AD DS-domein wordt geïmplementeerd in één regio. Zorg ervoor dat u een virtueel netwerk maakt of selecteert in een [regio die Azure AD DS ondersteunt.](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all)
* Houd rekening met de nabijheid van andere Azure-regio's en de virtuele netwerken die uw toepassingsworkloads hosten.
    * Als u de latentie wilt minimaliseren, houdt u uw kerntoepassingen dicht bij of in dezelfde regio als het virtuele netwerksubnet voor uw door Azure AD DS beheerde domein. U vpn-verbindingen (virtual network peering) of vpn-verbindingen (virtual private network) gebruiken. Deze verbindingsopties worden in een volgende sectie besproken.
* Het virtuele netwerk kan niet afhankelijk zijn van andere DNS-services dan die van Azure AD DS.
    * Azure AD DS biedt een eigen DNS-service. Het virtuele netwerk moet zijn geconfigureerd om deze DNS-serviceadressen te gebruiken. Naamoplossing voor extra naamruimten kan worden uitgevoerd met voorwaardelijke doorstuurers.
    * U geen aangepaste DNS-serverinstellingen gebruiken om query's van andere DNS-servers te sturen, ook niet op VM's. Bronnen in het virtuele netwerk moeten gebruik maken van de DNS-service van Azure AD DS.

> [!IMPORTANT]
> U Azure AD DS niet naar een ander virtueel netwerk verplaatsen nadat u de service hebt ingeschakeld.

Een door Azure AD DS beheerd domein maakt verbinding met een subnet in een virtueel Azure-netwerk. Ontwerp dit subnet voor Azure AD DS met de volgende overwegingen:

* Azure AD DS moet worden geïmplementeerd in een eigen subnet. Gebruik geen bestaand subnet of een gateway-subnet.
* Er wordt een netwerkbeveiligingsgroep gemaakt tijdens de implementatie van een door Azure AD DS beheerd domein. Deze netwerkbeveiligingsgroep bevat de vereiste regels voor correcte servicecommunicatie.
    * Maak of gebruik geen bestaande netwerkbeveiligingsgroep met uw eigen aangepaste regels.
* Azure AD DS vereist 3-5 IP-adressen. Zorg ervoor dat uw subnet IP-adresbereik dit aantal adressen kan opgeven.
    * Als u de beschikbare IP-adressen beperkt, kan worden voorkomen dat Azure AD Domain Services twee domeincontrollers onderhoudt.

In het volgende voorbeelddiagram wordt een geldig ontwerp beschreven waarbij Azure AD DS een eigen subnet heeft, er een gateway-subnet is voor externe connectiviteit en toepassingsworkloads zich in een verbonden subnet binnen het virtuele netwerk bevinden:

![Aanbevolen subnetontwerp](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Verbindingen met het virtuele Azure AD DS-netwerk

Zoals in de vorige sectie is opgemerkt, u alleen een beheerd Azure AD Domain Services-domein maken in één virtueel netwerk in Azure en kan er slechts één beheerd domein worden gemaakt per Azure AD-tenant. Op basis van deze architectuur moet u mogelijk een of meer virtuele netwerken die uw toepassingsworkloads hosten, verbinden met uw virtuele Azure AD DS-netwerk.

U toepassingsworkloads die worden gehost in andere virtuele Azure-netwerken met elkaar verbinden met een van de volgende methoden:

* Peering op virtueel netwerk
* VPN-verbinding (Virtual Private Network)

### <a name="virtual-network-peering"></a>Peering op virtueel netwerk

Virtueel netwerkpeeren is een mechanisme dat twee virtuele netwerken in dezelfde regio verbindt via het Azure-backbonenetwerk. Wereldwijd virtueel netwerkpeeren kan virtueel netwerk verbinden tussen Azure-regio's. Eenmaal peered, de twee virtuele netwerken laat middelen, zoals VM's, rechtstreeks met elkaar communiceren met behulp van prive-IP-adressen. Met behulp van virtuele netwerkpeering u een door Azure AD DS beheerd domein implementeren met uw toepassingsworkloads die zijn geïmplementeerd in andere virtuele netwerken.

![Virtuele netwerkconnectiviteit met peering](./media/active-directory-domain-services-design-guide/vnet-peering.png)

Zie overzicht [van azure virtual network peering voor](../virtual-network/virtual-network-peering-overview.md)meer informatie.

### <a name="virtual-private-networking-vpn"></a>Virtual Private Networking (VPN)

U een virtueel netwerk op dezelfde manier verbinden met een ander virtueel netwerk (VNet-to-VNet) als u een virtueel netwerk configureren op een on-premises locatielocatie. Beide verbindingen maken gebruik van een VPN-gateway om een veilige tunnel te maken met behulp van IPsec/IKE. Met dit verbindingsmodel u Azure AD DS implementeren in een virtueel Azure-netwerk en vervolgens on-premises locaties of andere clouds verbinden.

![Virtuele netwerkconnectiviteit met behulp van een VPN-gateway](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

Lees [Een VNet-naar-VNet VPN-gatewayverbinding configureren met behulp van de Azure-portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal)voor meer informatie over het gebruik van virtual private networking.

## <a name="name-resolution-when-connecting-virtual-networks"></a>Naamresolutie bij het verbinden van virtuele netwerken

Virtuele netwerken die zijn verbonden met het virtuele netwerk azure AD Domain Services hebben doorgaans hun eigen DNS-instellingen. Wanneer u virtuele netwerken verbindt, configureert het niet automatisch naamomzetting voor het verbindende virtuele netwerk om services op te lossen die worden geleverd door het beheerde Azure AD DS-domein. Naamomzetting op de verbindende virtuele netwerken moet worden geconfigureerd om toepassingsworkloads in staat te stellen Azure AD Domain Services te vinden.

U naamomzetting inschakelen met behulp van voorwaardelijke DNS-expediteurs op de DNS-server die de verbindende virtuele netwerken ondersteunen, of door dezelfde DNS-IP-adressen van het virtuele azure AD Domain Service-netwerk te gebruiken.

## <a name="network-resources-used-by-azure-ad-ds"></a>Netwerkbronnen die worden gebruikt door Azure AD DS

Een door Azure AD DS beheerd domein maakt een aantal netwerkbronnen tijdens de implementatie. Deze resources zijn nodig voor een succesvolle bewerking en beheer van het beheerde Azure AD DS-domein en mogen niet handmatig worden geconfigureerd.

| Azure-bron                          | Beschrijving |
|:----------------------------------------|:---|
| Netwerkinterfacekaart                  | Azure AD DS host het beheerde domein op twee domeincontrollers (DC's) die op Windows Server worden uitgevoerd als Azure VM's. Elke VM heeft een virtuele netwerkinterface die verbinding maakt met uw virtuele netwerksubnet. |
| Dynamisch standaard openbaar IP-adres      | Azure AD DS communiceert met de synchronisatie- en beheerservice met behulp van een standaard Openbaar IP-adres van SKU. Zie [IP-adrestypen en toewijzingsmethoden in Azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md)voor meer informatie over openbare IP-adressen. |
| Azure-standaardloadbalancer            | Azure AD DS gebruikt een standaard SKU-loadbalancer voor nat (network address translation) en load balancing (bij gebruik met beveiligde LDAP). Zie Wat is Azure Load [Balancer voor](../load-balancer/load-balancer-overview.md) meer informatie over Azure load balancers? |
| NAT-regels (Network address translation) | Azure AD DS maakt en gebruikt drie NAT-regels voor de load balancer: één regel voor veilig HTTP-verkeer en twee regels voor het beveiligen van PowerShell-remoting. |
| Regels voor load balancers                     | Wanneer een door Azure AD DS beheerd domein is geconfigureerd voor beveiligde LDAP op TCP-poort 636, worden drie regels gemaakt en gebruikt op een load balancer om het verkeer te distribueren. |

> [!WARNING]
> Verwijder geen van de netwerkbronnen die zijn gemaakt door Azure AD DS. Als u een van de netwerkbronnen verwijdert, treedt er een azure AD DS-servicestoring op.

## <a name="network-security-groups-and-required-ports"></a>Netwerkbeveiligingsgroepen en vereiste poorten

Een [netwerkbeveiligingsgroep (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) bevat een lijst met regels die netwerkverkeer toestaan of weigeren voor verkeer in een virtueel Azure-netwerk. Er wordt een netwerkbeveiligingsgroep gemaakt wanneer u Azure AD DS implementeert met een set regels waarmee de service verificatie- en beheerfuncties kan bieden. Deze standaardnetwerkbeveiligingsgroep is gekoppeld aan het virtuele netwerksubnet waaruw Door Azure AD DS beheerde domein in is geïmplementeerd.

De volgende regels voor netwerkbeveiligingsgroepen zijn vereist voor Azure AD DS om verificatie- en beheerservices te bieden. Bewerk of verwijder deze regels voor netwerkbeveiliging niet voor het virtuele netwerksubnet waaruw Door Azure AD DS beheerde domein in is geïmplementeerd.

| Poortnummer | Protocol | Bron                             | Doel | Actie | Vereist | Doel |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | AzureActiveDirectoryDomainServices | Alle         | Toestaan  | Ja      | Synchronisatie met uw Azure AD-tenant. |
| 3389        | TCP      | CorpNetSaw (CorpNetSaw)                         | Alle         | Toestaan  | Ja      | Beheer van uw domein. |
| 5986        | TCP      | AzureActiveDirectoryDomainServices | Alle         | Toestaan  | Ja      | Beheer van uw domein. |
| 636         | TCP      | Alle                                | Alle         | Toestaan  | Nee       | Alleen ingeschakeld wanneer u beveiligde LDAP (LDAPS) configureert. |

> [!WARNING]
> Bewerk deze netwerkbronnen en -configuraties niet handmatig. Wanneer u een verkeerd geconfigureerde netwerkbeveiligingsgroep of een door de gebruiker gedefinieerde routetabel koppelt aan het subnet waarin Azure AD DS is geïmplementeerd, u de mogelijkheid van Microsoft om het domein te onderhouden en te beheren verstoren. Synchronisatie tussen uw Azure AD-tenant en uw door Azure AD DS beheerde domein is ook verstoord.
>
> Standaardregels voor *AllowVnetInBound*, *AllowAzureLoadBalancerInBound*, *DenyAllInBound*, *AllowVnetOutBound*, *AllowInternetOutBound*en *DenyAllOutBound* bestaan ook voor de netwerkbeveiligingsgroep. Bewerk of verwijder deze standaardregels niet.
>
> De Azure SLA is niet van toepassing op implementaties waarbij een onjuist geconfigureerde netwerkbeveiligingsgroep en/of door de gebruiker gedefinieerde routetabellen zijn toegepast die Azure AD DS blokkeren om uw domein bij te werken en te beheren.

### <a name="port-443---synchronization-with-azure-ad"></a>Poort 443 - synchronisatie met Azure AD

* Wordt gebruikt om uw Azure AD-tenant te synchroniseren met uw door Azure AD DS beheerde domein.
* Zonder toegang tot deze poort kan uw door Azure AD DS beheerde domein niet worden gesynchroniseerd met uw Azure AD-tenant. Gebruikers kunnen zich mogelijk niet aanmelden omdat wijzigingen in hun wachtwoorden niet worden gesynchroniseerd met uw door Azure AD DS beheerde domein.
* De inkomende toegang tot deze poort tot IP-adressen is standaard beperkt met de **AzureActiveDirectoryDomainServices-servicetag.**
* Beperk uitgaande toegang vanuit deze poort niet.

### <a name="port-3389---management-using-remote-desktop"></a>Poort 3389 - beheer met extern bureaublad

* Wordt gebruikt voor externe bureaubladverbindingen met domeincontrollers in uw door Azure AD DS beheerde domein.
* De standaardregel voor netwerkbeveiliging gebruikt de *Servicetag CorpNetSaw* om het verkeer verder te beperken.
    * Met deze servicetag kunnen alleen beveiligde werkstations in het Microsoft-bedrijfsnetwerk extern bureaublad gebruiken voor het beheerde Azure AD DS-domein.
    * Toegang is alleen toegestaan met zakelijke rechtvaardiging, zoals voor scenario's voor beheer of probleemoplossing.
* Deze regel kan worden ingesteld op *Weigeren*en alleen worden ingesteld *op Toestaan* wanneer dat nodig is. De meeste beheer- en bewakingstaken worden uitgevoerd met PowerShell remoting. RDP wordt alleen gebruikt in het zeldzame geval dat Microsoft op afstand moet worden verbonden met uw beheerde domein voor geavanceerde probleemoplossing.

> [!NOTE]
> U de *CorpNetSaw-servicetag* niet handmatig selecteren in de portal als u deze regel voor netwerkbeveiliging probeert te bewerken. U moet Azure PowerShell of de Azure CLI gebruiken om handmatig een regel te configureren die de *CorpNetSaw-servicetag* gebruikt.

### <a name="port-5986---management-using-powershell-remoting"></a>Poort 5986 - beheer met PowerShell remoting

* Wordt gebruikt om beheertaken uit te voeren met PowerShell-remoting in uw door Azure AD DS beheerde domein.
* Zonder toegang tot deze poort kan uw door Azure AD DS beheerde domein niet worden bijgewerkt, geconfigureerd, ondersteund of gecontroleerd.
* Voor azure AD DS-beheerde domeinen die een virtueel netwerk op basis van ResourceBeheer gebruiken, u de inkomende toegang tot deze poort beperken tot de *AzureActiveDirectoryDomainServices-servicetag.*
    * Voor verouderde Azure AD DS-beheerde domeinen met een virtueel netwerk op basis van klassiek u de inkomende toegang tot deze poort beperken tot de volgende bron-IP-adressen: *52.180.183.8*, *23.101.0.70*, *52.225.184.198*, *52.179.126.223*, *13.74.249.156*, *52.187.117,83*, *52.161.13.95*, *104.40.156,18*en *104.40.87.209*.

    > [!NOTE]
    > In 2017 werd Azure AD Domain Services beschikbaar voor host in een Azure Resource Manager-netwerk. Sindsdien hebben we een veiligere service kunnen bouwen met behulp van de moderne mogelijkheden van Azure Resource Manager. Omdat Azure Resource Manager-implementaties klassieke implementaties volledig vervangen, worden de klassieke virtuele netwerkimplementaties van Azure AD DS op 1 maart 2023 uitgeschakeld.
    >
    > Zie voor meer informatie het [officiële afschrijvingsbericht](https://azure.microsoft.com/updates/we-are-retiring-azure-ad-domain-services-classic-vnet-support-on-march-1-2023/)

## <a name="user-defined-routes"></a>Door de gebruiker gedefinieerde routes

Door de gebruiker gedefinieerde routes worden niet standaard gemaakt en zijn niet nodig om Azure AD DS correct te laten werken. Als u routetabellen moet gebruiken, moet u geen wijzigingen aanbrengen in de route *0.0.0.0.* Wijzigingen in deze route verstoren Azure AD Domain Services en stellen het beheerde domein in een niet-ondersteunde status.

U moet binnenkomend verkeer ook routeren van de IP-adressen die zijn opgenomen in de respectievelijke Azure-servicetags naar het subnet Azure AD Domain Services. Zie Azure IP Ranges and Service Tags [- Public Cloud](https://www.microsoft.com/en-us/download/details.aspx?id=56519)voor meer informatie over servicetags en het bijbehorende IP-adres.

> [!CAUTION]
> Deze IP-bereiken van Azure-datacenters kunnen zonder kennisgeving worden gewijzigd. Zorg ervoor dat u processen hebt om te valideren dat u over de nieuwste IP-adressen beschikt.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende artikelen voor meer informatie over enkele netwerkbronnen en verbindingsopties die door Azure AD DS worden gebruikt:

* [Azure virtual network peering Azure virtual network peering Azure virtual network peering Azure virtual](../virtual-network/virtual-network-peering-overview.md)
* [Azure VPN-gateways](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Netwerkbeveiligingsgroepen in Azure](../virtual-network/security-overview.md)
