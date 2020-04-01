---
title: Wat is Azure Private Link?
description: Overzicht van Azure Private Link-functies, -architectuur en -implementatie. Meer informatie over hoe Azure Private Endpoints en Azure Private Link-service werken en hoe u deze gebruiken.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 02/27/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: f75f8392b01e69760171c13c0e6d3b2e22b20dea
ms.sourcegitcommit: 7581df526837b1484de136cf6ae1560c21bf7e73
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/31/2020
ms.locfileid: "80421193"
---
# <a name="what-is-azure-private-link"></a>Wat is Azure Private Link? 
Met Azure Private Link heeft u toegang tot Azure PaaS-services (bijvoorbeeld Azure Storage en SQL Database) en Azure gehoste client-owned/partnerservices via een [privéeindpunt](private-endpoint-overview.md) in uw virtuele netwerk.

Verkeer tussen uw virtuele netwerk en de service reist het Microsoft-backbone-netwerk. Het blootstellen van uw dienst aan het openbare internet is niet langer nodig. U uw eigen [privélinkservice](private-link-service-overview.md) in uw virtuele netwerk maken en deze aan uw klanten leveren. Het instellen en verbruik met Azure Private Link is consistent in Azure PaaS, services die eigendom zijn van klanten en gedeelde partnerservices.

> [!IMPORTANT]
> Azure Private Link is nu algemeen beschikbaar. Zowel Private Endpoint als Private Link service (service achter standaard load balancer) zijn over het algemeen beschikbaar. Verschillende Azure PaaS wordt aan boord van Azure Private Link op verschillende schema's. Controleer de [beschikbaarheid](https://docs.microsoft.com/azure/private-link/private-link-overview#availability) sectie hieronder voor nauwkeurige status van Azure PaaS op Private Link. Zie [Privéeindpunt](private-endpoint-overview.md#limitations) en Private [Link Service](private-link-service-overview.md#limitations)voor bekende beperkingen. 

