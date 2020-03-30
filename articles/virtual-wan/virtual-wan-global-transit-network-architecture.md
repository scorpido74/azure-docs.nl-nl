---
title: 'Architectuur: Wereldwijde architectuur van het transitnetwerk'
titleSuffix: Azure Virtual WAN
description: Meer informatie over wereldwijde transitnetwerkarchitectuur voor Virtual WAN
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: article
ms.date: 02/06/2020
ms.author: cherylmc
ms.openlocfilehash: 17d0e678008c76da32f20562aa795e83e49c80e4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77064968"
---
# <a name="global-transit-network-architecture-and-virtual-wan"></a>Wereldwijde transitnetwerkarchitectuur en Virtual WAN

Moderne ondernemingen vereisen alomtegenwoordige connectiviteit tussen hypergedistribueerde toepassingen, gegevens en gebruikers in de cloud en on-premises. Global transit network architecture wordt door bedrijven overgenomen om de cloud-centric moderne, wereldwijde ENTERPRISE IT-footprint te consolideren, te verbinden en te beheren.

De wereldwijde transitnetwerkarchitectuur is gebaseerd op een klassiek hub-and-spoke connectiviteitsmodel waarbij de cloud gehoste netwerkhub transitive connectiviteit mogelijk maakt tussen eindpunten die over verschillende soorten 'spaken' kunnen worden verdeeld.

In dit model kan een spaak zijn:
* Virtueel netwerk (VNets)
* Fysieke filiaalsite
* Externe gebruiker
* Internet

![hub en spaak](./media/virtual-wan-global-transit-network-architecture/figure1.png)

**Figuur 1: Global transit hub-and-spoke network**

Figuur 1 toont de logische weergave van het wereldwijde transitnetwerk waar geografisch verspreide gebruikers, fysieke sites en VNets met elkaar verbonden zijn via een netwerkhub die in de cloud wordt gehost. Deze architectuur maakt logische one-hop transit connectiviteit tussen de netwerkeindpunten mogelijk.

## <a name="global-transit-network-with-virtual-wan"></a><a name="globalnetworktransit"></a>Wereldwijd transitnetwerk met Virtual WAN

Azure Virtual WAN is een door Microsoft beheerde cloudnetwerkservice. Alle netwerkonderdelen waarvan deze service is samengesteld, worden gehost en beheerd door Microsoft. Zie het artikel [Virtual WAN Overview](virtual-wan-about.md) voor meer informatie over Virtual WAN.

Azure Virtual WAN maakt een wereldwijde transitnetwerkarchitectuur mogelijk door alomtegenwoordige, elke-to-any-connectiviteit tussen wereldwijd gedistribueerde sets cloudworkloads in VNets, branchsites, SaaS- en PaaS-toepassingen en gebruikers mogelijk te maken.

![Azure Virtual WAN](./media/virtual-wan-global-transit-network-architecture/figure2.png)

**Figuur 2: Wereldwijd transitnetwerk en Virtueel WAN**

