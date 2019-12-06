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
ms.openlocfilehash: 152b9f3974f24644e55bed68f5ed65faa90d7fe7
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/05/2019
ms.locfileid: "74851652"
---
# <a name="virtual-network-service-tags"></a>Service tags van virtueel netwerk 
<a name="network-service-tags"></a>

Een servicetag vertegenwoordigt een groep IP-adres voorvoegsels van een bepaalde Azure-service. Het helpt de complexiteit van regel matige updates voor netwerk beveiligings regels te minimaliseren. U kunt service tags gebruiken voor het definiëren van netwerk toegangs beheer voor [netwerk beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) of [Azure firewall](https://docs.microsoft.com/azure/firewall/service-tags). U kunt servicetags gebruiken in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de service label (bijvoorbeeld **ApiManagement**) op te geven in het juiste *bron* -of *doel* veld van een regel, kunt u het verkeer voor de bijbehorende service toestaan of weigeren. Micro soft beheert de adres voorvoegsels die zijn opgenomen in het servicetag van de service en werkt de servicetag automatisch bij met gewijzigde adressen. 

## <a name="available-service-tags"></a>Beschik bare service Tags
De volgende tabel bevat alle service tags die beschikbaar zijn voor gebruik in regels voor [netwerk beveiligings groepen](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) .

De kolommen geven aan of het label:

- Geschikt voor regels die betrekking hebben op inkomend of uitgaand verkeer
- [Regionaal](https://azure.microsoft.com/regions) bereik ondersteunen 
- Bruikbaar in [Azure firewall](https://docs.microsoft.com/azure/firewall/service-tags) -regels

Service Tags geven standaard de bereiken weer voor de hele Cloud.  Sommige service Tags bieden ook meer nauw keurige controle door de overeenkomende IP-bereiken te beperken tot een bepaalde regio.  Als de service label **opslag** bijvoorbeeld Azure Storage voor de hele Cloud, wordt de **opslag. westelijkeus** beperkt tot alleen de IP-adresbereiken van het opslag gebied uit de regio westus.  De beschrijvingen van elk servicetag hieronder geeft aan of het regionale bereik wordt ondersteund.  



| Tag | Doel | Kunt u inkomend of uitgaand gebruiken? | Kan regionaal worden? | Kunt gebruiken met Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **ApiManagement** | Beheer verkeer voor APIM speciale implementaties. | Beide | Nee | Ja |
| **AppService**    | App Service-Service. Deze tag wordt aanbevolen voor uitgaande beveiligings regels voor front-end-front-ends. | Uitgaand | Ja | Ja |
| **AppServiceManagement** | Beheer verkeer voor App Service Environment specifieke implementaties. | Beide | Nee | Ja |
| **AzureActiveDirectory** | Azure Active Directory-service. | Uitgaand | Nee | Ja |
| **AzureActiveDirectoryDomainServices** | Beheer verkeer voor Azure Active Directory Domain Services specifieke implementaties. | Beide | Nee | Ja |
| **AzureBackup** |Azure Backup-service.<br/><br/>*Opmerking:* Deze tag bevat een afhankelijkheid van de **opslag** -en **AzureActiveDirectory** -Tags. | Uitgaand | Nee | Ja |
| **Cloud** | Alle [open bare IP-adressen van data centers](https://www.microsoft.com/download/details.aspx?id=41653). | Uitgaand | Ja | Ja |
| **AzureConnectors** | Logic Apps connectors voor probe/back-end-verbindingen. | Inkomend | Ja | Ja |
| **AzureContainerRegistry** | Azure Container Registry-service. | Uitgaand | Ja | Ja |
| **AzureCosmosDB** | Azure Cosmos-database service. | Uitgaand | Ja | Ja |
| **AzureDataLake** | Azure Data Lake-service. | Uitgaand | Nee | Ja |
| **AzureHDInsight** | Azure HDInsight-service. | Inkomend | Ja | Nee |
| **AzureIoTHub** | Azure IoT Hub-service. | Uitgaand | Nee | Nee |
| **AzureKeyVault** | Azure-sleutel kluis service.<br/><br/>*Opmerking:* Deze tag bevat een afhankelijkheid van de **AzureActiveDirectory** -tag. | Uitgaand | Ja | Ja |
| **AzureLoadBalancer** | Load balancer van de infra structuur van Azure. De tag wordt omgezet in het [Virtuele IP-adres van de host](security-overview.md#azure-platform-considerations) (168.63.129.16) van waaruit statuscontroles van Azure worden uitgevoerd. Als u de load balancer van Azure niet gebruikt, kunt u deze regel onderdrukken. | Beide | Nee | Nee |
| **AzureMachineLearning** | Azure Machine Learning-service. | Uitgaand | Nee | Ja |
| **AzureMonitor** | Log Analytics, app Insights, AzMon en aangepaste metrische gegevens (GB-eind punten).<br/><br/>*Opmerking:* Voor Log Analytics heeft deze tag afhankelijk van de **opslag** label. | Uitgaand | Nee | Ja |
| **AzurePlatformDNS** | De basis-DNS-service (standaard).<br/><br>U kunt deze tag gebruiken om de standaard-DNS uit te scha kelen. Wees voorzichtig met het gebruik van deze tag. Het lezen van [aandachtspunten](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) voor het Azure-platform wordt aanbevolen. U wordt aangeraden te testen voordat u deze tag gebruikt. | Uitgaand | Nee | Nee |
| **AzurePlatformIMDS** | IMDS: dit is een basis infrastructuur service.<br/><br/>U kunt deze tag gebruiken om de standaard IMDS uit te scha kelen.  Wees voorzichtig met het gebruik van deze tag. Het lezen van [aandachtspunten](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) voor het Azure-platform wordt aanbevolen. U wordt aangeraden te testen voordat u deze tag gebruikt. | Uitgaand | Nee | Nee |
| **AzurePlatformLKM** | Windows-licentie verlening of service voor sleutel beheer.<br/><br/>U kunt deze tag gebruiken om de standaard instellingen voor licentie verlening uit te scha kelen. Wees voorzichtig met het gebruik van deze tag.  Het lezen van [aandachtspunten](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations) voor het Azure-platform wordt aanbevolen. U wordt aangeraden te testen voordat u deze tag gebruikt. | Uitgaand | Nee | Nee |
| **AzureTrafficManaged** | IP-adressen van Azure Traffic Manager test.<br/><br/>Meer informatie over de test-IP-adressen van Traffic Manager vindt u in de [Veelgestelde vragen over Azure Traffic Manager](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs). | Inkomend | Nee | Ja |  
| **BatchNodeManagement** | Beheer verkeer voor Azure Batch specifieke implementaties. | Beide | Nee | Ja |
| **CognitiveServicesManagement** | De adresbereiken voor verkeer voor Cognitive Services | Uitgaand | Nee | Nee |
| **Dynamics365ForMarketingEmail** | De adresbereiken voor de marketing-e-mail service van Dynamics 365. | Uitgaand | Ja | Nee |
| **EventHub** | Azure EventHub-service. | Uitgaand | Ja | Ja |
| **GatewayManager** | Beheer verkeer voor de eigen implementaties van VPN/app-gateways. | Inkomend | Nee | Nee |
| **Internet** | De IP-adres ruimte die zich buiten het virtuele netwerk bevindt en bereikbaar is via het open bare Internet.<br/><br/>Dit adresbereik omvat ook de [openbare IP-adresruimte van Azure](https://www.microsoft.com/download/details.aspx?id=41653). | Beide | Nee | Nee |
| **MicrosoftContainerRegistry** | Micro soft Container Registry service. | Uitgaand | Ja | Ja |
| **ServiceBus** | Azure Service Bus service met de laag Premium-Service. | Uitgaand | Ja | Ja |
| **ServiceFabric** | Service Fabric-service. | Uitgaand | Nee | Nee |
| **SQL** | Azure SQL Database, Azure Database for MySQL, Azure Database for PostgreSQL en Azure SQL Data Warehouse Services.<br/><br/>*Opmerking:* Deze tag vertegenwoordigt de service, maar geen specifieke exemplaren van de service. De tag vertegenwoordigt bijvoorbeeld de service Azure SQL Database, maar geen specifieke SQL-database of -server. | Uitgaand | Ja | Ja |
| **SqlManagement** | Beheer verkeer voor SQL-toegewezen implementaties. | Beide | Nee | Ja |
| **Storage** | Azure Storage-service. <br/><br/>*Opmerking:* De tag vertegenwoordigt de service, maar geen specifieke exemplaren van de service. De tag vertegenwoordigt bijvoorbeeld de service Azure Storage, maar geen specifiek Azure Storage-account. | Uitgaand | Ja | Ja |
| **VirtualNetwork** | De adres ruimte van het virtuele netwerk (alle IP-adresbereiken die zijn gedefinieerd voor het virtuele netwerk), alle verbonden on-premises adres ruimten, [gekoppelde virtuele netwerken](virtual-network-peering-overview.md) of virtuele netwerken die zijn verbonden met een [virtuele netwerk gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json), het [virtuele IP-adres van de host](security-overview.md#azure-platform-considerations) en adres voorvoegsels die worden gebruikt voor door de [gebruiker gedefinieerde routes](virtual-networks-udr-overview.md). Houd er rekening mee dat deze tag mogelijk ook standaard routes bevat. | Beide | Nee | Nee |

>[!NOTE]
>Wanneer u werkt in de *klassieke* omgeving (vooraf Azure Resource Manager), wordt een set van de bovenstaande tags worden ondersteund.  Deze gebruiken een alternatieve spelling:

| Klassieke spelling | Gelijkwaardige Resource Manager-tag |
|---|---|
| AZURE_LOADBALANCER | AzureLoadBalancer |
| INTERNET | Internet |
| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Met servicetags van Azure-services worden de adresvoorvoegsels aangeduid van de specifieke cloud die wordt gebruikt. bijvoorbeeld, de onderliggende IP-bereiken die overeenkomen met de **SQL** -code waarde verschillen tussen de open bare Azure-Cloud en de Azure China-Cloud.

> [!NOTE]
> Als u een [service-eindpunt voor een virtueel netwerk](virtual-network-service-endpoints-overview.md) voor een service als Azure Storage of Azure SQL Database implementeert, wordt een [route](virtual-networks-udr-overview.md#optional-default-routes) naar een virtueel netwerksubnet voor de service toegevoegd. De adresvoorvoegsels in de route zijn dezelfde adresvoorvoegsels (of CIDR-bereiken) als de bijbehorende servicetag.



## <a name="service-tags-in-on-premises"></a>Service tags in on-premises  
U kunt de huidige informatie over de servicetag en het bereik ophalen om op te nemen als onderdeel van uw on-premises firewall configuraties.  Deze informatie is de huidige tijdgebonden lijst met IP-bereiken die overeenkomen met elk service label.  De informatie kan via programma code of via het downloaden van JSON-bestanden worden verkregen.

### <a name="service-tag-discovery-api-public-preview"></a>Service Tags detectie-API (open bare preview)
U kunt de huidige lijst met Service Tags op een programmatische manier ophalen met IP-adres bereik gegevens:

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> In de open bare preview-versie kan de detectie-API informatie retour neren die niet zo actueel is als de JSON-down loads (hieronder).


### <a name="discover-service-tags-using-downloadable-json-files"></a>Service Tags detecteren met Download bare JSON-bestanden 
U kunt JSON-bestanden met de huidige lijst met Service Tags met IP-adres bereik gegevens downloaden. Deze worden wekelijks bijgewerkt en gepubliceerd.  Locaties voor elke Cloud zijn:

- [Open bare Azure](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure Government](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure China](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Duitsland](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>Een subset van deze informatie is eerder gepubliceerd in XML-bestanden voor [Azure Public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure China](https://www.microsoft.com/download/details.aspx?id=42064) en [Azure Duitsland](https://www.microsoft.com/download/details.aspx?id=54770). Deze XML-down loads worden vóór 30 juni 2020 verouderd en zijn na die datum niet meer beschikbaar. Migreer naar met behulp van de detectie-API of de down loads van het JSON-bestand zoals hierboven wordt beschreven.

### <a name="tips"></a>Tips 
- U kunt updates van één publicatie naar het volgende detecteren via meer *changeNumber* -waarden in het JSON-bestand. Elke subsectie (bijvoorbeeld **opslag. westus**) heeft een eigen *changeNumber* die wordt verhoogd wanneer er wijzigingen optreden.  Het hoogste niveau van de *changeNumber* van het bestand wordt verhoogd wanneer een van de subsecties is gewijzigd.
- Voor voor beelden van het parseren van de servicetag gegevens (bijvoorbeeld het ophalen van alle adresbereiken voor opslag in Westus), raadpleegt u de Help-documentatie voor de [service label detectie-API](https://aka.ms/discoveryapi_powershell) .

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Een netwerkbeveiligingsgroep maken](tutorial-filter-network-traffic.md).