![Overzicht van privéeindpunten](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>Belangrijkste voordelen
Azure Private Link biedt de volgende voordelen:  
- **Services met privétoegang op het Azure-platform:** Verbind uw virtuele netwerk met services in Azure zonder een openbaar IP-adres bij de bron of bestemming. Dienstverleners kunnen hun diensten in hun eigen virtuele netwerk leveren en consumenten hebben toegang tot deze diensten in hun lokale virtuele netwerk. Het Private Link-platform verzorgt de connectiviteit tussen de consument en de services via het Azure-backbone-netwerk. 
 
- **On-premises en peered-netwerken:** Toegang tot services die in Azure worden uitgevoerd via on-premises via ExpressRoute private peering, VPN-tunnels en peered virtuele netwerken met behulp van privéeindpunten. Het is niet nodig om openbare peering in te stellen of het internet te doorkruisen om de service te bereiken. Private Link biedt een veilige manier om workloads naar Azure te migreren.
 
- **Bescherming tegen gegevenslekken**: Een privéeindpunt wordt toegewezen aan een instantie van een PaaS-bron in plaats van de hele service. Consumenten kunnen alleen verbinding maken met de specifieke bron. De toegang tot andere bronnen in de service wordt geblokkeerd. Dit mechanisme biedt bescherming tegen risico's op gegevenslekken. 
 
- **Wereldwijd bereik**: Maak privé verbinding met services die in andere regio's worden uitgevoerd. Het virtuele netwerk van de consument zou zich in regio A kunnen bevinden en het kan verbinding maken met diensten achter Private Link in regio B.  
 
- **Breid uit naar uw eigen services:** schakel dezelfde ervaring en functionaliteit in om uw service privé te renderen aan consumenten in Azure. Door uw service achter een standaard Azure Load Balancer te plaatsen, u deze inschakelen voor Private Link. De consument kan dan rechtstreeks verbinding maken met uw service via een privéeindpunt in zijn eigen virtuele netwerk. U de verbindingsaanvragen beheren met behulp van een goedkeuringsoproepstroom. Azure Private Link werkt voor consumenten en services die behoren tot verschillende Azure Active Directory-tenants. 

## <a name="availability"></a>Beschikbaarheid 
 In de volgende tabel worden de privéverbindingsservices en de regio's waar ze beschikbaar zijn weergegeven. 

|Scenario  |Ondersteunde services  |Beschikbare regio's | Status  |
|:---------|:-------------------|:-----------------|:--------|
|Private Link voor diensten die eigendom zijn van de klant |Private Link-services achter standaard Azure Load Balancer | Alle openbare regio's  | Algemene beschikbaarheid <br/> [Meer informatie](https://docs.microsoft.com/azure/private-link/private-link-service-overview) |
|Private Link voor Azure PaaS-services   | Azure Storage        |  Alle openbare regio's      | Algemene beschikbaarheid <br/> [Meer informatie](/azure/storage/common/storage-private-endpoints)  |
|  | Azure Data Lake Storage Gen2        |  Alle openbare regio's      | Algemene beschikbaarheid <br/> [Meer informatie](/azure/storage/common/storage-private-endpoints)  |
|  |  Azure SQL Database         | Alle openbare regio's      |   Algemene beschikbaarheid <br/> [Meer informatie](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)      |
|  |Azure Synapse Analytics (SQL Data Warehouse)| Alle openbare regio's |Algemene beschikbaarheid <br/> [Meer informatie](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)|
|  |Azure Cosmos DB|  Alle openbare regio's |Algemene beschikbaarheid <br/> [Meer informatie](https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints)|
|  |  Azure Database voor PostgreSQL - Enkele server         | Alle openbare regio's      |   Algemene beschikbaarheid <br/> [Meer informatie](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link)      |
|  |  Azure Database for MySQL         | Alle openbare regio's      |   Algemene beschikbaarheid <br/> [Meer informatie](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)     |
|  |  Azure Database for MariaDB         | Alle openbare regio's      |   Algemene beschikbaarheid <br/> [Meer informatie](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link)      |
|  |  Azure Key Vault         | Alle openbare regio's      |   Algemene beschikbaarheid   <br/> [Meer informatie](https://docs.microsoft.com/azure/key-vault/private-link-service)   |
|  |Azure Kubernetes-service - Kubernetes API | Alle openbare regio's      |   Algemene beschikbaarheid   <br/> [Meer informatie](https://docs.microsoft.com/azure/aks/private-clusters)   |
|  |Azure Search | OOST-VS, WEST US2, ZUID CENTRAAL VS |   Preview    |
|  |Azure Container Registry | Alle openbare regio's      |   Preview   |
|  |Azure App Configuration | Alle openbare regio's      |   Preview   |
|  |Azure Backup | OOST-VS, WEST US2, ZUID CENTRAAL VS     |   Preview   |
|  |Azure Event Hub | Alle openbare regio's      |   Preview    |
|  |Azure Service Bus | Alle openbare regio's      |   Preview   |
|  |Azure Relay | Alle openbare regio's      |   Preview   |
|  |Azure Event Grid| Alle openbare regio's      |   Preview   <br/> [Meer informatie](https://docs.microsoft.com/azure/event-grid/network-security)   |
|  |Azure Web Apps | OOST-VS, WEST US2, ZUID CENTRAAL VS      |   Preview   <br/> [Meer informatie](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)   |

Ga voor de meest actuele meldingen naar de [pagina Azure Virtual Network-updates](https://azure.microsoft.com/updates/?product=virtual-network).

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

Azure Private Link heeft integratie met Azure Monitor. Deze combinatie maakt het mogelijk:

 - Archivering van logboeken naar een opslagaccount.
 - Het streamen van evenementen naar je Event Hub.
 - Azure Monitor-logboekregistratie.

U hebt toegang tot de volgende informatie op Azure Monitor: 
- **Privé eindpunt**: 
    - Gegevens die worden verwerkt door het privéeindpunt (IN/OUT)
 
- **Private Link-service:**
    - Gegevens die worden verwerkt door de Private Link-service (IN/OUT)
    - Beschikbaarheid van NAT-poort  
 
## <a name="pricing"></a>Prijzen   
Zie [Azure Private Link-prijzen](https://azure.microsoft.com/pricing/details/private-link/)voor prijsdetails.
 
## <a name="faqs"></a>Veelgestelde vragen  
Zie [veelgestelde vragen](private-link-faq.md)van Azure Private Link voor veelgestelde vragen.
 
## <a name="limits"></a>Limieten  
Zie Azure [Private Link-limieten](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits)voor limieten voor limieten .

## <a name="service-level-agreement"></a>Servicelevel-overeenkomst
Zie SLA [voor Azure Private Link voor](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/)SLA .

## <a name="next-steps"></a>Volgende stappen

- [Snelstart: een privéeindpunt maken met Azure-portal](create-private-endpoint-portal.md)
- [Snelstart: een Private Link-service maken met behulp van de Azure-portal](create-private-link-service-portal.md)


 
