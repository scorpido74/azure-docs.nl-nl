---
title: Netwerkoverwegingen - Azure Dedicated HSM | Microsoft Documenten
description: Overzicht van netwerkoverwegingen die van toepassing zijn op Azure Dedicated HSM-implementaties
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "70881282"
---
# <a name="azure-dedicated-hsm-networking"></a>Azure Dedicated HSM-netwerken

Azure Dedicated HSM vereist een zeer veilige netwerkomgeving. Dit geldt of het nu van de Azure-cloud terug gaat naar de IT-omgeving van de klant (on-premises), met behulp van gedistribueerde toepassingen of voor scenario's met hoge beschikbaarheid. Azure Networking biedt dit en er zijn vier verschillende gebieden die moeten worden aangepakt.

- HSM-apparaten maken in uw Virtual Network (VNet) in Azure
- On-premises verbinding maken met cloudbronnen voor de configuratie en het beheer van HSM-apparaten
- Virtuele netwerken maken en verbinden voor het onderling verbinden van applicatiebronnen en HSM-apparaten
- Virtuele netwerken tussen regio's verbinden voor intercommunicatie en ook om scenario's met hoge beschikbaarheid mogelijk te maken

## <a name="virtual-network-for-your-dedicated-hsms"></a>Virtueel netwerk voor uw dedicated HSM's

Speciale HSM's worden geïntegreerd in een virtueel netwerk en in het eigen privénetwerk van klanten in Azure geplaatst. Dit maakt toegang tot de apparaten mogelijk via virtuele machines of rekenbronnen in het virtuele netwerk.  
Zie [Virtual network for Azure services](../virtual-network/virtual-network-for-azure-services.md) documentation voor meer informatie over het integreren van Azure-services in het virtuele netwerk en de mogelijkheden die het biedt.

### <a name="virtual-networks"></a>Virtuele netwerken

Voordat ze een dedicated HSM-apparaat inrichten, moeten klanten eerst een virtueel netwerk in Azure maken of een netwerk gebruiken dat al bestaat in het klantenabonnement. Het virtuele netwerk definieert de beveiligingsperimeter voor het dedicated HSM-apparaat. Zie [virtuele netwerkdocumentatie](../virtual-network/virtual-networks-overview.md)voor meer informatie over het maken van virtuele netwerken.

### <a name="subnets"></a>Subnetten

Subnetten segmenteren het virtuele netwerk in afzonderlijke adresruimten die kunnen worden gebruikt door de Azure-resources die u erin plaatst. Speciale HSM's worden geïmplementeerd in een subnet in het virtuele netwerk. Elk specifiek HSM-apparaat dat in het subnet van de klant wordt geïmplementeerd, ontvangt een privé-IP-adres van dit subnet. Het subnet waarin het HSM-apparaat wordt geïmplementeerd, moet expliciet worden gedelegeerd aan de service: Microsoft.HardwareSecurityModules/dedicatedHSMs. Hiermee worden bepaalde machtigingen verleend aan de HSM-service voor implementatie in het subnet. Delegatie naar Dedicated HSM's legt bepaalde beleidsbeperkingen op aan het subnet. Netwerkbeveiligingsgroepen (NSG's) en door gebruikers gedefinieerde routes (UDR's) worden momenteel niet ondersteund op gedelegeerde subnetten. Als gevolg hiervan kan het subnet alleen worden gebruikt om HSM-resources te implementeren zodra een subnet is gedelegeerd aan speciale HSM's. De implementatie van andere klantbronnen in het subnet mislukt.


### <a name="expressroute-gateway"></a>ExpressRoute-gateway

Een vereiste van de huidige architectuur is de configuratie van een ER-gateway in het subnet van de klant, waarbij een HSM-apparaat moet worden geplaatst om de integratie van het HSM-apparaat in Azure mogelijk te maken. Deze ER-gateway kan niet worden gebruikt voor het verbinden van on-premises locaties met de HSM-apparaten van klanten in Azure.

## <a name="connecting-your-on-premises-it-to-azure"></a>Uw on-premises IT verbinden met Azure

