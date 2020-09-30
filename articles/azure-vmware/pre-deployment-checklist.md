---
title: Controle lijst voor pre-implementatie van Azure VMware-oplossing
description: Gebruik deze controle lijst als onderdeel van het plannings proces vóór de implementatie.
ms.topic: how-to
ms.date: 10/02/2020
ms.openlocfilehash: 51480f72d6de6b1f49aa753e006c4683bbd48cc7
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91579766"
---
# <a name="azure-vmware-solution-pre-deployment-checklist"></a>Controle lijst voor pre-implementatie van Azure VMware-oplossing
Tijdens de [plannings fase](production-ready-deployment-steps.md)gebruikt u deze controle lijst voor de implementatie van de voor bereiding.

## <a name="success-criteria"></a>Criteria voor geslaagde pogingen
Definieer welke tests u wilt uitvoeren en de verwachte uitkomst.

| Benodigde basis informatie | Uw reactie |
| ----------- | ------------- |
| SDDC implementeren | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Virtueel netwerk maken | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Jump Box maken | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Virtuele netwerk gateway maken | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| ExpressRoute verbinden Global Reach | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| Aanmelden bij NSX Manager en vCenter | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



| Primaire informatie vereist | Uw reactie |
| --------| --------------|
| DHCP-server maken | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Netwerk segmenten maken | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Schalen (knoop punten toevoegen of verwijderen) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| VMware-HCX implementeren | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Virtuele machines maken (Vm's) | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;
| Internet inschakelen | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| VM-migratie van on-premises naar privécloud | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



| Aanvullende informatie die nodig is | Uw reactie |
| --------| --------------|
| VM-momentopname bewerkingen | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| NSX-T implementeren load balancer | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Azure-integratie<br><ul><li>Gedeelde inhouds bibliotheek</li><li>Beveiligings integratie</li><li>ISO uploaden</li><li>Bouwen vanuit ISO</li><li>Azure Backup</li></ul> | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Micro segmentatie | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Routering | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |




## <a name="azure-vmware-solution-information"></a>Informatie over de Azure VMware-oplossing

#### <a name="azure-subscription"></a>Azure-abonnement
Geef de naam van het abonnement en de abonnements-ID op voor de Azure VMware-oplossing. Het abonnement kan nieuw of bestaand abonnement zijn. Maak geen gebruik van een dev/test-abonnement.

| Informatie die nodig is  | Uw reactie |
| ------------| ------------- |
| Abonnement en ID | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Een abonnement met een EA | ☐ Ja &nbsp; &nbsp; ☐ Nee  |
| Naam resourcegroep | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Locatie | ☒ VS-Oost |
| Azure-beheerder<br><br>Geef de naam en de contact persoon van de beheerder op<br>wordt toegewezen om de service in te scha kelen voor Marketplace.<br>Inzender is de minimale rol vereist voor <br>[Registreer de resource provider van de Azure VMware-oplossing](tutorial-create-private-cloud.md#register-the-resource-provider). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="on-premises-vmware-information"></a>On-premises VMware-informatie

| Informatie die nodig is  | Uw reactie |
| ------------| --------------|
| vSphere-versie | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| vCenter-versie | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| vCenter-beheerder | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| L2-extensie<br><br>Bij het uitbreiden van L2-netwerken met VMware HCX,<br>Geef het on-premises netwerk op dat u wilt uitbreiden. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; | 
| vSwitch<br><br>Geef het type vSwitch op dat in de hele omgeving wordt gebruikt. | ☐ Standard &nbsp; &nbsp; ☐ gedistribueerd<br><br>Als u standaard gebruikt, is VMware HCX niet beschikbaar. |
| DNS en DHCP<br><br>Er is een juiste DNS-en DHCP-infra structuur vereist. <br>Het wordt aanbevolen de DHCP-service te gebruiken die is ingebouwd in <br>NSX of gebruiken van een lokale DHCP-server in de privécloud <br>route ring van DHCP-verkeer in plaats van via de <br>WAN terug naar on-premises. Meer informatie <br>Zie [DHCP in azure VMware-oplossing maken en beheren](manage-dhcp.md). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |


 

## <a name="networking---azure-vmware-solution-infrastructure"></a>Netwerken-Azure VMware-oplossings infrastructuur 
De benodigde gegevens zijn om u te helpen te voldoen aan de vereisten voor de Azure VMware-oplossings netwerken voor de standaard-en test omgeving. 

| Informatie die nodig is | Uw reactie |
| ----------- | ------------- |
|Azure VMware-oplossing CIDR<br><br>Vereist voor vSphere-hosts,-vSAN en-beheer <br>netwerken in azure VMware-oplossing. Voor meer <br>informatie, Zie [route ring en aandachtspunten](tutorial-network-checklist.md#routing-and-subnet-considerations)voor het subnet. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| CIDR-workload van Azure VMware-oplossing (optioneel)<br><br>Een netwerk toewijzen dat moet worden gebruikt in azure VMware<br>Oplossing voor eerste test doeleinden. Virtuele machines<br>wordt geïmplementeerd om de netwerk verbinding te valideren <br>van/naar Azure VMware-oplossing. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="networking---connect-azure-vmware-solution-to-azure-virtual-network"></a>Netwerken: Azure VMware-oplossing verbinden met Azure Virtual Network
De gegevens die nodig zijn voor het opruimen van het Azure VMware-oplossings cluster, kunnen worden verbonden met Azure via een ExpressRoute-circuit, dat deel uitmaakt van de Azure VMware-oplossings service.

| Informatie die nodig is | Uw reactie |
| ------------------ | ------------- |
| Jump Box-Windows Server | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Virtueel netwerk maken | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Gateway-subnet maken | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Virtuele netwerk gateway maken<br><br>Zie [de gateway van het virtuele netwerk maken](../expressroute/expressroute-howto-add-gateway-portal-resource-manager.md#create-the-virtual-network-gateway)voor meer informatie. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |


 

## <a name="networking---connect-azure-vmware-solution-to-an-on-premises-datacenter"></a>Netwerken: Azure VMware-oplossing verbinden met een on-premises Data Center

| Informatie die nodig is | Uw reactie |
| ------------------ | ------------- |
| ExpressRoute-peering CIDR<br><br>Het `/29` CIDR-adres blok. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| ExpressRoute-autorisatie sleutel en Resource-ID<br><br>Geef een autorisatie sleutel en een resource-ID op <br>die is gegenereerd op basis van de huidige ExpressRoute <br>circuit dat verbinding maakt met het on-premises Data Center.  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Richting van standaard route<br><br>Moeten de virtuele machines in de Azure VMware-oplossing <br>toegang tot internet via de Azure VMware-oplossing die is meegeleverd <br>Internet of kom terug over het ExpressRoute-circuit om <br>on-premises voor de standaard route? | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| Netwerkpoorten die nodig zijn om te communiceren met de service<br><br>Zie [vereiste netwerk poorten](tutorial-network-checklist.md#required-network-ports)voor meer informatie. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |



## <a name="networking---vmware-hcx"></a>Netwerken-VMware-HCX

| Informatie die nodig is | Uw reactie |
| ------------------ | ------------- |
| Netwerkpoorten<br><br>Als er een firewall is, controleert u of de vereiste netwerk poorten <br>worden geopend tussen on-premises en Azure VMware-oplossingen. | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| DNS<br><br>Voor informatie over het configureren van DNS, <br>Zie [netwerken-Azure VMware-oplossings infrastructuur](#networking---azure-vmware-solution-infrastructure). | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |
| VMware HCX-CIDRs<br><br>U hebt twee `/29` CIDR-blokken nodig die worden gebruikt voor <br>de onderdelen van de VMware HCX-infra structuur on-premises.  | &nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp; |

