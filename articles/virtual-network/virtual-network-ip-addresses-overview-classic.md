---
title: IP-adres typen in azure (klassiek)
titlesuffix: Azure Virtual Network
description: Meer informatie over open bare en privé-IP-adressen (klassiek) in Azure.
services: virtual-network
documentationcenter: na
author: genlin
manager: dcscontentpm
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/11/2016
ms.author: genli
ms.openlocfilehash: 207e728d25df9192f8a600b13d86330af8311700
ms.sourcegitcommit: ca359c0c2dd7a0229f73ba11a690e3384d198f40
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/17/2019
ms.locfileid: "71058927"
---
# <a name="ip-address-types-and-allocation-methods-classic-in-azure"></a>IP-adres typen en toewijzings methoden (klassiek) in azure
U kunt IP-adressen toewijzen aan Azure-resources om te communiceren met andere Azure-resources, uw on-premises netwerk en internet. Er zijn twee soorten IP-adressen die u in azure kunt gebruiken: openbaar en privé.

Open bare IP-adressen worden gebruikt voor communicatie met internet, met inbegrip van open bare services van Azure.

Privé-IP-adressen worden gebruikt voor communicatie binnen een virtueel Azure-netwerk (VNet), een Cloud service en uw on-premises netwerk wanneer u een VPN-gateway of ExpressRoute-circuit gebruikt om uw netwerk uit te breiden naar Azure.

> [!IMPORTANT]
> Azure heeft twee verschillende implementatiemodellen voor het maken van en werken met resources:  [Resource Manager en het klassieke model](../resource-manager-deployment-model.md).  Dit artikel gaat over het gebruik van het klassieke implementatiemodel. Micro soft raadt aan de meeste nieuwe implementaties Resource Manager te gebruiken. Lees het artikel [IP-adressen](virtual-network-ip-addresses-overview-arm.md) in Resource Manager voor meer informatie over IP-adressen.