Bij het maken van cloudgebaseerde resources is dit een typische vereiste voor een privéverbinding terug naar on-premises IT-resources. In het geval van Dedicated HSM is dit voornamelijk aan de HSM-clientsoftware om de HSM-apparaten te configureren en ook voor activiteiten zoals back-ups en het ophalen van logboeken van HSM's voor analyse. Een belangrijk beslissingspunt hier is de aard van de verbinding als er opties zijn.  De meest flexibele optie is Site-to-Site VPN, omdat er waarschijnlijk meerdere on-premises bronnen zijn die veilige communicatie met resources (inclusief HSM's) in de Azure-cloud vereisen. Hiervoor moet een klantorganisatie een VPN-apparaat hebben om de verbinding te vergemakkelijken. Een Point-to-Site VPN-verbinding kan worden gebruikt als er slechts één eindpunt on-premises is, zoals één administratiewerkstation.
Zie [VPN Gateway-planningsopties](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#planningtable)voor meer informatie over connectiviteitsopties.

> [!NOTE]
> Op dit moment is ExpressRoute geen optie voor aansluiting op on-premises resources. Er zij ook op gewezen dat de ExpressRoute Gateway die wordt gebruikt zoals hierboven beschreven, niet is bedoeld voor verbindingen met on-premises infrastructuur.

### <a name="point-to-site-vpn"></a>Punt-naar-site-VPN

Een point-to-site Virtual Private Network is de eenvoudigste vorm van beveiligde verbinding met één eindpunt on-premises. Dit kan relevant zijn als u slechts één beheerwerkstation voor speciale HSM's op Azure wilt hebben.

### <a name="site-to-site-vpn"></a>Site-naar-site-VPN

Een site-to-site Virtual Private Network maakt veilige communicatie mogelijk tussen azure-gebaseerde dedicated HSM's en uw on-premises IT. Een reden om dit te doen is het hebben van een back-up faciliteit voor de HSM's on-premises en die een verbinding tussen de twee voor het uitvoeren van de back-up.

## <a name="connecting-virtual-networks"></a>Virtuele netwerken verbinden

Een typische implementatiearchitectuur voor Dedicated HSM begint met één virtueel netwerk en bijbehorend subnet waarin de HSM-apparaten worden gemaakt en ingericht. Binnen diezelfde regio zouden er wel eens extra virtuele netwerken en subnetten voor toepassingscomponenten kunnen zijn die gebruik zouden maken van de Dedicated HSM. Om communicatie tussen deze netwerken mogelijk te maken, maken we gebruik van Virtual Network Peering.

### <a name="virtual-network-peering"></a>Peering op virtueel netwerk

Wanneer er meerdere virtuele netwerken binnen een regio zijn die toegang nodig hebben tot elkaars bronnen, kan Virtual Network Peering worden gebruikt om veilige communicatiekanalen tussen deze netwerken te maken.  Virtuele netwerkpeering biedt niet alleen veilige communicatie, maar zorgt ook voor een verbindingen met een lage latentie en een hoge bandbreedte tussen de resources in Azure.

![netwerkpeering](media/networking/peering.png)

## <a name="connecting-across-azure-regions"></a>Verbinding maken in Azure-regio's

De HSM-apparaten hebben de mogelijkheid om via softwarebibliotheken verkeer om te leiden naar een alternatieve HSM. Verkeersomleiding is handig als apparaten uitvallen of de toegang tot een apparaat verloren gaat. Scenario's voor het mislukken op regionaal niveau kunnen worden beperkt door hs-m's in andere regio's in te zetten en communicatie tussen virtuele netwerken tussen regio's mogelijk te maken.

### <a name="cross-region-ha-using-vpn-gateway"></a>Cross region HA met VPN-gateway

Voor wereldwijd gedistribueerde toepassingen of voor regionale failoverscenario's met hoge beschikbaarheid is het vereist om virtuele netwerken tussen regio's met elkaar te verbinden. Met Azure Dedicated HSM kan hoge beschikbaarheid worden bereikt door gebruik te maken van een VPN-gateway die een veilige tunnel tussen de twee virtuele netwerken biedt. Zie het artikel getiteld Wat is VPN Gateway voor meer informatie over Vnet-naar-Vnet-verbindingen met VPN [Gateway?](../vpn-gateway/vpn-gateway-about-vpngateways.md#V2V)

> [!NOTE]
> Global Vnet-peering is op dit moment niet beschikbaar in connectiviteitsscenario's met speciale HSM's en VPN-gateway moet in plaats daarvan worden gebruikt. 

![global-vnet](media/networking/global-vnet.png)

## <a name="next-steps"></a>Volgende stappen

- [Veelgestelde vragen](faq.md)
- [Ondersteuning](supportability.md)
- [Hoge beschikbaarheid](high-availability.md)
- [Fysieke beveiliging](physical-security.md)
- [Monitoring](monitoring.md)
- [Implementatiearchitectuur](deployment-architecture.md)