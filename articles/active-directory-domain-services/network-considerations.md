---
title: Netwerk planning en verbindingen voor Azure AD Domain Services | Microsoft Docs
description: Meer informatie over een aantal overwegingen voor het ontwerpen van virtuele netwerken en bronnen die worden gebruikt voor connectiviteit wanneer u Azure Active Directory Domain Services uitvoert.
services: active-directory-ds
author: iainfoulds
manager: daveba
ms.assetid: 23a857a5-2720-400a-ab9b-1ba61e7b145a
ms.service: active-directory
ms.subservice: domain-services
ms.workload: identity
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: iainfou
ms.openlocfilehash: 1a6fb12311fe4474f03c22c91d9b478220adf5d1
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/25/2019
ms.locfileid: "75425538"
---
# <a name="virtual-network-design-considerations-and-configuration-options-for-azure-ad-domain-services"></a>Ontwerp overwegingen voor het virtuele netwerk en configuratie opties voor Azure AD Domain Services

Als Azure Active Directory Domain Services (AD DS) biedt verificatie-en beheer services aan andere toepassingen en werk belastingen, is de netwerk verbinding een belang rijk onderdeel. Zonder de juiste geconfigureerde virtuele netwerk bronnen kunnen toepassingen en werk belastingen niet communiceren met en gebruikmaken van de functies van Azure AD DS. Als u het virtuele netwerk op de juiste wijze plant, moet u ervoor zorgen dat Azure AD DS uw toepassingen en werk belastingen naar behoefte kan leveren.

In dit artikel vindt u een overzicht van overwegingen en vereisten voor het ontwerp van een virtueel Azure-netwerk dat Azure AD DS ondersteunt.

## <a name="azure-virtual-network-design"></a>Azure Virtual Network-ontwerp

Als u verbinding met het netwerk wilt maken en toepassingen en services wilt toestaan voor verificatie bij Azure AD DS, gebruikt u een virtueel netwerk en subnet van Azure. In het ideale geval moeten Azure AD DS worden geïmplementeerd in een eigen virtueel netwerk. U kunt een afzonderlijk toepassings subnet in hetzelfde virtuele netwerk toevoegen om uw beheer-VM of lichte toepassings werkbelastingen te hosten. Een apart virtueel netwerk voor grotere of complexe toepassings werkbelastingen, gekoppeld aan het virtuele netwerk van Azure AD DS, is doorgaans het meest geschikte ontwerp. Andere opties voor ontwerpen zijn geldig, op voor waarden dat u voldoet aan de vereisten die worden beschreven in de volgende secties voor het virtuele netwerk en subnet.

Wanneer u het virtuele netwerk voor Azure AD DS ontwerpt, zijn de volgende overwegingen van toepassing:

* Azure AD DS moet in dezelfde Azure-regio worden geïmplementeerd als uw virtuele netwerk.
    * Op dit moment kunt u slechts één door Azure AD DS beheerd domein implementeren per Azure AD-Tenant. Het beheerde domein van Azure AD DS wordt geïmplementeerd in een enkele regio. Zorg ervoor dat u een virtueel netwerk maakt of selecteert in een [regio die Azure-AD DS ondersteunt](https://azure.microsoft.com/global-infrastructure/services/?products=active-directory-ds&regions=all).
* Denk na over de nabijheid van andere Azure-regio's en de virtuele netwerken die als host fungeren voor de werk belasting van uw toepassing.
    * Als u de latentie wilt minimaliseren, houdt u uw belangrijkste toepassingen dicht bij of in dezelfde regio als het subnet van het virtuele netwerk voor uw Azure AD DS beheerde domein. U kunt peering van virtuele netwerken of VPN-verbindingen (virtueel particulier netwerk) tussen virtuele netwerken van Azure gebruiken.
* Het virtuele netwerk is niet afhankelijk van de DNS-services die door Azure AD DS worden verschaft.
    * Azure AD DS biedt een eigen DNS-service. Het virtuele netwerk moet worden geconfigureerd voor het gebruik van deze DNS-service adressen. Naam omzetting voor extra naam ruimten kan worden uitgevoerd met behulp van voorwaardelijke doorstuur servers.
    * U kunt geen aangepaste DNS-server instellingen gebruiken om query's te sturen van andere DNS-servers, inclusief op Vm's. Resources in het virtuele netwerk moeten gebruikmaken van de DNS-service van Azure AD DS.

> [!IMPORTANT]
> U kunt Azure-AD DS niet verplaatsen naar een ander virtueel netwerk nadat u de service hebt ingeschakeld.

Een door Azure AD DS beheerd domein maakt verbinding met een subnet in een virtueel Azure-netwerk. Ontwerp dit subnet voor Azure AD DS met de volgende overwegingen:

* Azure AD DS moet worden geïmplementeerd in een eigen subnet. Gebruik geen bestaand subnet of een gateway-subnet.
* Er wordt een netwerk beveiligings groep gemaakt tijdens de implementatie van een door Azure AD DS beheerd domein. Deze netwerk beveiligings groep bevat de vereiste regels voor de juiste service communicatie.
    * Maak of gebruik geen bestaande netwerk beveiligings groep met uw eigen aangepaste regels.
* Azure AD DS vereist 3-5 IP-adressen. Zorg ervoor dat het IP-adres bereik van uw subnet dit aantal adressen kan bevatten.
    * Het beperken van de beschik bare IP-adressen kan verhinderen dat Azure AD Domain Services twee domein controllers beheert.

In het volgende voorbeeld diagram wordt een geldig ontwerp beschreven waarbij Azure AD DS een eigen subnet heeft, er is een gateway-subnet voor externe verbinding en de werk belasting van toepassingen bevindt zich in een verbonden subnet in het virtuele netwerk:

![Aanbevolen subnet ontwerpen](./media/active-directory-domain-services-design-guide/vnet-subnet-design.png)

## <a name="connections-to-the-azure-ad-ds-virtual-network"></a>Verbindingen met het virtuele netwerk van Azure AD DS

Zoals u in de vorige sectie hebt genoteerd, kunt u alleen een door Azure AD Domain Services beheerd domein in één virtueel netwerk in azure maken en kan er slechts één beheerd domein worden gemaakt per Azure AD-Tenant. Op basis van deze architectuur moet u mogelijk een of meer virtuele netwerken koppelen die de werk belasting van uw toepassing hosten naar uw Azure AD DS virtuele netwerk.

U kunt met behulp van een van de volgende methoden toepassings werkbelastingen die worden gehost in andere virtuele netwerken van Azure verbinden:

* Virtual Network-peering
* VPN-verbinding (Virtual Private Network)

### <a name="virtual-network-peering"></a>Virtual Network peering

Peering op virtueel netwerk is een mechanisme dat twee virtuele netwerken in dezelfde regio verbindt via het Azure-backbone-netwerk. Wereld wijde virtuele netwerk peering kan virtueel netwerk verbinden tussen Azure-regio's. Als de twee virtuele netwerken zijn gekoppeld, kunnen resources, zoals virtuele machines, rechtstreeks communiceren met behulp van privé-IP-adressen. Met Virtual Network-peering kunt u een door Azure AD DS beheerd domein implementeren met uw toepassings werkbelastingen die in andere virtuele netwerken zijn geïmplementeerd.

![Verbinding met virtueel netwerk met behulp van peering](./media/active-directory-domain-services-design-guide/vnet-peering.png)

Zie [overzicht van Azure Virtual Network-peering](../virtual-network/virtual-network-peering-overview.md)voor meer informatie.

### <a name="virtual-private-networking"></a>Virtueel particulier netwerk

U kunt een virtueel netwerk verbinden met een ander virtueel netwerk (VNet-naar-VNet) op dezelfde manier als u een virtueel netwerk op een on-premises site locatie kunt configureren. Beide verbindingen gebruiken een VPN-gateway om een beveiligde tunnel met IPsec/IKE te maken. Met dit verbindings model kunt u Azure-AD DS implementeren in een virtueel Azure-netwerk en vervolgens on-premises locaties of andere Clouds verbinden.

![Verbinding met het virtuele netwerk met behulp van een VPN Gateway](./media/active-directory-domain-services-design-guide/vnet-connection-vpn-gateway.jpg)

Voor meer informatie over het gebruik van virtuele particuliere netwerken, Lees [een vnet-naar-vnet VPN-gateway verbinding configureren met behulp van de Azure Portal](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal).

## <a name="name-resolution-when-connecting-virtual-networks"></a>Naam omzetting bij het verbinden van virtuele netwerken

Virtuele netwerken die zijn verbonden met het virtuele Azure AD Domain Services-netwerk hebben doorgaans hun eigen DNS-instellingen. Wanneer u virtuele netwerken verbindt, wordt de naam omzetting voor het verbinding maken met het virtuele netwerk niet automatisch geconfigureerd om services die worden geboden door het door Azure AD DS beheerde domein, op te lossen. De naam omzetting van de virtuele netwerken die verbinding maken moet zo worden geconfigureerd dat werk belastingen van toepassingen Azure AD Domain Services kunnen vinden.

U kunt naam omzetting inschakelen met behulp van voorwaardelijke DNS-doorstuur servers op de DNS-server die de verbinding met virtuele netwerken ondersteunt, of door gebruik te maken van dezelfde DNS IP-adressen van het virtuele netwerk van de Azure AD-domein service.

## <a name="network-resources-used-by-azure-ad-ds"></a>Netwerk bronnen die worden gebruikt door Azure AD DS

Een door Azure AD DS beheerd domein maakt sommige netwerk bronnen tijdens de implementatie. Deze resources zijn nodig voor een geslaagde bewerking en beheer van het beheerde Azure AD DS-domein, en mogen niet hand matig worden geconfigureerd.

| Azure-resource                          | Beschrijving |
|:----------------------------------------|:---|
| Netwerk interface kaart                  | Azure AD DS fungeert als host voor het beheerde domein op twee domein controllers (Dc's) die worden uitgevoerd op Windows Server als Azure-Vm's. Elke VM heeft een virtuele netwerk interface die verbinding maakt met het subnet van het virtuele netwerk. |
| Dynamisch standaard openbaar IP-adres         | Azure AD DS communiceert met de synchronisatie-en beheer service met een standaard-SKU openbaar IP-adres. Zie [IP-adres typen en toewijzings methoden in azure](../virtual-network/virtual-network-ip-addresses-overview-arm.md)voor meer informatie over open bare IP-adressen. |
| Azure Standard load balancer               | Azure AD DS maakt gebruik van een standaard-SKU load balancer voor Network Address Translation (NAT) en taak verdeling (bij gebruik met beveiligde LDAP). Zie [Wat is Azure Load Balancer?](../load-balancer/load-balancer-overview.md) voor meer informatie over Azure load balancers. |
| NAT-regels (Network Address Translation) | Azure AD DS maakt en gebruikt drie NAT-regels voor de load balancer-één regel voor beveiligd HTTP-verkeer en twee regels voor beveiligde communicatie met Power shell. |
| Regels voor load balancers                     | Wanneer een beheerd domein van Azure AD DS is geconfigureerd voor beveiligde LDAP op TCP-poort 636, worden er drie regels gemaakt en gebruikt in een load balancer om het verkeer te distribueren. |

> [!WARNING]
> Verwijder geen van de netwerk resources die zijn gemaakt door Azure AD DS. Als u een van de netwerk bronnen verwijdert, vindt er een storing in de Azure AD DS-service plaats.

## <a name="network-security-groups-and-required-ports"></a>Netwerk beveiligings groepen en de vereiste poorten

Een [netwerk beveiligings groep (NSG)](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) bevat een lijst met regels waarmee netwerk verkeer naar verkeer in een virtueel Azure-netwerk wordt toegestaan of geweigerd. Er wordt een netwerk beveiligings groep gemaakt wanneer u Azure AD DS implementeert die een set regels bevat waarmee de service verificatie-en beheer functies kan bieden. Deze standaard netwerk beveiligings groep is gekoppeld aan het subnet van het virtuele netwerk waarop uw Azure AD DS beheerde domein is geïmplementeerd.

De volgende regels voor de netwerk beveiligings groep zijn vereist voor Azure AD DS om verificatie-en beheer services te bieden. Wijzig of verwijder deze regels voor netwerk beveiligings groepen niet voor het subnet van het virtuele netwerk waarop uw Azure AD DS beheerde domein is geïmplementeerd.

| Poortnummer | Protocol | Bron                             | Bestemming | Actie | Verplicht | Doel |
|:-----------:|:--------:|:----------------------------------:|:-----------:|:------:|:--------:|:--------|
| 443         | TCP      | AzureActiveDirectoryDomainServices | Alle         | Toestaan  | Ja      | Synchronisatie met uw Azure AD-Tenant. |
| 3389        | TCP      | CorpNetSaw                         | Alle         | Toestaan  | Ja      | Beheer van uw domein. |
| 5986        | TCP      | AzureActiveDirectoryDomainServices | Alle         | Toestaan  | Ja      | Beheer van uw domein. |
| 636         | TCP      | Alle                                | Alle         | Toestaan  | Nee       | Alleen ingeschakeld wanneer u Secure LDAP (LDAPS) configureert. |

> [!WARNING]
> Bewerk deze netwerk bronnen en configuraties niet hand matig. Wanneer u een onjuist geconfigureerde netwerk beveiligings groep of een door de gebruiker gedefinieerde route tabel koppelt aan het subnet waarin Azure AD DS is geïmplementeerd, kunt u de mogelijkheid van micro soft om het domein te onderhouden en te beheren, verstoren. De synchronisatie tussen uw Azure AD-Tenant en uw Azure AD DS beheerde domein wordt ook verstoord.
>
> De standaard regels voor *AllowVnetInBound*, *AllowAzureLoadBalancerInBound*, *DenyAllInBound*, *AllowVnetOutBound*, *AllowInternetOutBound*en *DenyAllOutBound* bestaan ook voor de netwerk beveiligings groep. Bewerk of verwijder deze standaard regels niet.
>
> De Azure SLA is niet van toepassing op implementaties waarbij een onjuist geconfigureerde netwerk beveiligings groep en/of door de gebruiker gedefinieerde route tabellen zijn toegepast waardoor Azure AD DS het bijwerken en beheren van uw domein blokkeert.

### <a name="port-443---synchronization-with-azure-ad"></a>Poort 443-synchronisatie met Azure AD

* Wordt gebruikt om uw Azure AD-Tenant te synchroniseren met uw door Azure AD DS beheerd domein.
* Als u geen toegang hebt tot deze poort, kan uw door Azure AD DS beheerde domein niet worden gesynchroniseerd met uw Azure AD-Tenant. Gebruikers kunnen zich mogelijk niet aanmelden als wijzigingen in hun wacht woord niet worden gesynchroniseerd met uw Azure AD DS beheerd domein.
* Inkomende toegang tot deze poort voor IP-adressen wordt standaard beperkt met behulp van de **AzureActiveDirectoryDomainServices** -service label.
* Beperk de uitgaande toegang niet vanaf deze poort.

### <a name="port-3389---management-using-remote-desktop"></a>Poort 3389-beheer met extern bureau blad

* Wordt gebruikt voor extern bureau blad-verbindingen met domein controllers in uw Azure AD DS beheerd domein.
* De standaard regel voor de netwerk beveiligings groep maakt gebruik van het *CorpNetSaw* -service label om het verkeer verder te beperken.
    * Met deze servicetag kunnen alleen beveiligde toegangs werkstations op het micro soft-bedrijfs netwerk worden gebruikt om extern bureau blad te gebruiken voor het door Azure AD DS beheerde domein.
    * Toegang is alleen toegestaan met zakelijke redenen, zoals voor beheer-of probleemoplossings scenario's.
* Deze regel kan worden ingesteld op *weigeren*en alleen ingesteld op *toestaan* wanneer dit nodig is. De meeste beheer-en bewakings taken worden uitgevoerd met externe communicatie met Power shell. RDP wordt alleen gebruikt in de zeldzame gebeurtenis die micro soft nodig heeft om extern verbinding te maken met uw beheerde domein voor geavanceerde probleem oplossing.

> [!NOTE]
> U kunt de *CorpNetSaw* -servicetag niet hand matig selecteren in de portal als u deze regel voor de netwerk beveiligings groep probeert te bewerken. U moet Azure PowerShell of de Azure CLI gebruiken om hand matig een regel te configureren die gebruikmaakt van het *CorpNetSaw* -service label.

### <a name="port-5986---management-using-powershell-remoting"></a>Poort 5986-beheer met externe communicatie van Power shell

* Wordt gebruikt voor het uitvoeren van beheer taken met externe communicatie van Power shell in uw Azure AD DS beheerde domein.
* Als u geen toegang hebt tot deze poort, kan uw door Azure AD DS beheerde domein niet worden bijgewerkt, geconfigureerd, ondersteund of gecontroleerd.
* Voor Azure AD DS beheerde domeinen die gebruikmaken van een virtueel netwerk op basis van Resource Manager, kunt u de inkomende toegang tot deze poort beperken tot het *AzureActiveDirectoryDomainServices* -service label.
    * Voor verouderde Azure AD DS beheerde domeinen die gebruikmaken van een klassiek virtueel netwerk, kunt u de inkomende toegang tot deze poort beperken tot de volgende bron-IP-adressen: *52.180.183.8*, *23.101.0.70*, *52.225.184.198*, *52.179.126.223*, *13.74.249.156*, *52.187.117.83*, *52.161.13.95*, *104.40.156.18*en *104.40.87.209*.

## <a name="user-defined-routes"></a>Door de gebruiker gedefinieerde routes

Door de gebruiker gedefinieerde routes worden niet standaard gemaakt en zijn niet vereist voor een juiste werking van Azure AD DS. Als u gebruik wilt maken van route tabellen, moet u geen wijzigingen aanbrengen in de *0.0.0.0* -route. Wijzigingen in deze route kunnen de Azure AD Domain Services verstoren.

U moet ook binnenkomend verkeer omleiden van de IP-adressen die zijn opgenomen in de respectieve Azure-service tags naar het Azure AD Domain Services subnet. Zie voor meer informatie over service tags en het bijbehorende IP-adres [Azure IP-bereiken en service Tags-open bare Cloud](https://www.microsoft.com/en-us/download/details.aspx?id=56519).

> [!CAUTION]
> Deze IP-bereiken van Azure-data centers kunnen zonder kennisgeving worden gewijzigd. Zorg ervoor dat u over processen beschikt om te controleren of u de meest recente IP-adressen hebt.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de volgende artikelen voor meer informatie over sommige netwerk bronnen en verbindings opties die worden gebruikt door Azure AD DS:

* [Peering van het virtuele netwerk van Azure](../virtual-network/virtual-network-peering-overview.md)
* [Azure VPN-gateways](../vpn-gateway/vpn-gateway-about-vpn-gateway-settings.md)
* [Azure-netwerk beveiligings groepen](../virtual-network/security-overview.md)

<!-- INTERNAL LINKS -->

<!-- EXTERNAL LINKS -->