In de Azure Virtual WAN-architectuur worden virtuele WAN-hubs ingericht in Azure-regio's, waarmee u kiezen om uw branches, VNets en externe gebruikers met elkaar te verbinden. De fysieke filiaalsites zijn verbonden met de hub via Premium ExpressRoute of site-to-site-VPN's, VNets zijn verbonden met de hub door VNet-verbindingen en externe gebruikers kunnen rechtstreeks verbinding maken met de hub via User VPN (point-to-site VPN's). Virtual WAN ondersteunt ook cross-region VNet-verbinding waarbij een VNet in één regio kan worden aangesloten op een virtuele WAN-hub in een andere regio.

U een virtueel WAN instellen door één virtuele WAN-hub in de regio te maken met het grootste aantal spaken (branches, VNets, gebruikers) en vervolgens de spaken in andere regio's met de hub te verbinden. Dit is een goede optie wanneer een enterprise footprint is meestal in een regio met een paar remote spaken.  
  
## <a name="hub-to-hub-connectivity"></a><a name="hubtohub"></a>Hub-to-hub-connectiviteit

Een Enterprise-cloudfootprint kan meerdere cloudregio's bestrijken en het is optimaal (latentie-gewijs) om toegang te krijgen tot de cloud vanuit een regio die het dichtst bij hun fysieke site en gebruikers staat. Een van de belangrijkste principes van de wereldwijde transitnetwerkarchitectuur is het mogelijk maken van cross-region connectiviteit tussen alle cloud- en on-premises netwerkeindpunten. Dit betekent dat verkeer van een filiaal dat is verbonden met de cloud in een regio een andere branch of een VNet in een andere regio kan bereiken met behulp van hub-to-hub-connectiviteit die is ingeschakeld door [Azure Global Network.](https://azure.microsoft.com/global-infrastructure/global-network/)

![regiooverschrijden](./media/virtual-wan-global-transit-network-architecture/figure3.png)

**Figuur 3: Virtuele WAN-connectiviteit tussen regio's**

Wanneer meerdere hubs zijn ingeschakeld in één virtueel WAN, worden de hubs automatisch met elkaar verbonden via hub-to-hub-koppelingen, waardoor wereldwijde connectiviteit mogelijk wordt tussen branches en Vnets die over meerdere regio's zijn verdeeld. 

Bovendien kunnen hubs die allemaal deel uitmaken van hetzelfde virtuele WAN, worden gekoppeld aan verschillende regionale toegangs- en beveiligingsbeleid. Zie [Besturingselement Beveiliging en beleid](#security) later in dit artikel voor meer informatie.

## <a name="any-to-any-connectivity"></a><a name="anytoany"></a>Elke-to-any-connectiviteit

Global transit netwerk architectuur maakt elke-to-any connectiviteit via virtuele WAN hubs. Deze architectuur elimineert of vermindert de noodzaak van volledige mesh- of gedeeltelijke mesh-connectiviteit tussen spaken, die complexer zijn om te bouwen en te onderhouden. Bovendien is routebeheer in hub-and-spoke vs. mesh-netwerken eenvoudiger te configureren en te onderhouden.

Elke-to-any-connectiviteit (in de context van een globale architectuur) stelt een onderneming met wereldwijd gedistribueerde gebruikers, vestigingen, datacenters, VNets en toepassingen in staat om verbinding met elkaar te maken via de 'transit'-hub(s). Azure Virtual WAN fungeert als het wereldwijde doorvoersysteem.

![elke aan een](./media/virtual-wan-global-transit-network-architecture/figure4.png)

**Figuur 4: Virtuele WAN-verkeerspaden**

Azure Virtual WAN ondersteunt de volgende globale transitconnectiviteitspaden. De letters tussen haakjes gaan naar figuur 4.

* Branch-to-VNet (a)
* Branch-to-branch (b)
  * ExpressRoute Global Reach en Virtual WAN
* Remote User-to-VNet (c)
* Remote User-to-branch (d)
* VNet-naar-VNet (e)
* Branch-to-hub-hub-to-Branch (f)
* Branch-to-hub-hub-to-VNet (g)
* VNet-naar-hub-hub-to-VNet (h)

### <a name="branch-to-vnet-a-and-branch-to-vnet-cross-region-g"></a>Branch-to-VNet (a) en Branch-to-VNet Cross-region (g)

Branch-to-VNet is het primaire pad dat wordt ondersteund door Azure Virtual WAN. Met dit pad u branches verbinden met Azure IAAS-bedrijfsworkloads die zijn geïmplementeerd in Azure VNets. Branches kunnen worden aangesloten op het virtuele WAN via ExpressRoute of site-to-site VPN. Het verkeer gaat door naar VNets die via VNet Connections zijn aangesloten op de virtuele WAN-hubs. Expliciete [gatewaytransit](../virtual-network/virtual-network-peering-overview.md#gateways-and-on-premises-connectivity) is niet vereist voor Virtual WAN omdat Virtual WAN automatisch gatewaytransit naar branchsite inschakelt. Zie het artikel [van Virtual WAN Partners](virtual-wan-configure-automation-providers.md) over het aansluiten van een SD-WAN CPE op Virtual WAN.

### <a name="expressroute-global-reach-and-virtual-wan"></a>ExpressRoute Global Reach en Virtual WAN

ExpressRoute is een privé- en veerkrachtige manier om uw on-premises netwerken aan te sluiten op de Microsoft Cloud. Virtual WAN ondersteunt Express Route-circuitverbindingen. Het aansluiten van een filiaal site om Virtual WAN met Express Route vereist 1) Premium Circuit 2) Circuit te worden in een Global Reach ingeschakeld locatie.

ExpressRoute Global Reach is een add-on functie voor ExpressRoute. Met Global Reach u ExpressRoute-circuits met elkaar verbinden om een privénetwerk te maken tussen uw on-premises netwerken. Branches die zijn verbonden met Azure Virtual WAN via ExpressRoute vereisen dat expressroute global reach met elkaar communiceert.

In dit model kan elke tak die is verbonden met de virtuele WAN-hub via ExpressRoute verbinding maken met VNets via het branch-to-VNet-pad. Branch-to-branch verkeer zal de hub niet doorsturen omdat ExpressRoute Global Reach een beter pad over Azure WAN mogelijk maakt.

### <a name="branch-to-branch-b-and-branch-to-branch-cross-region-f"></a>Branch-to-branch (b) en Branch-to-Branch cross-region (f)

Branches kunnen worden verbonden met een Azure virtuele WAN-hub via ExpressRoute-circuits en/of site-to-site VPN-verbindingen. U de vertakkingen verbinden met de virtuele WAN-hub in de regio die het dichtst bij de vertakking ligt.

Met deze optie kunnen bedrijven de Azure-backbone gebruiken om branches met elkaar te verbinden. Hoewel deze mogelijkheid beschikbaar is, moet u de voordelen van het verbinden van branches via Azure Virtual WAN wegen vs. het gebruik van een eigen WAN.  

### <a name="remote-user-to-vnet-c"></a>Remote User-to-VNet (c)

U directe, beveiligde externe toegang tot Azure inschakelen via point-to-site-verbinding van een externe gebruikersclient naar een virtueel WAN. Zakelijke externe gebruikers hoeven niet langer met een zakelijke VPN aan de cloud te worden vastgemaakt.

### <a name="remote-user-to-branch-d"></a>Remote User-to-branch (d)

Met het pad van Remote User-to-branch kunnen externe gebruikers die een point-to-site-verbinding met on-premises workloads en toepassingen van Azure gebruiken, worden geopend door door de cloud te reizen. Dit pad biedt externe gebruikers de flexibiliteit om toegang te krijgen tot workloads die zowel in Azure als on-premises zijn geïmplementeerd. Ondernemingen kunnen centrale cloudgebaseerde beveiligde externe toegangsservice inschakelen in Azure Virtual WAN.

### <a name="vnet-to-vnet-transit-e-and-vnet-to-vnet-cross-region-h"></a>VNet-naar-VNet transit (e) en VNet-naar-VNet cross-region (h)

Met de VNet-to-VNet-transit kunnen VNets met elkaar worden verbonden om multi-tier applicaties die worden geïmplementeerd op meerdere VNets met elkaar te verbinden. Optioneel u VNets met elkaar verbinden via VNet Peering en dit kan geschikt zijn voor sommige scenario's waarbij doorvoer via de VWAN-hub niet nodig is.

## <a name="security-and-policy-control"></a><a name="security"></a>Veiligheids- en beleidscontrole

De Azure Virtual WAN-hubs verbinden alle netwerkeindpunten over het hybride netwerk en kunnen mogelijk al het transitnetwerkverkeer zien. Virtuele WAN-hubs kunnen worden geconverteerd naar beveiligde virtuele hubs door de Azure Firewall te implementeren in VWAN-hubs om cloudgebaseerde beveiliging, toegang en beleidsbeheer mogelijk te maken. Orkestratie van Azure Firewalls in virtuele WAN-hubs kan worden uitgevoerd door Azure Firewall Manager.

[Azure Firewall Manager](https://go.microsoft.com/fwlink/?linkid=2107683) biedt de mogelijkheden om beveiliging voor wereldwijde transitnetwerken te beheren en te schalen. Azure Firewall Manager biedt de mogelijkheid om routering, wereldwijd beleidsbeheer, geavanceerde internetbeveiligingsservices centraal te beheren via derden, samen met de Azure Firewall.

![beveiligde virtuele hub met Azure Firewall](./media/virtual-wan-global-transit-network-architecture/figure5.png)

**Figuur 5: Beveiligde virtuele hub met Azure Firewall**

Azure Firewall naar het virtuele WAN ondersteunt de volgende globale beveiligde transitconnectiviteitspaden. De letters tussen haakjes gaan naar figuur 5.

* VNet-naar-VNet beveiligde doorvoer (e)
* VNet-to-Internet of third-party Security Service (i)
* Branch-to-Internet of third-party Security Service (j)

### <a name="vnet-to-vnet-secured-transit-e"></a>VNet-naar-VNet beveiligde doorvoer (e)

Met de VNet-naar-VNet beveiligde transit kunnen VNets verbinding met elkaar maken via de Azure Firewall in de virtuele WAN-hub.

### <a name="vnet-to-internet-or-third-party-security-service-i"></a>VNet-to-Internet of third-party Security Service (i)

Met de VNet-to-Internet of door derden beveiligde doorvoer kunnen VNets verbinding maken met internet of een ondersteunde beveiligingsservices van derden via de Azure Firewall in de virtuele WAN-hub.

### <a name="branch-to-internet-or-third-party-security-service-j"></a>Branch-to-Internet of third-party Security Service (j)
Met de branch-to-internet of secure transit van derden kunnen branches verbinding maken met internet of een ondersteunde beveiligingsservices van derden via de Azure Firewall in de virtuele WAN-hub.

## <a name="next-steps"></a>Volgende stappen

Maak een verbinding met Virtual WAN en Implementeer Azure Firewall in VWAN-hub(s).

* [Site-to-site-verbindingen met Virtual WAN](virtual-wan-site-to-site-portal.md)
* [ExpressRoute-verbindingen met Virtual WAN](virtual-wan-expressroute-portal.md)
* [Azure Firewall Manager om Azure FW in VWAN te implementeren](https://go.microsoft.com/fwlink/?linkid=2107683)
