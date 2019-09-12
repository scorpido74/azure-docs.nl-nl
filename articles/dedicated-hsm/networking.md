---
title: Aandachtspunten voor netwerken - Azure toegewezen HSM | Microsoft Docs
description: Overzicht van netwerken van overwegingen die van toepassing op Azure toegewezen HSM-implementaties
services: dedicated-hsm
author: msmbaldwin
manager: rkarlin
ms.custom: mvc, seodec18
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: mbaldwin
ms.openlocfilehash: 044930c9df7b54515b9b66426a6b05aa9517a3a1
ms.sourcegitcommit: 7c5a2a3068e5330b77f3c6738d6de1e03d3c3b7d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/11/2019
ms.locfileid: "70881282"
---
# <a name="azure-dedicated-hsm-networking"></a>Azure toegewezen HSM-netwerken

Azure toegewezen HSM is een zeer veilige netwerkomgeving vereist. Dit is van toepassing of het is met de Azure-cloud terug naar de klant IT-omgeving (on-premises) met behulp van gedistribueerde toepassingen of voor scenario's voor hoge beschikbaarheid. Azure-netwerk bieden dit en er zijn vier belangrijke gebieden die moeten worden opgelost.

- HSM-apparaten in uw Virtueelnetwerk (VNet) maken in Azure
- On-premises verbinding te maken met cloudresources voor de configuratie en beheer van de HSM-apparaten
- Het maken en verbinden van virtuele netwerken voor tussen verbindende toepassingsresources en HSM-apparaten
- Verbinden van virtuele netwerken tussen regio's voor de communicatie tussen en scenario's voor hoge beschikbaarheid inschakelen

## <a name="virtual-network-for-your-dedicated-hsms"></a>Virtueel netwerk voor uw door de toegewezen HSM 's

Toegewezen HSM's zijn geïntegreerd in een Virtueelnetwerk en geplaatst in de eigen klanten-privénetwerk in Azure. Dit kan toegang worden ingeschakeld op de apparaten van virtuele machines of compute-resources in het virtuele netwerk.  
Zie voor meer informatie over het integreren van Azure-services in het virtuele netwerk en de mogelijkheden die het biedt [virtueel netwerk voor Azure-services](../virtual-network/virtual-network-for-azure-services.md) documentatie.

### <a name="virtual-networks"></a>Virtuele netwerken

Voordat u een toegewezen HSM-apparaat inricht, moet klanten eerst naar een Virtueelnetwerk maken in Azure of te gebruiken dat al in het abonnement klanten bestaat. Het virtuele netwerk definieert de beveiligingsperimeter-voor de toegewezen HSM-apparaat. Zie voor meer informatie over het maken van virtuele netwerken [documentatie voor virtual network](../virtual-network/virtual-networks-overview.md).

### <a name="subnets"></a>Subnetten

Subnetten voor het segmenteren van het virtuele netwerk in afzonderlijke adresruimten die worden gebruikt door de Azure-resources die u erin plaatst. Toegewezen HSM's zijn geïmplementeerd in een subnet in het virtuele netwerk. Elk toegewezen HSM-apparaat dat is geïmplementeerd in het subnet van de klant ontvangt een privé IP-adres van dit subnet. Het subnet waarin het HSM-apparaat wordt geïmplementeerd, moet expliciet worden gedelegeerd naar de service: Microsoft.HardwareSecurityModules/dedicatedHSMs. Hierbij verleend bepaalde machtigingen voor de HSM-service voor implementatie in het subnet. Overdracht naar toegewezen HSM's legt bepaalde beperkingen-beleid op het subnet. Netwerkbeveiligingsgroepen (nsg's) en door de gebruiker gedefinieerde Routes (udr's) worden momenteel niet ondersteund op subnetten zijn overgedragen. Als gevolg hiervan zodra een subnet wordt overgedragen naar toegewezen HSM's, kan alleen worden gebruikt om HSM-resources te implementeren. Mislukt de implementatie van alle andere resources van de klant in het subnet.


### <a name="expressroute-gateway"></a>ExpressRoute-gateway

Een vereiste voor de huidige architectuur is de configuratie van een gateway ER in het subnet klanten waar een HSM-apparaat moet worden geplaatst om in te schakelen van de integratie van de HSM-apparaat in Azure. Deze gateway ER worden niet gebruikt om verbinding te maken van on-premises locaties op de klanten HSM-apparaten in Azure.

## <a name="connecting-your-on-premises-it-to-azure"></a>Verbinding van uw on-premises IT naar Azure

