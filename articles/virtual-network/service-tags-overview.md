---
title: Overzicht van Azure-service Tags
titlesuffix: Azure Virtual Network
description: Meer informatie over service tags. Service Tags helpen de complexiteit van het maken van de beveiligings regel te minimaliseren.
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
ms.openlocfilehash: b1219153b2b2a0246110d2d9a7a84d16cc2a25f0
ms.sourcegitcommit: f2149861c41eba7558649807bd662669574e9ce3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/07/2020
ms.locfileid: "75707556"
---
# <a name="virtual-network-service-tags"></a>Service tags van virtueel netwerk 
<a name="network-service-tags"></a>

Een servicetag vertegenwoordigt een groep IP-adres voorvoegsels van een bepaalde Azure-service. Zo kunt u de complexiteit van regel matige updates voor netwerk beveiligings regels minimaliseren. U kunt service tags gebruiken voor het definiëren van netwerk toegangs beheer voor [netwerk beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) of [Azure firewall](https://docs.microsoft.com/azure/firewall/service-tags). 

U kunt service tags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligings regels maakt. Door de naam van de service label (bijvoorbeeld **ApiManagement**) op te geven in het juiste *bron* -of *doel* veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen.

## <a name="available-service-tags"></a>Beschik bare service Tags
De volgende tabel bevat alle service tags die beschikbaar zijn voor gebruik in regels voor de [netwerk beveiligings groep](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) .

De kolommen geven aan of de tag:

- Is geschikt voor regels die betrekking hebben op inkomend of uitgaand verkeer.
- Ondersteunt het [regionale](https://azure.microsoft.com/regions) bereik.
- Is bruikbaar in [Azure firewall](https://docs.microsoft.com/azure/firewall/service-tags) -regels.

Service Tags geven standaard de bereiken weer voor de hele Cloud. Sommige service Tags bieden ook een nauw keurigere controle door de overeenkomende IP-bereiken te beperken tot een bepaalde regio. De service tags **opslag** vertegenwoordigt bijvoorbeeld Azure Storage voor de hele Cloud, maar **opslag. westus** beperkt het bereik tot alleen de IP-adresbereiken van het opslag gebied van de regio westus. In de volgende tabel wordt aangegeven of elk servicetag het regionale bereik ondersteunt.  

| Tag | Doel | Kunt u inkomend of uitgaand gebruiken? | Kan regionaal worden? | Kunt gebruiken met Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ApiManagement** | Beheer verkeer voor Azure API Management-specifieke implementaties. | Beide | Nee | Ja |
| **AppService**    | Azure App Service. Deze tag wordt aanbevolen voor uitgaande beveiligings regels voor front-ends van webtoepassingen. | Uitgaand | Ja | Ja |
| **AppServiceManagement** | Beheer verkeer voor implementaties die zijn toegewezen aan App Service Environment. | Beide | Nee | Ja |
| **AzureActiveDirectory** | Azure Active Directory. | Uitgaand | Nee | Ja |
| **AzureActiveDirectoryDomainServices** | Beheer verkeer voor implementaties die zijn toegewezen aan Azure Active Directory Domain Services. | Beide | Nee | Ja |
| **AzureBackup** |Azure Backup.<br/><br/>*Opmerking:* Deze tag bevat een afhankelijkheid van de **opslag** -en **AzureActiveDirectory** -Tags. | Uitgaand | Nee | Ja |
| **Cloud** | Alle [open bare IP-adressen van data centers](https://www.microsoft.com/download/details.aspx?id=41653). | Uitgaand | Ja | Ja |
| **AzureConnectors** | Azure Logic Apps connectors voor probe/back-endverbinding. | Inkomend | Ja | Ja |
| **AzureContainerRegistry** | Azure Container Registry. | Uitgaand | Ja | Ja |
| **AzureCosmosDB** | Azure Cosmos DB. | Uitgaand | Ja | Ja |
| **AzureDataLake** | Azure Data Lake. | Uitgaand | Nee | Ja |
| **AzureHDInsight** | Azure HDInsight. | Inkomend | Ja | Nee |
| **AzureIoTHub** | Azure IoT Hub. | Uitgaand | Nee | Nee |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Opmerking:* Deze tag bevat een afhankelijkheid van de **AzureActiveDirectory** -tag. | Uitgaand | Ja | Ja |
| **AzureLoadBalancer** | De Azure-infrastructuur load balancer. De tag wordt omgezet in het [virtuele IP-adres van de host](security-overview.md#azure-platform-considerations) (168.63.129.16) waar de Azure Health-tests afkomstig zijn. Als u Azure Load Balancer niet gebruikt, kunt u deze regel onderdrukken. | Beide | Nee | Nee |
| **AzureMachineLearning** | Azure Machine Learning. | Beide | Nee | Ja |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon en aangepaste metrische gegevens (GB-eind punten).<br/><br/>*Opmerking:* Voor Log Analytics heeft deze tag een afhankelijkheid van de **opslag** code. | Uitgaand | Nee | Ja |
| **AzurePlatformDNS** | De basis-DNS-service (standaard).<br/><br>U kunt deze tag gebruiken om de standaard-DNS uit te scha kelen. Wees voorzichtig wanneer u deze tag gebruikt. U wordt aangeraden [Azure-platform overwegingen](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)te lezen. We raden u ook aan eerst tests uit te voeren voordat u deze tag gebruikt. | Uitgaand | Nee | Nee |
| **AzurePlatformIMDS** | Azure Instance Metadata Service (IMDS), een basis infrastructuur service.<br/><br/>U kunt deze tag gebruiken om de standaard IMDS uit te scha kelen. Wees voorzichtig wanneer u deze tag gebruikt. U wordt aangeraden [Azure-platform overwegingen](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)te lezen. We raden u ook aan eerst tests uit te voeren voordat u deze tag gebruikt. | Uitgaand | Nee | Nee |
| **AzurePlatformLKM** | Windows-licentie verlening of service voor sleutel beheer.<br/><br/>U kunt deze tag gebruiken om de standaard instellingen voor licentie verlening uit te scha kelen. Wees voorzichtig wanneer u deze tag gebruikt. U wordt aangeraden [Azure-platform overwegingen](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)te lezen.  We raden u ook aan eerst tests uit te voeren voordat u deze tag gebruikt. | Uitgaand | Nee | Nee |
| **AzureTrafficManager** | IP-adressen van Azure Traffic Manager test.<br/><br/>Zie [Veelgestelde vragen over Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs)voor meer informatie over het IP-adres van Traffic Manager test. | Inkomend | Nee | Ja |  
| **BatchNodeManagement** | Beheer verkeer voor implementaties die zijn toegewezen aan Azure Batch. | Beide | Nee | Ja |
| **CognitiveServicesManagement** | De adresbereiken voor verkeer voor Azure Cognitive Services. | Uitgaand | Nee | Nee |
| **Dynamics365ForMarketingEmail** | De adresbereiken voor de marketing-e-mail service van Dynamics 365. | Uitgaand | Ja | Nee |
| **EventHub** | Azure Event Hubs. | Uitgaand | Ja | Ja |
| **GatewayManager** | Beheer verkeer voor implementaties die zijn toegewezen aan Azure VPN Gateway en Application Gateway. | Inkomend | Nee | Nee |
| **Internet** | De IP-adres ruimte die zich buiten het virtuele netwerk bevindt en bereikbaar is via het open bare Internet.<br/><br/>Het adres bereik bevat de [open bare IP-adres ruimte van Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Beide | Nee | Nee |
| **MicrosoftContainerRegistry** | Azure Container Registry. | Uitgaand | Ja | Ja |
| **ServiceBus** | Azure Service Bus verkeer dat gebruikmaakt van de Premium-servicelaag. | Uitgaand | Ja | Ja |
| **ServiceFabric** | Azure Service Fabric. | Uitgaand | Nee | Nee |
| **Sql** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL en Azure SQL Data Warehouse.<br/><br/>*Opmerking:* Deze tag vertegenwoordigt de service, maar geen specifieke exemplaren van de service. De tag vertegenwoordigt bijvoorbeeld de service Azure SQL Database, maar geen specifieke SQL-database of -server. | Uitgaand | Ja | Ja |
| **SqlManagement** | Beheer verkeer voor SQL-specifieke implementaties. | Beide | Nee | Ja |
| **Opslag** | Azure Storage. <br/><br/>*Opmerking:* Deze tag vertegenwoordigt de service, maar geen specifieke exemplaren van de service. De tag vertegenwoordigt bijvoorbeeld de service Azure Storage, maar geen specifiek Azure Storage-account. | Uitgaand | Ja | Ja |
| **VirtualNetwork** | De adres ruimte van het virtuele netwerk (alle IP-adresbereiken die zijn gedefinieerd voor het virtuele netwerk), alle verbonden on-premises adres ruimten, [peered](virtual-network-peering-overview.md) virtuele netwerken, virtuele netwerken die zijn verbonden met een [virtuele netwerk gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), het [virtuele IP-adres van de host](security-overview.md#azure-platform-considerations)en adres voorvoegsels die worden gebruikt voor door de [gebruiker gedefinieerde routes](virtual-networks-udr-overview.md). Deze tag kan ook standaard routes bevatten. | Beide | Nee | Nee |

>[!NOTE]
>In het klassieke implementatie model (vóór Azure Resource Manager) wordt een subset van de tags die in de vorige tabel worden weer gegeven, ondersteund. Deze tags zijn anders gespeld:
>
>| Klassieke spelling | Gelijkwaardige Resource Manager-tag |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Service tags van Azure Services duiden op de adres voorvoegsels van de specifieke Cloud die wordt gebruikt. Het onderliggende IP-bereik dat overeenkomt met de **SQL** -code waarde in de open bare Azure-Cloud, verschilt bijvoorbeeld van de onderliggende bereiken in de Azure China-Cloud.

> [!NOTE]
> Als u een [service-eindpunt voor een virtueel netwerk](virtual-network-service-endpoints-overview.md) voor een service als Azure Storage of Azure SQL Database implementeert, wordt een [route](virtual-networks-udr-overview.md#optional-default-routes) naar een virtueel netwerksubnet voor de service toegevoegd. De adres voorvoegsels in de route zijn dezelfde adres voorvoegsels of CIDR-bereiken als die van de bijbehorende servicetag.

## <a name="service-tags-on-premises"></a>On-premises service Tags  
U kunt de huidige informatie over de servicetag en het bereik ophalen om op te nemen als onderdeel van uw on-premises firewall configuraties. Deze informatie is de huidige tijdgebonden lijst met IP-bereiken die overeenkomen met elke servicetag. U kunt de informatie via programma code of via het downloaden van een JSON-bestand verkrijgen, zoals wordt beschreven in de volgende secties.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>De service label detectie-API (open bare preview) gebruiken
U kunt de huidige lijst met Service Tags op een programmatische manier ophalen in combi natie met details van IP-adres bereik:

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure-CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> In de open bare preview-versie kan de detectie-API informatie retour neren die minder actueel is dan gegevens die door de JSON-down loads worden geretourneerd. (Zie de volgende sectie.)


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Service Tags detecteren met behulp van Download bare JSON-bestanden 
U kunt JSON-bestanden downloaden die de huidige lijst met Service Tags samen met IP-adres bereik gegevens bevatten. Deze lijsten worden wekelijks bijgewerkt en gepubliceerd. Locaties voor elke Cloud zijn:

- [Open bare Azure](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure van de Amerikaanse overheid](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure China](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Duitsland](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>Een subset van deze gegevens is gepubliceerd in XML-bestanden voor [Azure Public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure China](https://www.microsoft.com/download/details.aspx?id=42064)en [Azure Duitsland](https://www.microsoft.com/download/details.aspx?id=54770). Deze XML-down loads worden vóór 30 juni 2020 verouderd en zijn na die datum niet meer beschikbaar. U moet migreren naar met behulp van de detectie-API of JSON-bestand downloads, zoals beschreven in de vorige secties.

### <a name="tips"></a>Tips 
- U kunt updates van de ene publicatie naar de volgende detecteren door verhoogde *changeNumber* -waarden in het JSON-bestand op te nemen. Elke subsectie (bijvoorbeeld **opslag. westus**) heeft een eigen *changeNumber* die wordt verhoogd wanneer er wijzigingen optreden. Het hoogste niveau van de *changeNumber* van het bestand wordt verhoogd wanneer een van de subsecties wordt gewijzigd.
- Voor voor beelden van het parseren van de servicetag gegevens (bijvoorbeeld het ophalen van alle adresbereiken voor opslag in Westus), raadpleegt u de Help-documentatie voor de [service label detectie-API](https://aka.ms/discoveryapi_powershell) .

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [maken van een netwerk beveiligings groep](tutorial-filter-network-traffic.md).