## <a name="public-ip-addresses"></a>Openbare IP-adressen
Met open bare IP-adressen kunnen Azure-resources communiceren met internet en open bare Azure-Services zoals [Azure cache voor redis](https://azure.microsoft.com/services/cache/), [Azure Event hubs](https://azure.microsoft.com/services/event-hubs/), [SQL-data bases](../sql-database/sql-database-technical-overview.md)en [Azure Storage](../storage/common/storage-introduction.md).

Een openbaar IP-adres wordt gekoppeld aan de volgende bron typen:

* Cloud services
* IaaS-Virtual Machines (Vm's)
* PaaS
* VPN-gateways
* Toepassingsgateways

### <a name="allocation-method"></a>Toewijzingsmethode
Wanneer een openbaar IP-adres moet worden toegewezen aan een Azure-resource, wordt het *dynamisch* toegewezen vanuit een groep beschik bare open bare IP-adressen binnen de locatie waarin de resource is gemaakt. Dit IP-adres wordt vrijgegeven wanneer de resource wordt gestopt. Met Cloud service gebeurt dit wanneer alle rolinstanties worden gestopt. deze kunnen worden vermeden door gebruik te maken van een *statisch* (gereserveerd) IP-adres (Zie [Cloud Services](#cloud-services)).

> [!NOTE]
> De lijst met IP-bereiken waaruit open bare IP-adressen worden toegewezen aan Azure-resources, wordt gepubliceerd op [Azure Data Center IP-adresbereiken](https://www.microsoft.com/download/details.aspx?id=41653).
> 
> 

### <a name="dns-hostname-resolution"></a>DNS-hostnaamomzetting
Wanneer u een Cloud service of een IaaS-VM maakt, moet u een DNS-naam van de Cloud service opgeven die uniek is voor alle resources in Azure. Hiermee maakt u een toewijzing in de door Azure beheerde DNS-servers voor *dnsname*. cloudapp.net naar het open bare IP-adres van de resource. Wanneer u bijvoorbeeld een Cloud service maakt met de DNS-naam van de Cloud service van **Contoso**, wordt de Fully QUALIFIED domain name (FQDN) **contoso.cloudapp.net** omgezet in een openbaar IP-adres (VIP) van de Cloud service. U kunt deze FDQN gebruiken voor het maken van een aangepaste domein-CNAME-record die verwijst naar het openbare IP-adres in Azure.

### <a name="cloud-services"></a>Cloud services
Een Cloud service heeft altijd een openbaar IP-adres waarnaar wordt verwezen als een virtueel IP-adres (VIP). U kunt eind punten maken in een Cloud service om verschillende poorten in het VIP te koppelen aan interne poorten op virtuele machines en rolinstanties in de Cloud service. 

Een Cloud service kan meerdere IaaS Vm's of PaaS-rolinstanties bevatten, allemaal beschikbaar via dezelfde Cloud service-VIP. U kunt ook [meerdere vip's toewijzen aan een Cloud service](../load-balancer/load-balancer-multivip.md), waarmee multi-VIP-scenario's zoals multi-tenant omgevingen met op SSL gebaseerde websites mogelijk zijn.

U kunt ervoor zorgen dat het open bare IP-adres van een Cloud service hetzelfde blijft, zelfs wanneer alle rolinstanties worden gestopt, met behulp van een *statisch* openbaar IP-adres, aangeduid als [gereserveerd IP](virtual-networks-reserved-public-ip.md). U kunt een statische (gereserveerde) IP-bron op een specifieke locatie maken en deze toewijzen aan elke Cloud service op die locatie. U kunt het werkelijke IP-adres voor de gereserveerde IP niet opgeven. deze wordt toegewezen uit de groep beschik bare IP-adressen op de locatie die wordt gemaakt. Dit IP-adres wordt pas vrijgegeven wanneer u het expliciet verwijdert.

Statische (gereserveerde) open bare IP-adressen worden vaak gebruikt in de scenario's waarin een Cloud service:

* vereist dat firewall regels worden ingesteld door eind gebruikers.
* is afhankelijk van externe DNS-naam omzetting en een dynamisch IP-adres moet een record bijwerken.
* gebruikt externe webservices die gebruikmaken van op IP gebaseerd beveiligings model.
* maakt gebruik van SSL-certificaten die zijn gekoppeld aan een IP-adres.

> [!NOTE]
> Wanneer u een klassieke virtuele machine maakt, wordt er een container- *Cloud service* gemaakt door Azure, die een virtueel IP-adres (VIP) heeft. Wanneer het maken van de portal is voltooid, wordt een standaard RDP-of SSH- *eind punt* geconfigureerd door de portal, zodat u verbinding kunt maken met de virtuele machine via de Cloud service-VIP. Deze Cloud service-VIP kan worden gereserveerd, waarmee effectief een gereserveerd IP-adres wordt geboden om verbinding te maken met de virtuele machine. U kunt extra poorten openen door meer eind punten te configureren.
> 
> 

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS Vm's en PaaS-rolinstanties
U kunt een openbaar IP-adres rechtstreeks toewijzen aan een IaaS- [VM](../virtual-machines/virtual-machines-linux-about.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) of PaaS Role-exemplaar binnen een Cloud service. Dit wordt een openbaar IP-adres op exemplaar niveau ([ILPIP](virtual-networks-instance-level-public-ip.md)) genoemd. Dit open bare IP-adres kan alleen dynamisch zijn.

> [!NOTE]
> Dit wijkt af van het VIP van de Cloud service, een container voor IaaS Vm's of PaaS-rolinstanties, omdat een Cloud service meerdere IaaS Vm's of PaaS rolinstanties kan bevatten, die allemaal beschikbaar zijn via dezelfde Cloud service-VIP.
> 
> 

### <a name="vpn-gateways"></a>VPN-gateways
Een [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md) kan worden gebruikt om een Azure-VNet te verbinden met andere Azure VNets of on-premises netwerken. Aan een VPN-gateway wordt *dynamisch*een openbaar IP-adres toegewezen, waarmee communicatie met het externe netwerk mogelijk is.

### <a name="application-gateways"></a>Toepassingsgateways
Een Azure [Application Gateway](../application-gateway/application-gateway-introduction.md) kan worden gebruikt voor Layer7-taak verdeling om netwerk verkeer te routeren op basis van http. Aan de toepassings gateway wordt *dynamisch*een openbaar IP-adres toegewezen, dat FUNGEERT als VIP met gelijke taak verdeling.

### <a name="at-a-glance"></a>In een oogopslag
In de volgende tabel wordt elk resource type weer gegeven met de mogelijke toewijzings methoden (dynamisch/statisch) en de mogelijkheid om meerdere open bare IP-adressen toe te wijzen.

| Resource | Dynamisch | Statisch | Meerdere IP-adressen |
| --- | --- | --- | --- |
| Cloudservice |Ja |Ja |Ja |
| IaaS VM of PaaS-rolinstantie |Ja |Nee |Nee |
| VPN-gateway |Ja |Nee |Nee |
| Toepassingsgateway |Ja |Nee |Nee |

## <a name="private-ip-addresses"></a>Privé-IP-adressen
Met persoonlijke IP-adressen kunnen Azure-resources communiceren met andere resources in een Cloud service of een [virtueel netwerk](virtual-networks-overview.md)(VNet), of naar een on-premises netwerk (via een VPN-gateway of ExpressRoute-circuit), zonder gebruik te maken van een Internet bereikbaar IP-adres.

In het klassieke Azure-implementatie model kan een persoonlijk IP-adres worden toegewezen aan de volgende Azure-resources:

* IaaS Vm's en PaaS-rolinstanties
* Interne load balancer
* Toepassingsgateway

### <a name="iaas-vms-and-paas-role-instances"></a>IaaS Vm's en PaaS-rolinstanties
Virtuele machines (Vm's) die zijn gemaakt met het klassieke implementatie model worden altijd in een Cloud service geplaatst, vergelijkbaar met PaaS. Het gedrag van privé-IP-adressen is daarom vergelijkbaar voor deze resources.

Het is belang rijk te weten dat een Cloud service op twee manieren kan worden geïmplementeerd:

* Als een *zelfstandige* Cloud service, waar deze zich niet in een virtueel netwerk bevindt.
* Als onderdeel van een virtueel netwerk.

#### <a name="allocation-method"></a>Toewijzingsmethode
In het geval van een *zelfstandige* Cloud service krijgen resources een persoonlijk IP-adres *dynamisch* toegewezen uit het persoonlijke IP-adres bereik van Azure Data Center. Deze kan alleen worden gebruikt voor communicatie met andere virtuele machines in dezelfde Cloud service. Dit IP-adres kan veranderen wanneer de resource wordt gestopt en gestart.

In het geval van een Cloud service die binnen een virtueel netwerk is geïmplementeerd, krijgen resources een privé-IP-adres (sen) toegewezen uit het adres bereik van de gekoppelde subnetten (zoals opgegeven in de netwerk configuratie). Deze privé-IP-adressen kunnen worden gebruikt voor communicatie tussen alle virtuele machines binnen het VNet.

In het geval van Cloud Services binnen een VNet wordt een privé-IP-adres standaard *dynamisch* toegewezen (via DHCP). Het kan worden gewijzigd wanneer de resource wordt gestopt en gestart. Om ervoor te zorgen dat het IP-adres hetzelfde blijft, moet u de toewijzings methode instellen op *static*en een geldig IP-adres binnen het bijbehorende adres bereik opgeven.

Statische privé-IP-adressen worden vaak gebruikt voor:

* Virtuele machines die fungeren als domeincontrollers of DNS-servers.
* Vm's waarvoor Firewall regels met IP-adressen zijn vereist.
* Vm's met services die door andere apps worden gebruikt via een IP-adres.

#### <a name="internal-dns-hostname-resolution"></a>Omzetting van interne DNS-hostnamen
Alle Azure-Vm's en PaaS-rolinstanties worden standaard geconfigureerd met door [Azure beheerde DNS-servers](virtual-networks-name-resolution-for-vms-and-role-instances.md#azure-provided-name-resolution) , tenzij u expliciet aangepaste DNS-servers configureert. Deze DNS-servers bieden een interne naam omzetting voor Vm's en rolinstanties die zich binnen hetzelfde VNet of dezelfde Cloud service bevinden.

Wanneer u een virtuele machine maakt, wordt er een toewijzing van de hostnaam aan het bijbehorende privé-IP-adres toegevoegd aan de via Azure beheerde DNS-servers. Met een multi-NIC-VM wordt de hostnaam toegewezen aan het privé-IP-adres van de primaire NIC. Deze toewijzings gegevens zijn echter beperkt tot resources binnen dezelfde Cloud service of VNet.

In het geval van een *zelfstandige* Cloud service kunt u hostnamen van alle virtuele machines en rolinstanties binnen dezelfde Cloud service omzetten. In het geval van een Cloud service binnen een VNet kunt u hostnamen van alle virtuele machines/rolinstanties in het VNet omzetten.

### <a name="internal-load-balancers-ilb--application-gateways"></a>Interne load balancers (ILB) en toepassingsgateways
U kunt een privé-IP-adres toewijzen aan de **front-end**-configuratie van een [interne Azure Load Balancer](../load-balancer/load-balancer-internal-overview.md) (ILB) of een [Azure Application Gateway](../application-gateway/application-gateway-introduction.md). Dit privé-IP-adres fungeert als een intern eindpunt dat alleen toegankelijk is voor de resources binnen het virtuele netwerk (VNet) en de externe netwerken die met het VNet zijn verbonden. U kunt een dynamisch of statisch privé-IP-adres toewijzen aan de front-end-configuratie. U kunt ook meerdere privé-IP-adressen toewijzen om multi-VIP-scenario's mogelijk te maken.

### <a name="at-a-glance"></a>In een oogopslag
In de volgende tabel wordt elk resource type weer gegeven met de mogelijke toewijzings methoden (dynamisch/statisch) en de mogelijkheid om meerdere privé IP-adressen toe te wijzen.

| Resource | Dynamisch | Statisch | Meerdere IP-adressen |
| --- | --- | --- | --- |
| VM (in een *zelfstandige* Cloud service of VNet) |Ja |Ja |Ja |
| PaaS (in een *zelfstandige* Cloud service of VNet) |Ja |Nee |Nee |
| Interne load balancer front-end |Ja |Ja |Ja |
| Front-end van Application Gateway |Ja |Ja |Ja |

## <a name="limits"></a>Limieten
In de onderstaande tabel ziet u de limieten die zijn opgelegd voor IP-adres Sering in azure per abonnement. U kunt [contact opnemen met ondersteuning](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade) als u op basis van uw bedrijfsbehoeften de standaardlimieten wilt verhogen tot de maximumlimieten.

|  | Standaardlimiet | Maximumaantal |
| --- | --- | --- |
| Openbare IP-adressen (dynamisch) |5 |Contact opnemen met ondersteuning |
| Gereserveerde openbare IP-adressen |20 |Contact opnemen met ondersteuning |
| Open bare VIP per implementatie (Cloud service) |5 |Contact opnemen met ondersteuning |
| Privé-VIP (ILB) per implementatie (Cloud service) |1 |1 |

Lees de volledige set [limieten voor netwerken](../azure-subscription-service-limits.md#networking-limits) in Azure.

## <a name="pricing"></a>Prijzen
In de meeste gevallen zijn open bare IP-adressen gratis. Er is een nominale belasting voor het gebruik van extra en/of statische open bare IP-adressen. Zorg ervoor dat u de [prijs structuur voor open bare ip's](https://azure.microsoft.com/pricing/details/ip-addresses/)begrijpt.

## <a name="differences-between-resource-manager-and-classic-deployments"></a>Verschillen tussen Resource Manager en klassieke implementaties
Hieronder vindt u een vergelijking van de functies voor IP-adres Sering in Resource Manager en het klassieke implementatie model.

|  | Resource | Klassiek | Resource Manager |
| --- | --- | --- | --- |
| **Openbaar IP-adres** |***VM*** |Wordt een ILPIP genoemd (alleen dynamisch) |Wordt aangeduid als een openbaar IP-adres (dynamisch of statisch) |
|  ||Toegewezen aan een IaaS-VM of een PaaS-rolinstantie |Gekoppeld aan de NIC van de virtuele machine |
|  |***Internet gerichte load balancer*** |Wordt aangeduid als VIP (dynamisch) of Gereserveerd IP (statisch) |Wordt aangeduid als een openbaar IP-adres (dynamisch of statisch) |
|  ||Toegewezen aan een Cloud service |Gekoppeld aan de front-end configuratie van de load balancer |
|  | | | |
| **Privé-IP-adres** |***VM*** |Aangeduid als een DIP |Aangeduid als een privé IP-adres |
|  ||Toegewezen aan een IaaS-VM of een PaaS-rolinstantie |Toegewezen aan de NIC van de virtuele machine |
|  |***Interne load balancer (ILB)*** |Toegewezen aan de ILB (dynamisch of statisch) |Toegewezen aan de front-end configuratie van de ILB (dynamisch of statisch) |

## <a name="next-steps"></a>Volgende stappen
* [Implementeer een virtuele machine met een statisch privé-IP-adres](virtual-networks-static-private-ip-classic-pportal.md) met behulp van de Azure Portal.

