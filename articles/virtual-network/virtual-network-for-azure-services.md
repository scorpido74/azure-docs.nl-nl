---
title: Virtueel netwerk voor Azure-services
titlesuffix: Azure Virtual Network
description: Meer informatie over de voordelen van het implementeren van resources in een virtueel netwerk. Bronnen in virtuele netwerken kunnen met elkaar en on-premises bronnen communiceren, zonder dat er verkeer via internet wordt gebruikt.
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
ms.openlocfilehash: 24bcc7e698527cd39958c53b48a0b36404c36bb4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79279661"
---
# <a name="virtual-network-integration-for-azure-services"></a>Integratie van virtuele netwerken voor Azure-services

Als u Azure-services integreert in een virtueel Azure-netwerk, u privétoegang tot de service maken vanaf virtuele machines of rekenbronnen in het virtuele netwerk.
U Azure-services in uw virtuele netwerk integreren met de volgende opties:
- Het implementeren van speciale exemplaren van de service in een virtueel netwerk. De diensten zijn dan privé toegankelijk binnen het virtuele netwerk en via on-premises netwerken.
- Met Behulp van [Private Link](../private-link/private-link-overview.md) om privé toegang te krijgen tot een specifiek exemplaar van de service vanuit uw virtuele netwerk en vanaf on-premises netwerken.

U de service ook openen via openbare eindpunten door een virtueel netwerk uit te breiden naar de service, via [serviceeindpunten.](virtual-network-service-endpoints-overview.md) Met serviceeindpunten kunnen serviceresources worden beveiligd voor het virtuele netwerk.
 
## <a name="deploy-azure-services-into-virtual-networks"></a>Azure-services implementeren in virtuele netwerken

Wanneer u dedicated Azure-services implementeert in een [virtueel netwerk,](virtual-networks-overview.md)u privé communiceren met de servicebronnen via privé-IP-adressen.

![Services geïmplementeerd in een virtueel netwerk](./media/virtual-network-for-azure-services/deploy-service-into-vnet.png)

Het implementeren van services binnen een virtueel netwerk biedt de volgende mogelijkheden:

- Bronnen binnen het virtuele netwerk kunnen privé met elkaar communiceren, via privé-IP-adressen. Bijvoorbeeld het rechtstreeks overdragen van gegevens tussen HDInsight en SQL Server die op een virtuele machine wordt uitgevoerd, in het virtuele netwerk.
- On-premises bronnen hebben toegang tot bronnen in een virtueel netwerk met behulp van privé-IP-adressen via een [VPN-gateway (Site-to-Site)](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json#s2smulti) of [ExpressRoute.](../expressroute/expressroute-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)
- Virtuele netwerken kunnen worden [gekeken](virtual-network-peering-overview.md) om resources in de virtuele netwerken in staat te stellen met elkaar te communiceren met behulp van privé-IP-adressen.
- Service-exemplaren in een virtueel netwerk worden doorgaans volledig beheerd door de Azure-service. Dit omvat het bewaken van de status van de resources en schalen met belasting.
- Service-exemplaren worden geïmplementeerd in een subnet in een virtueel netwerk. Inkomende en uitgaande netwerktoegang voor het subnet moet worden geopend via [netwerkbeveiligingsgroepen](security-overview.md#network-security-groups), volgens de richtlijnen die door de service worden verstrekt.
- Bepaalde services leggen ook beperkingen op aan het subnet waarin ze worden ingezet, waardoor de toepassing van beleid, routes of het combineren van VM's en serviceresources binnen hetzelfde subnet wordt beperkt. Neem contact op met elke service over de specifieke beperkingen als ze kunnen veranderen in de tijd. Voorbeelden van dergelijke services zijn Azure NetApp Files, Dedicated HSM, Azure Container Instances, App Service. 
- Optioneel vereisen services mogelijk een [gedelegeerd subnet](virtual-network-manage-subnet.md#add-a-subnet) als expliciete id waarmee een subnet een bepaalde service kan hosten. Door te delegeren, krijgen services expliciete machtigingen om servicespecifieke resources te maken in het gedelegeerde subnet.
- Zie een voorbeeld van een REST API-antwoord op een [virtueel netwerk met een gedelegeerd subnet.](https://docs.microsoft.com/rest/api/virtualnetwork/virtualnetworks/get#get-virtual-network-with-a-delegated-subnet) Een uitgebreide lijst van services die gebruik maken van het gedelegeerde subnetmodel kan worden verkregen via de [API beschikbare delegaties.](https://docs.microsoft.com/rest/api/virtualnetwork/availabledelegations/list)

### <a name="services-that-can-be-deployed-into-a-virtual-network"></a>Services die kunnen worden geïmplementeerd in een virtueel netwerk

|Categorie|Service| Dedicated¹ Subnet
|-|-|-|
| Compute | Virtuele machines: [Linux](../virtual-machines/linux/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) of [Windows](../virtual-machines/windows/infrastructure-networking-guidelines.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Virtuele machineschaalsets](../virtual-machine-scale-sets/virtual-machine-scale-sets-mvss-existing-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Cloud Service:](https://msdn.microsoft.com/library/azure/jj156091)Alleen virtueel netwerk (klassiek)<br/> [Azure Batch](../batch/batch-api-basics.md?toc=%2fazure%2fvirtual-network%2ftoc.json#virtual-network-vnet-and-firewall-configuration)| Nee <br/> Nee <br/> Nee <br/> No²
| Netwerk | [Application Gateway - WAF](../application-gateway/application-gateway-ilb-arm.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[VPN Gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Firewall](../firewall/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) <br/>[Netwerk virtuele apparaten](/windows-server/networking/sdn/manage/use-network-virtual-appliances-on-a-vn) | Ja <br/> Ja <br/> Ja <br/> Nee
|Gegevens|[RedisCache](../azure-cache-for-redis/cache-how-to-premium-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure SQL Database Managed Instance](../sql-database/sql-database-managed-instance-connectivity-architecture.md?toc=%2fazure%2fvirtual-network%2ftoc.json)| Ja <br/> Ja <br/> 
|Analyse | [Azure HDInsight](../hdinsight/hdinsight-extend-hadoop-virtual-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Databricks](../azure-databricks/what-is-azure-databricks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |No² <br/> No² <br/> 
| Identiteit | [Azure Active Directory Domain Services](../active-directory-domain-services/active-directory-ds-getting-started-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json) |Nee <br/>
| Containers | [Azure Kubernetes Service (AKS)](../aks/concepts-network.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Container Instance (ACI)](https://www.aka.ms/acivnet)<br/>[Azure Container Service Engine](https://github.com/Azure/acs-engine) met Azure Virtual Network CNI-plug-in [plug-in](https://github.com/Azure/acs-engine/tree/master/examples/vnet)|No²<br/> Ja <br/><br/> Nee
| Web | [API Management](../api-management/api-management-using-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[App-serviceomgeving](../app-service/web-sites-integrate-with-vnet.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure Logic Apps](../logic-apps/connect-virtual-network-vnet-isolated-environment-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ja <br/> Ja <br/> Ja
| Gehost | [Azure toegewezen HSM](../dedicated-hsm/index.yml?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>[Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md?toc=%2fazure%2fvirtual-network%2ftoc.json)<br/>|Ja <br/> Ja <br/>
| | |

¹ 'Dedicated' houdt in dat alleen servicespecifieke resources in dit subnet kunnen worden geïmplementeerd en niet kunnen worden gecombineerd met vm/VMSs van klanten <br/> ² Het wordt aanbevolen als een beste praktijk om deze diensten in een speciaal subnet te hebben, maar niet een verplichte eis opgelegd door de dienst.
