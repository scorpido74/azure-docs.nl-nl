---
title: Virtueel netwerk voor Azure-services
titlesuffix: Azure Virtual Network
description: Meer informatie over de voor delen van het implementeren van resources in een virtueel netwerk. Resources in virtuele netwerken kunnen met elkaar communiceren, en on-premises resources zonder verkeer dat het Internet passeren.
services: virtual-network
documentationcenter: na
author: malopMSFT
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/25/2017
ms.author: malop
ms.reviewer: kumud
ms.openlocfilehash: c345b31c218c4678e7811c36113c94e0c4d2ac03
ms.sourcegitcommit: 71db032bd5680c9287a7867b923bf6471ba8f6be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/16/2019
ms.locfileid: "71019064"
---
# <a name="virtual-network-integration-for-azure-services"></a>Integratie van virtueel netwerk voor Azure-Services

Door Azure-Services te integreren in een virtueel Azure-netwerk, kunt u persoonlijke toegang tot de service krijgen via virtuele machines of reken resources in het virtuele netwerk.
U kunt Azure-Services in uw virtuele netwerk integreren met de volgende opties:
- Implementatie van toegewezen exemplaren van de service in een virtueel netwerk. De services kunnen vervolgens worden geopend in het virtuele netwerk en vanuit on-premises netwerken.
- Een [persoonlijke koppeling](../private-link/private-link-overview.md) gebruiken voor toegang tot een specifiek exemplaar van de service vanuit uw virtuele netwerk en uit on-premises netwerken.

U kunt de service ook openen met behulp van open bare eind punten door een virtueel netwerk uit te breiden naar de service, via [service-eind punten](virtual-network-service-endpoints-overview.md). Met Service-eind punten kunnen service resources worden beveiligd met het virtuele netwerk.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Azure-Services implementeren in virtuele netwerken

Wanneer u toegewezen Azure-Services in een [virtueel netwerk](virtual-networks-overview.md)implementeert, kunt u met de service bronnen privé communiceren via privé-IP-adressen.

![Services die zijn geïmplementeerd in een virtueel netwerk](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Het implementeren van services binnen een virtueel netwerk biedt de volgende mogelijkheden:

- Resources binnen het virtuele netwerk kunnen met elkaar communiceren via privé-IP-adressen. Zo kunt u gegevens rechtstreeks overbrengen tussen HDInsight en SQL Server die worden uitgevoerd op een virtuele machine, in het virtuele netwerk.
- On-premises resources hebben toegang tot bronnen in een virtueel netwerk met behulp van privé-IP-adressen via een [site-to-site VPN (VPN gateway)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) of [ExpressRoute](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json).
- Virtuele netwerken [kunnen worden gekoppeld](virtual-network-peering-overview.md) om resources in de virtuele netwerken met elkaar te laten communiceren met behulp van privé-IP-adressen.
- Service-exemplaren in een virtueel netwerk worden doorgaans volledig beheerd door de Azure-service. Dit omvat het controleren van de status van de resources en schalen met laden.
- Service-exemplaren worden geïmplementeerd in een subnet in een virtueel netwerk. Binnenkomende en uitgaande netwerk toegang voor het subnet moet worden geopend via [netwerk beveiligings groepen](security-overview.md#network-security-groups), volgens de richt lijnen van de service.
- Bepaalde services leggen ook beperkingen op het subnet waarin ze worden geïmplementeerd, waardoor de toepassing van beleids regels wordt beperkt, de virtuele machines en service bronnen binnen hetzelfde subnet worden gedistribueerd of gecombineerd. Controleer bij elke service op de specifieke beperkingen, aangezien deze na verloop van tijd kunnen veranderen. Voor beelden van dergelijke services zijn Azure NetApp Files, toegewezen HSM, Azure Container Instances App Service. 
- Services kunnen eventueel een [gedelegeerd subnet](virtual-network-manage-subnet.md#add-a-subnet) vereisen als een expliciete id die een subnet kan hosten voor een bepaalde service. Door het delegeren van Services krijgen expliciete machtigingen voor het maken van servicespecifieke bronnen in het gedelegeerde subnet.
- Bekijk een voor beeld van een REST API antwoord op een [virtueel netwerk met een overgedragen subnet](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet). Een uitgebreide lijst met services die gebruikmaken van het gedelegeerde subnet model kan worden verkregen via de [beschik bare delegaties](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list) -API.

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Services die kunnen worden geïmplementeerd in een virtueel netwerk

|Categorie|Service| Toegewezen ¹-subnet
|-|-|-|
| Compute | Virtuele machines: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Virtuele-machineschaalsets](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Cloud service](https://msdn.microsoft.com/library/azure/jj156091): Alleen virtueel netwerk (klassiek)<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Nee <br/> Nee <br/> Nee <br/> Nee ²
| Netwerk | [Application Gateway-WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Virtuele netwerk apparaten](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | Ja <br/> Ja <br/> Ja <br/> Nee
|Data|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Beheerd exemplaar voor Azure SQL Database](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Ja <br/> Ja <br/> 
|Analyse | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Nee ² <br/> Nee ² <br/> 
| Identiteit | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Nee <br/>
| Containers | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure container instance (ACI)](https://www.aka.ms/acivnet)<br/>[Azure container service-engine](https://github.com/Azure/acs-engine) met Azure Virtual Network cni [-invoeg toepassing](https://github.com/Azure/acs-engine/tree/master/examples/vnet)|Nee ²<br/> Ja <br/><br/> Nee
| Web | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App Service-omgeving](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ja <br/> Ja <br/> Ja
| Gehost | [Specifieke HSM van Azure](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ja <br/> Ja <br/>
| | |

¹ ' dedicated ' impliceert dat alleen servicespecifieke bronnen kunnen worden geïmplementeerd in dit subnet en niet kunnen worden gecombineerd met de VM-VMSSs van de klant <br/> ² aanbevolen, maar geen verplichte vereiste die door de service is opgelegd.
