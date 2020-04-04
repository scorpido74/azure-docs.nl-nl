---
title: Overzicht van Azure-servicetags
titlesuffix: Azure Virtual Network
description: Meer informatie over servicetags. Servicetags helpen de complexiteit van het maken van beveiligingsregels te minimaliseren.
services: virtual-network
documentationcenter: na
author: jispar
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2020
ms.author: jispar
ms.reviewer: kumud
ms.openlocfilehash: 7da20a0bf87b33f05ea7f1d457157c5b7ee2ec7b
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/03/2020
ms.locfileid: "80631515"
---
# <a name="virtual-network-service-tags"></a>Tags voor virtuele netwerkservice
<a name="network-service-tags"></a>

Een servicetag vertegenwoordigt een groep IP-adresvoorvoegsels van een bepaalde Azure-service. Microsoft beheert de adresvoorvoegsels die zijn gebruikt door de servicetag en werkt de servicetag automatisch bij wanneer adressen veranderen, waardoor de complexiteit van frequente updates voor netwerkbeveiligingsregels wordt geminimaliseerd.

U servicetags gebruiken om netwerktoegangsbesturingselementen te definiëren in [netwerkbeveiligingsgroepen](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules) of [Azure Firewall.](https://docs.microsoft.com/azure/firewall/service-tags) Gebruik servicetags in plaats van specifieke IP-adressen wanneer u beveiligingsregels maakt. Door de naam van de servicetag (bijvoorbeeld **ApiManagement)** op te geven in het juiste *bron-* of *bestemmingsveld* van een regel, u het verkeer voor de desbetreffende service toestaan of weigeren.

U servicetags gebruiken om netwerkisolatie te bereiken en uw Azure-bronnen te beschermen tegen het algemene internet terwijl u toegang hebt tot Azure-services met openbare eindpunten. Maak regels voor binnenkomende/uitgaande netwerkbeveiligingsgroepen om verkeer van/naar **internet** te weigeren en verkeer van/naar **AzureCloud** of andere [beschikbare servicetags](#available-service-tags) van specifieke Azure-services toe te staan.

## <a name="available-service-tags"></a>Beschikbare servicetags
In de volgende tabel vindt u alle servicetags die beschikbaar zijn voor gebruik in de regels [voor netwerkbeveiligingsgroepen.](https://docs.microsoft.com/azure/virtual-network/security-overview#security-rules)

De kolommen geven aan of de tag:

- Is geschikt voor regels die betrekking hebben op inkomend of uitgaand verkeer.
- Ondersteunt [regionale](https://azure.microsoft.com/regions) scope.
- Is bruikbaar in [Azure Firewall-regels.](https://docs.microsoft.com/azure/firewall/service-tags)

Standaard geven servicetags de bereiken voor de hele cloud weer. Sommige servicetags bieden ook meer gedetailleerde controle mogelijk door de bijbehorende IP-bereiken te beperken tot een bepaald gebied. De servicetag **Storage** vertegenwoordigt bijvoorbeeld Azure Storage voor de hele cloud, maar **Storage.WestUS** beperkt het bereik tot alleen het ip-adres van de opslag en strekt zich uit tot het WestUS-gebied. In de volgende tabel wordt aangegeven of elke servicetag een dergelijk regionaal bereik ondersteunt.  

| Label | Doel | Kan u in-komende of uitgaande gebruiken? | Kan regionaal zijn? | Kan u gebruik maken van Azure Firewall? |
| --- | -------- |:---:|:---:|:---:|:---:|:---:|:---:|:---:|
| **Actiegroep** | Actiegroep. | Inkomend | Nee | Nee |
| **ApiManagement** | Beheerverkeer voor azure API Management-dedicated implementaties. <br/><br/>*Let op:* Deze tag vertegenwoordigt het eindpunt van de Azure API Management-service voor besturingsvlak per regio. Hierdoor kunnen klanten beheerbewerkingen uitvoeren op de API's, Operations, Policies, NamedValues geconfigureerd op de API Management-service.  | Inkomend | Ja | Ja |
| **Beschikbaarheid van applicatie-inzichten** | Beschikbaarheid van applicatie-inzichten. | Inkomend | Nee | Nee |
| **AppConfiguratie** | App-configuratie. | Uitgaand | Nee | Nee |
| **AppService**    | Azure App-service. Deze tag wordt aanbevolen voor uitgaande beveiligingsregels aan de voorkant van de web-app. | Uitgaand | Ja | Ja |
| **AppServiceBeheer** | Beheerverkeer voor implementaties die zijn gewijd aan de app-serviceomgeving. | Beide | Nee | Ja |
| **AzureActiveDirectory** | Azure Active Directory. | Uitgaand | Nee | Ja |
| **AzureActiveDirectoryDomainServices** | Beheerverkeer voor implementaties die zijn gewijd aan Azure Active Directory Domain Services. | Beide | Nee | Ja |
| **AzureAdvancedThreatProtection** | Azure Advanced Threat Protection. | Uitgaand | Nee | Nee |
| **AzureBackup** |Azure Backup.<br/><br/>*Let op:* Deze tag is afhankelijk van de **tags Storage en** **AzureActiveDirectory.** | Uitgaand | Nee | Ja |
| **AzureBotService** | Azure Bot-service. | Uitgaand | Nee | Nee |
| **AzureCloud** | Alle [openbare IP-adressen van datacenters](https://www.microsoft.com/download/details.aspx?id=56519). | Uitgaand | Ja | Ja |
| **AzureCognitiveSearch** | Azure Cognitive Search. <br/><br/>Deze tag of de IP-adressen waarop deze tag betrekking heeft, kunnen worden gebruikt om indexers veilige toegang tot gegevensbronnen te verlenen. Raadpleeg de documentatie van de [indexerverbinding](https://docs.microsoft.com/azure/search/search-indexer-troubleshooting#connection-errors) voor meer informatie. <br/><br/> *Opmerking:* Het IP van de zoekservice is niet opgenomen in de lijst met IP-bereiken voor deze servicetag en **moet ook worden toegevoegd aan** de IP-firewall van gegevensbronnen. | Inkomend | Nee | Nee |
| **AzureConnectors** | Azure Logic Apps-connectors voor probe-/back-endverbindingen. | Inkomend | Ja | Ja |
| **AzureContainerRegistry AzureContainerRegistry** | Azure-containerregister. | Uitgaand | Ja | Ja |
| **AzureCosmosDB** | Azure Cosmos DB. | Uitgaand | Ja | Ja |
| **AzureDatabricks** | Azure Databricks. | Beide | Nee | Nee |
| **AzureDataExplorerBeheer** | Azure Data Explorer-beheer. | Inkomend | Nee | Nee |
| **AzureDataLake** | Azure Data Lake Storage Gen1. | Uitgaand | Nee | Ja |
| **AzureDevSpaces** | Azure Dev Spaces. | Uitgaand | Nee | Nee |
| **AzureEventGrid** | Azure-gebeurtenisraster. <br/><br/>*Let op:* Deze tag heeft alleen betrekking op Azure Event Grid-eindpunten in US South Central, US East, US East 2, US West 2 en US Central. | Beide | Nee | Nee |
| **AzureFrontDoor.Frontend** <br/> **AzureFrontDoor.Backend** <br/> **AzureFrontDoor.FirstParty**  | Azure voordeur. | Beide | Nee | Nee |
| **AzureInformationProtection** | Azure-informatiebeveiliging.<br/><br/>*Let op:* Deze tag is afhankelijk van de **AzureActiveDirectory,AzureFrontDoor.Frontend-** en **AzureFrontDoor.FirstParty-tags.** **AzureActiveDirectory** | Uitgaand | Nee | Nee |
| **AzureIoTHub** | Azure IoT Hub. | Uitgaand | Nee | Nee |
| **AzureKeyVault** | Azure Key Vault.<br/><br/>*Let op:* Deze tag is afhankelijk van de **AzureActiveDirectory-tag.** | Uitgaand | Ja | Ja |
| **AzureLoadBalancer** | De Azure-infrastructuurloadbalancer. De tag vertaalt naar het [virtuele IP-adres van de host](security-overview.md#azure-platform-considerations) (168.63.129.16) waar de Azure-statussondes vandaan komen. Dit omvat geen verkeer naar uw Azure Load Balancer-bron. Als u Azure Load Balancer niet gebruikt, u deze regel overschrijven. | Beide | Nee | Nee |
| **AzureMachineLearning** | Azure Machine Learning. | Beide | Nee | Ja |
| **AzureMonitor** | Log Analytics, Application Insights, AzMon en aangepaste metrics (GiG-eindpunten).<br/><br/>*Let op:* Voor Log Analytics is deze tag afhankelijk van de **opslagtag.** | Uitgaand | Nee | Ja |
| **AzureOpenDatasets** | Azure Open-gegevenssets.<br/><br/>*Let op:* Deze tag is afhankelijk van de **AzureFrontDoor.Frontend-** en **Storage-tag.** | Uitgaand | Nee | Nee |
| **AzurePlatformDNS AzurePlatformDNS** | De basisinfrastructuur (standaard) DNS-service.<br/><br>U deze tag gebruiken om de standaard DNS uit te schakelen. Wees voorzichtig wanneer u deze tag gebruikt. We raden u aan de overwegingen van [azure-platforms te](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)lezen. We raden u ook aan om tests uit te voeren voordat u deze tag gebruikt. | Uitgaand | Nee | Nee |
| **AzurePlatformIMDS** | Azure Instance Metadata Service (IMDS), een basisinfrastructuurservice.<br/><br/>U deze tag gebruiken om de standaard IMDS uit te schakelen. Wees voorzichtig wanneer u deze tag gebruikt. We raden u aan de overwegingen van [azure-platforms te](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)lezen. We raden u ook aan om tests uit te voeren voordat u deze tag gebruikt. | Uitgaand | Nee | Nee |
| **AzurePlatformLKM** | Windows-licentie- of sleutelbeheerservice.<br/><br/>U deze tag gebruiken om de standaardinstellingen voor licenties uit te schakelen. Wees voorzichtig wanneer u deze tag gebruikt. We raden u aan de overwegingen van [azure-platforms te](https://docs.microsoft.com/azure/virtual-network/security-overview#azure-platform-considerations)lezen.  We raden u ook aan om tests uit te voeren voordat u deze tag gebruikt. | Uitgaand | Nee | Nee |
| **AzureResourceManager** | Azure Resource Manager. | Uitgaand | Nee | Nee |
| **AzureSignalR** | Azure SignalR. | Uitgaand | Nee | Nee |
| **AzureSiteHerstel** | Azure-siteherstel.<br/><br/>*Let op:* Deze tag is afhankelijk van de **azureactivedirectory-** en **AzureKeyVault-,** **EventHub-,****GuestAndHybridManagement-** en **opslagtags.** | Uitgaand | Nee | Nee |
| **AzureTrafficManager** | IP-adressen van Azure Traffic Manager-sonde.<br/><br/>Zie [Veelgestelde vragen](https://docs.microsoft.com/azure/traffic-manager/traffic-manager-faqs)over Azure Traffic Manager voor meer informatie over IP-adressen van Traffic Manager probe. | Inkomend | Nee | Ja |  
| **BatchNodeManagement** | Beheerverkeer voor implementaties die zijn gewijd aan Azure Batch. | Beide | Nee | Ja |
| **CognitiveServicesManagement** | De adresbereiken voor verkeer voor Azure Cognitive Services. | Beide | Nee | Nee |
| **Datafactory**  | Azure Data Factory | Beide | Nee | Nee |
| **DataFactoryManagement** | Beheerverkeer voor Azure Data Factory. | Uitgaand | Nee | Nee |
| **Dynamics365MarketingE-mail** | Het adres varieert voor de marketing e-mailservice van Dynamics 365. | Uitgaand | Ja | Nee |
| **ElasticAFD** | Elastische azure voordeur. | Beide | Nee | Nee |
| **EventHub** | Azure-gebeurtenishubs. | Uitgaand | Ja | Ja |
| **GatewayManager** | Beheerverkeer voor implementaties die zijn gewijd aan Azure VPN Gateway en Application Gateway. | Inkomend | Nee | Nee |
| **GuestAndHybridManagement** | Azure-automatisering en gastconfiguratie. | Uitgaand | Nee | Ja |
| **HDInsight** | Azure HDInsight. | Inkomend | Ja | Nee |
| **Internet** | De IP-adresruimte die buiten het virtuele netwerk valt en bereikbaar is via het openbare internet.<br/><br/>Het adresbereik omvat de [openbare IP-adresruimte die eigendom is van Azure.](https://www.microsoft.com/download/details.aspx?id=41653) | Beide | Nee | Nee |
| **LogicApps** | Logische apps. | Beide | Nee | Nee |
| **LogicAppsManagement** | Beheerverkeer voor Logic Apps. | Inkomend | Nee | Nee |
| **MicrosoftCloudAppSecurity** | Beveiliging van Microsoft Cloud-apps. | Uitgaand | Nee | Nee |
| **MicrosoftContainerRegistry** | Containerregister voor Microsoft-containerafbeeldingen. <br/><br/>*Let op:* Deze tag is afhankelijk van de **AzureFrontDoor.FirstParty-tag.** | Uitgaand | Ja | Ja |
| **PowerQueryOnline** | Power Query Online. | Beide | Nee | Nee |
| **ServiceBus** | Azure Service Bus-verkeer dat de Premium-servicelaag gebruikt. | Uitgaand | Ja | Ja |
| **ServiceFabric (ServiceFabric)** | Azure-servicestructuur.<br/><br/>*Let op:* This tag represents the Service Fabric service endpoint for control plane per region. Dit stelt klanten in staat om beheerbewerkingen uit te voeren voor hun Service Fabric-clusters vanuit hun VNET (endpoint bv. https:// westus.servicefabric.azure.com) | Beide | Nee | Nee |
| **Sql** | Azure SQL Database, Azure Database voor MySQL, Azure Database voor PostgreSQL en Azure SQL Data Warehouse.<br/><br/>*Let op:* Deze tag vertegenwoordigt de service, maar niet specifieke exemplaren van de service. De tag vertegenwoordigt bijvoorbeeld de service Azure SQL Database, maar geen specifieke SQL-database of -server. Deze tag is niet van toepassing op SQL-beheerde instantie. | Uitgaand | Ja | Ja |
| **SqlManagement SqlManagement** | Beheerverkeer voor SQL-specifieke implementaties. | Beide | Nee | Ja |
| **Opslag** | Azure-opslag. <br/><br/>*Let op:* Deze tag vertegenwoordigt de service, maar niet specifieke exemplaren van de service. De tag vertegenwoordigt bijvoorbeeld de service Azure Storage, maar geen specifiek Azure Storage-account. | Uitgaand | Ja | Ja |
| **StorageSyncService** | Opslagsynchronisatieservice. | Beide | Nee | Nee |
| **WindowsVirtualDesktop** | Windows Virtueel bureaublad. | Beide | Nee | Nee |
| **Virtueel netwerk** | De virtuele netwerkadresruimte (alle IP-adresbereiken gedefinieerd voor het virtuele netwerk), alle verbonden on-premises adresruimten, [peered](virtual-network-peering-overview.md) virtuele netwerken, virtuele netwerken die zijn aangesloten op een [virtuele netwerkgateway,](../vpn-gateway/vpn-gateway-about-vpngateways.md?toc=%2fazure%2fvirtual-network%3ftoc.json)het [virtuele IP-adres van de host](security-overview.md#azure-platform-considerations)en adresvoorvoegsels die worden gebruikt op door de gebruiker gedefinieerde [routes](virtual-networks-udr-overview.md). Deze tag kan ook standaardroutes bevatten. | Beide | Nee | Nee |

>[!NOTE]
>In het klassieke implementatiemodel (vóór Azure Resource Manager) wordt een subset van de tags in de vorige tabel ondersteund. Deze tags zijn anders gespeld:
>
>| Klassieke spelling | Gelijkwaardige resourcebeheertag |
>|---|---|
>| AZURE_LOADBALANCER | AzureLoadBalancer |
>| INTERNET | Internet |
>| VIRTUAL_NETWORK | VirtualNetwork |

> [!NOTE]
> Servicetags van Azure-services geven de adresvoorvoegsels aan uit de specifieke cloud die wordt gebruikt. De onderliggende IP-bereiken die overeenkomen met de **Sql-tagwaarde** in de Azure Public-cloud, verschillen bijvoorbeeld van de onderliggende bereiken in de Azure China-cloud.

> [!NOTE]
> Als u een eindpunt voor [virtuele netwerkservice](virtual-network-service-endpoints-overview.md) implementeert voor een service, zoals Azure Storage of Azure SQL Database, voegt Azure een [route](virtual-networks-udr-overview.md#optional-default-routes) toe naar een subnet voor virtuele netwerken voor de service. De adresvoorvoegsels in de route zijn dezelfde adresvoorvoegsels of CIDR-bereiken als die van de bijbehorende servicetag.

## <a name="service-tags-on-premises"></a>Servicetags on-premises  
U de huidige servicetag- en bereikgegevens verkrijgen die u opnemen als onderdeel van uw on-premises firewallconfiguraties. Deze informatie is de huidige point-in-time lijst van de IP-bereiken die overeenkomen met elke servicetag. U de informatie programmatisch of via een JSON-bestand downloaden, zoals beschreven in de volgende secties.

### <a name="use-the-service-tag-discovery-api-public-preview"></a>De API voor detectie van servicetags gebruiken (openbare voorbeeld)
U de huidige lijst met servicetags programmatisch ophalen, samen met ip-adresbereikgegevens:

- [REST](https://docs.microsoft.com/rest/api/virtualnetwork/servicetags/list)
- [Azure PowerShell](https://docs.microsoft.com/powershell/module/az.network/Get-AzNetworkServiceTag?view=azps-2.8.0&viewFallbackFrom=azps-2.3.2)
- [Azure-CLI](https://docs.microsoft.com/cli/azure/network?view=azure-cli-latest#az-network-list-service-tags)

> [!NOTE]
> Terwijl deze in een openbare preview staat, kan de Discovery API informatie retourneren die minder actueel is dan informatie die wordt geretourneerd door de JSON-downloads. (Zie de volgende sectie.)


### <a name="discover-service-tags-by-using-downloadable-json-files"></a>Ontdek servicetags met behulp van downloadbare JSON-bestanden 
U JSON-bestanden downloaden die de huidige lijst met servicetags bevatten, samen met ip-adresbereikgegevens. Deze lijsten worden wekelijks bijgewerkt en gepubliceerd. Locaties voor elke cloud zijn:

- [Azure-openbaar](https://www.microsoft.com/download/details.aspx?id=56519)
- [Azure Amerikaanse overheid](https://www.microsoft.com/download/details.aspx?id=57063)  
- [Azure China](https://www.microsoft.com/download/details.aspx?id=57062) 
- [Azure Duitsland](https://www.microsoft.com/download/details.aspx?id=57064)   

> [!NOTE]
>Een subset van deze informatie is gepubliceerd in XML-bestanden voor [Azure Public](https://www.microsoft.com/download/details.aspx?id=41653), [Azure China](https://www.microsoft.com/download/details.aspx?id=42064)en Azure [Germany](https://www.microsoft.com/download/details.aspx?id=54770). Deze XML-downloads worden voor 30 juni 2020 afgeschaft en zijn na die datum niet meer beschikbaar. U moet migreren naar het gebruik van de Discovery API of JSON-bestandsdownloads zoals beschreven in de vorige secties.

### <a name="tips"></a>Tips 
- U updates van de ene publicatie naar de volgende detecteren door te noteren dat de waarden *van het* AANTAL wijzigingen in het JSON-bestand worden gewijzigd. Elke onderafdeling (bijvoorbeeld **Storage.WestUS)** heeft zijn eigen *wijzigingAantal* dat wordt verhoogd naarmate er wijzigingen optreden. Het hoogste niveau van de wijziging van het *bestandAantal* wordt verhoogd wanneer een van de onderafdelingen wordt gewijzigd.
- Zie bijvoorbeeld de PowerShell-documentatie van [Service Tag Discovery API](https://aka.ms/discoveryapi_powershell) voor het ontleden van de servicetag (bijvoorbeeld alle adresbereiken voor opslag in WestUS).

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [maken van een netwerkbeveiligingsgroep](tutorial-filter-network-traffic.md).
