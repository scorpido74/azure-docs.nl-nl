---
title: Azure service tags overview
titlesuffix: Azure Virtual Network
description: Learn about service tags. Service tags help minimize complexity of security rule creation.
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2019
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: 33ee7351e547ee5ef57ef07f67ba6f5f4410b57f
ms.sourcegitcommit: f523c8a8557ade6c4db6be12d7a01e535ff32f32
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/22/2019
ms.locfileid: "74384147"
---
# <a name="virtual-network-service-tags"></a>Virtual network service tags 
<a name="network-service-tags"></a>

A service tag represents a group of IP address prefixes from a given Azure service. It helps to minimize complexity of frequent updates on network security rules. You can use service tags to define network access controls on [Network Security Groups](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) or [Azure Firewall](https://docs.microsoft.com/azure/firewall/service-tags). U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. By specifying the service tag name (e.g., **ApiManagement**) in the appropriate *source* or *destination* field of a rule, you can allow or deny the traffic for the corresponding service. Microsoft manages the address prefixes encompassed by the service tag and automatically updates the service tag as addresses change. 

## <a name="available-service-tags"></a>Available service tags
The following table includes all of the service tags available to be used in [Network Security Groups](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) rules.

The columns indicate whether the tag is:

- Suitable for rules covering inbound or outbound traffic
- Support [regional](https://azure.microsoft.com/regions) scope 
- Usable in [Azure Firewall](https://docs.microsoft.com/azure/firewall/service-tags) rules

By default, service tags reflect the ranges for the entire cloud.  Some service tags also allow more finer-grain control by restricting the corresponding IP ranges to a specified region.  For example while the service tag **Storage** represents Azure Storage for the entire cloud,  **Storage.WestUS** narrows that to only the storage IP address ranges from the WestUS region.  The descriptions of each Service tag below indicates whether they support such regional scope.  



| Tag | Doel | Can use Inbound or Outbound? | Can be Regional? | Can use with Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ApiManagement** | Management traffic for APIM dedicated deployments. | Beide | Nee | Ja |
| **AppService**    | App Service service. This tag is recommended for outbound security rules to WebApp frontends. | Uitgaand | Ja | Ja |
| **AppServiceManagement** | Management traffic for App Service Environment dedicated deployments. | Beide | Nee | Ja |
| **AzureActiveDirectory** | Azure Active Directory service. | Uitgaand | Nee | Ja |
| **AzureActiveDirectoryDomainServices** | Management traffic for Azure Active Directory Domain Services dedicated deployments. | Beide | Nee | Ja |
| **AzureBackup** |Azure Backup service.<br/><br/>*Note:* This tag has a dependency on the **Storage** and **AzureActiveDirectory** tags. | Uitgaand | Nee | Ja |
| **AzureCloud** | All [datacenter public IP addresses](https://www.microsoft.com/download/details.aspx?id=41653). | Uitgaand | Ja | Ja |
| **AzureConnectors** | Logic Apps connectors for probe/backend connections. | Inkomend | Ja | Ja |
| **AzureContainerRegistry** | Azure Container Registry service. | Uitgaand | Ja | Ja |
| **AzureCosmosDB** | Azure Cosmos Database service. | Uitgaand | Ja | Ja |
| **AzureDataLake** | Azure Data Lake service. | Uitgaand | Nee | Ja |
| **AzureIoTHub** | Azure IoT Hub service. | Uitgaand | Nee | Nee |
| **AzureKeyVault** | Azure KeyVault service.<br/><br/>*Note:* This tag has a dependency on the **AzureActiveDirectory** tag. | Uitgaand | Ja | Ja |
| **AzureLoadBalancer** | Azure's infrastructure load balancer. De tag wordt omgezet in het [Virtuele IP-adres van de host](security-overview.md#azure-platform-considerations) (168.63.129.16) van waaruit statuscontroles van Azure worden uitgevoerd. Als u de load balancer van Azure niet gebruikt, kunt u deze regel onderdrukken. | Beide | Nee | Nee |
| **AzureMachineLearning** | Azure Machine Learning service. | Uitgaand | Nee | Ja |
| **AzureMonitor** | Log Analytics, App Insights, AzMon, and custom metrics (GiG endpoints).<br/><br/>*Note:* For Log Analytics, this tag has dependency on the **Storage** tag. | Uitgaand | Nee | Ja |
| **AzurePlatformDNS** | The basic infrastructure (default) DNS service.<br/><br>You can use this tag to disable the default DNS. Please exercise caution in using this tag. Reading [Azure platform considerations](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) is recommended. Testing is recommended before using this tag. | Uitgaand | Nee | Nee |
| **AzurePlatformIMDS** | IMDS, which is a basic infrastructure service.<br/><br/>You can use this tag to disable the default IMDS.  Please exercise caution in using this tag. Reading [Azure platform considerations](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) is recommended. Testing is recommended before using this tag. | Uitgaand | Nee | Nee |
| **AzurePlatformLKM** | Windows licensing or key management service.<br/><br/>You can use this tag to disable the defaults for licensing. Please exercise caution in using this tag.  Reading [Azure platform considerations](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) is recommended. Testing is recommended before using this tag. | Uitgaand | Nee | Nee |
| **AzureTrafficManaged** | Azure Traffic Manager probe IP addresses.<br/><br/>Meer informatie over de test-IP-adressen van Traffic Manager vindt u in de [Veelgestelde vragen over Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | Inkomend | Nee | Ja |  
| **BatchNodeManagement** | Management traffic for Azure Batch dedicated deployments. | Beide | Nee | Ja |
| **CognitiveServicesManagement** | The address ranges for traffic for Cognitive Services | Uitgaand | Nee | Nee |
| **Dynamics365ForMarketingEmail** | The address ranges for the marketing email service of Dynamics 365. | Uitgaand | Ja | Nee |
| **EventHub** | Azure EventHub service. | Uitgaand | Ja | Ja |
| **GatewayManager** | Management traffic for VPN/App Gateways dedicated deployments. | Inkomend | Nee | Nee |
| **Internet** | The IP address space that is outside the virtual network and reachable by the public Internet.<br/><br/>Dit adresbereik omvat ook de [openbare IP-adresruimte van Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Beide | Nee | Nee |
| **MicrosoftContainerRegistry** | Microsoft Container Registry service. | Uitgaand | Ja | Ja |
| **ServiceBus** | Azure Service Bus service using the Premium service tier. | Uitgaand | Ja | Ja |
| **ServiceFabric** | Service Fabric service. | Uitgaand | Nee | Nee |
| **Sql** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL, and Azure SQL Data Warehouse services.<br/><br/>*Note:* This tag represents the service, but not specific instances of the service. De tag vertegenwoordigt bijvoorbeeld de service Azure SQL Database, maar geen specifieke SQL-database of -server. | Uitgaand | Ja | Ja |
| **SqlManagement** | Management traffic for SQL dedicated deployments. | Beide | Nee | Ja |
| **Storage** | Azure Storage service. <br/><br/>*Note:* The tag represents the service, but not specific instances of the service. De tag vertegenwoordigt bijvoorbeeld de service Azure Storage, maar geen specifiek Azure Storage-account. | Uitgaand | Ja | Ja |
| **VirtualNetwork** | The virtual network address space (all IP address ranges defined for the virtual network), all connected on-premises address spaces, [peered](virtual-network-peering-overview.md) virtual networks, or virtual network connected to a [virtual network gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), the [virtual IP address of the host](security-overview.md#azure-platform-considerations) and address prefixes used on [user-defined routes](virtual-networks-udr-overview.md). Be aware that this tag may also contain default routes. | Beide | Nee | Nee |

>[!NOTE]
>When working in the *Classic* (pre-Azure Resource Manager) environment, a select set of the above tags are supported.  These use an alternative spelling:

| Classic spelling | Equivalent Resource Manager tag |
|---|---|
| AZURE_LOADBALANCER | AzureLoadBalancer |
| INTERNET | Internet |
| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Met servicetags van Azure-services worden de adresvoorvoegsels aangeduid van de specifieke cloud die wordt gebruikt. e.g. the underlying IP ranges corresponding to the **Sql** tag value will differ between the Azure Public cloud and the Azure China cloud.

> [!NOTE]
> Als u een [service-eindpunt voor een virtueel netwerk](virtual-network-service-endpoints-overview.md) voor een service als Azure Storage of Azure SQL Database implementeert, wordt een [route](virtual-networks-udr-overview.md#optional-default-routes) naar een virtueel netwerksubnet voor de service toegevoegd. De adresvoorvoegsels in de route zijn dezelfde adresvoorvoegsels (of CIDR-bereiken) als de bijbehorende servicetag.



## <a name="service-tags-in-on-premises"></a>Service tags in on-premises  
You can obtain the current service tag and range information to include as part of your on-premises firewall configurations.  This information is the current point-in-time list of the IP ranges corresponding to each service tag.  The information can be obtained programmatically or via JSON file download as follows.

### <a name="service-tag-discovery-api-public-preview"></a>Service tag Discovery API (Public Preview)
You can programmatically retrieve the current list of service tags with IP address range details:

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> While in Public Preview, the Discovery API may return information that is not as current as the JSON downloads (below).


### <a name="discover-service-tags-using-downloadable-json-files"></a>Discover service tags using downloadable JSON files 
You can download JSON files containing the current list of service tags with IP address range details. These are updated and published weekly.  Locations for each cloud are:

- [Azure Public](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure China](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Germany](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>A subset of this information has previously been published in XML files for [Azure Public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure China](https://www.microsoft.com/download/details.aspx?id=42064) and [Azure Germany](https://www.microsoft.com/download/details.aspx?id=54770). These XML downloads will be deprecated by June 30, 2020 and will no longer be available after that date. Please migrate to using the Discovery API or JSON file downloads as described above.

### <a name="tips"></a>Tips 
- You can detect updates from one publishing to the next via increased *changeNumber* values within the JSON file. Each subsection (e.g. **Storage.WestUS**) has its own *changeNumber* that is incremented as changes occur.  The top level of the file's *changeNumber* is incremented when any of the subsections has changed.
- For examples of how to parse the service tag information (e.g. get all address ranges for Storage in WestUS), please refer to the [Service Tag Discovery API PowerShell](https://aka.ms/discoveryapi_powershell) documentation.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Een netwerkbeveiligingsgroep maken](tutorial-filter-network-traffic.md).