Bij het maken van cloud-gebaseerde bronnen, is een typische vereiste voor een particuliere verbinding naar on-premises IT-resources. In het geval van toegewezen HSM, wordt dit voornamelijk zijn voor de clientsoftware van de HSM om de HSM-apparaten te configureren en ook voor activiteiten, zoals back-ups en logboeken ophalen uit HSM's voor analyse. Een belangrijke beslissing hier is de aard van de verbinding, omdat er opties zijn.  De meest flexibele optie is Site-naar-Site VPN, omdat er waarschijnlijk meerdere on-premises bronnen waarvoor veilige communicatie met resources (met inbegrip van HSM's) in de Azure-cloud. Hiervoor moet de organisatie van een klant een VPN-apparaat om de verbinding mogelijk te maken hebben. Een punt-naar-Site VPN-verbinding kan worden gebruikt als er on-premises, zoals een werkstation voor beheer van één alleen een enkel eindpunt wordt.
Zie voor meer informatie over opties voor netwerkconnectiviteit, [VPN-Gateway opties](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable).

> [!NOTE]
> ExpressRoute is op dit moment geen een optie voor verbinding met on-premises bronnen. Er moet ook worden opgemerkt dat de ExpressRoute-Gateway gebruikt, zoals hierboven beschreven, is niet voor verbindingen met on-premises infrastructuur.

### <a name="point-to-site-vpn"></a>Punt-naar-Site-VPN

Een punt-naar-site virtueel particulier netwerk is de eenvoudigste vorm van een beveiligde verbinding met één eindpunt on-premises. Dit komt mogelijk relevant als u van plan bent te hebben alleen een werkstation voor beheer van één toegewezen HSM's op basis van Azure.

### <a name="site-to-site-vpn"></a>Site-to-Site VPN

Een site-naar-site virtueel particulier netwerk kunnen voor veilige communicatie tussen toegewezen HSM's op basis van Azure en uw on-premises IT. Een reden hiervoor is dat er een back-upfaciliteit is voor de on-premises HSM en dat er een verbinding nodig is tussen de twee voor het uitvoeren van de back-up.

## <a name="connecting-virtual-networks"></a>Virtuele netwerken verbinden

Een typische implementatiearchitectuur voor toegewezen HSM wordt gestart met een één virtueel netwerk en de bijbehorende subnet waarin de HSM-apparaten worden gemaakt en ingericht. Binnen die dezelfde regio kan ook zijn er aanvullende virtuele netwerken en subnetten voor onderdelen die zou maken gebruik van de toegewezen HSM. Voor het inschakelen van de communicatie tussen deze netwerken, gebruiken we de Peering in virtuele netwerken.

### <a name="virtual-network-peering"></a>Peering op virtueel netwerk

Wanneer er meerdere virtuele netwerken in een regio die u nodig hebt voor toegang tot elkaars bronnen, kan Peering in virtuele netwerken worden gebruikt om kanalen voor veilige communicatie tussen deze te maken.  Peering op virtueel netwerk biedt niet alleen beveiligde communicatie, maar zorgt er ook voor een lage latentie en hoge bandbreedte verbindingen tussen de resources in Azure.

![Netwerkpeering](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Verbinding maken tussen Azure-regio 's

De HSM-apparaten hebben de mogelijkheid via software-bibliotheken, verkeer omleiden naar een alternatieve HSM. Omleiden van verkeer is handig als apparaten niet of toegang tot een apparaat verloren gegaan is. Regionaal niveau scenario's kunnen worden opgelost door het implementeren van HSM's in andere regio's en communicatie tussen virtuele netwerken tussen regio's in te schakelen.

### <a name="cross-region-ha-using-vpn-gateway"></a>Interregionaal HA met behulp van VPN-gateway

Wereldwijd gedistribueerde toepassingen of voor hoge beschikbaarheid regionale failover-scenario's, is het vereist voor het verbinding maken met virtuele netwerken tussen regio's. Met Azure toegewezen HSM, kan hoge beschikbaarheid worden bereikt via een VPN-Gateway waarmee een beveiligde tunnel tussen de twee virtuele netwerken. Voor meer informatie over Vnet-naar-Vnet-verbindingen met behulp van VPN-Gateway, Zie het artikel [wat is VPN-Gateway?](../vpn-gateway/vpn-gateway-about-vpngateways.md#V2V)

> [!NOTE]
> Wereldwijde Vnet-peering is niet beschikbaar in de regio-overschrijdende verbinding scenario's met toegewezen HSM's op dit moment en de VPN-gateway in plaats daarvan moeten worden gebruikt. 

![globale vnet](media/networking/global-vnet.png)

## <a name="next-steps"></a>Volgende stappen

- [Veelgestelde vragen](faq.md)
- [Ondersteuning](supportability.md)
- [Hoge beschikbaarheid](high-availability.md)
- [Fysieke beveiliging](physical-security.md)
- [Controle](monitoring.md)
- [Implementatie-architectuur](deployment-architecture.md)