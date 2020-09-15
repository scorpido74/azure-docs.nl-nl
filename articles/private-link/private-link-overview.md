---
title: Wat is Azure Private Link?
description: Overzicht van Azure Private Link-functies, -architectuur en -implementatie. Meer informatie over hoe Azure privé-eindpunten en de Azure Private Link-service werken en hoe u deze kunt gebruiken.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 09/03/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 94d4a035c95b8eb2a567c71ec2172f55881e8099
ms.sourcegitcommit: de2750163a601aae0c28506ba32be067e0068c0c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/04/2020
ms.locfileid: "89488419"
---
# <a name="what-is-azure-private-link"></a>Wat is Azure Private Link? 
Met Azure Private Link hebt u via een [privé-eindpunt](private-endpoint-overview.md) in uw virtuele netwerk toegang tot Azure PaaS-services (bijvoorbeeld Azure Storage en SQL Database) en in Azure gehoste services van klanten of partners.

Verkeer tussen uw virtuele netwerk en de service wordt via het Microsoft-backbonenetwerk verplaatst. U hoeft uw service niet langer bloot te stellen aan het openbare internet. U kunt een eigen [Private Link-service](private-link-service-overview.md) maken in uw virtuele netwerk en deze aanbieden bij klanten. Het instellen en gebruiken van Azure Private Link is consistent voor Azure PaaS-services, services die eigendom zijn van klanten en gedeelde partnerservices.

> [!IMPORTANT]
> Azure Private Link is nu algemeen beschikbaar. Zowel privé-eindpunten als de Private Link-service (service achter standaard load balancer) zijn algemeen beschikbaar. Met verschillende Azure PaaS wordt de Azure Private Link volgens verschillende planningen uitgevoerd. Controleer het onderstaande gedeelte [beschikbaarheid](https://docs.microsoft.com/azure/private-link/private-link-overview#availability) voor de juiste status van Azure PaaS op Private Link. Zie [Privé-eindpunt](private-endpoint-overview.md#limitations) en [Private Link-service](private-link-service-overview.md#limitations) voor bekende beperkingen. 

![Overzicht Privé-eindpunt](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>Belangrijkste voordelen
Azure Private Link biedt de volgende voordelen:  
- **Privétoegang tot services op het Azure-platform**: Verbind uw virtuele netwerk met services in Azure zonder een openbaar IP-adres bij de bron of bestemming. Serviceproviders kunnen hun services weergeven in hun eigen virtuele netwerk en consumenten hebben toegang tot deze services in hun lokale virtuele netwerk. Het Private Link-platform zorgt voor de connectiviteit tussen de consument en de services via het Azure-backbonenetwerk. 
 
- **On-premises en peered netwerken**: Toegang tot services die worden uitgevoerd in Azure vanuit on-premises via ExpressRoute-privépeering, VPN-tunnels en gekoppelde virtuele netwerken met behulp van privé-eindpunten. U hoeft geen openbare peering in te stellen of door het internet te gaan om de service te bereiken. Private Link biedt een veilige manier om workloads naar Azure te migreren.
 
- **Bescherming tegen gegevenslekken**: Een privé-eindpunt wordt toegewezen aan een instantie van een PaaS-resource in plaats van de gehele service. Consumenten kunnen alleen verbinding maken met de specifieke resource. Toegang tot andere resources in de service is geblokkeerd. Dit mechanisme biedt beveiliging tegen risico's van gegevenslekken. 
 
- **Globaal bereik**: Maak privé verbinding met services die in andere regio's worden uitgevoerd. Het virtuele netwerk van de consument kan zich in regio A bevinden en kan verbinding maken met services achter een Private Link in regio B.  
 
- **Breid uit naar uw eigen services**: Schakel dezelfde ervaring en functionaliteit in om uw service privé te maken voor consumenten in Azure. Als u uw service achter een standaard Azure Load Balancer plaatst, kunt u deze inschakelen voor Private Link. De gebruiker kan vervolgens rechtstreeks verbinding maken met uw service met behulp van een privé-eindpunt in zijn eigen virtuele netwerk. U kunt de verbindingsaanvragen beheren met een goedkeuringsaanroepstroom. Azure Private Link werkt voor consumenten en services die deel uitmaken van verschillende Azure Active Directory-tenants. 

## <a name="availability"></a>Beschikbaarheid 
 De volgende tabel bevat de Private Link-services en de regio's waarin ze beschikbaar zijn. 

|Ondersteunde services  |Beschikbare regio's | Aanvullende overwegingen | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Private Link-services achter standaard Azure Load Balancer | Alle openbare regio's<br/> Alle Government-regio's  | Ondersteund op Standard Load Balancer | Algemene beschikbaarheid <br/> [Meer informatie](https://docs.microsoft.com/azure/private-link/private-link-service-overview) |
| Azure Blob Storage (inclusief Data Lake Storage Gen2)       |  Alle openbare regio's       |  Ondersteund op Account Kind General Purpose V2 | Algemene beschikbaarheid <br/> [Meer informatie](/azure/storage/common/storage-private-endpoints)  |
| Azure Files | Alle openbare regio's      | |   Algemene beschikbaarheid <br/> [Meer informatie](/azure/storage/files/storage-files-networking-endpoints)   |
| Azure File Sync | Alle openbare regio's      | |   Algemene beschikbaarheid <br/> [Meer informatie](/azure/storage/files/storage-sync-files-networking-endpoints)   |
| Azure Queue Storage       |  Alle openbare regio's       |  Ondersteund op Account Kind General Purpose V2 | Algemene beschikbaarheid <br/> [Meer informatie](/azure/storage/common/storage-private-endpoints)  |
| Azure Table Storage       |  Alle openbare regio's       |  Ondersteund op Account Kind General Purpose V2 | Algemene beschikbaarheid <br/> [Meer informatie](/azure/storage/common/storage-private-endpoints)  |
|  Azure SQL Database         | Alle openbare regio's <br/> Alle Government-regio's      |  Ondersteund voor [proxy-verbindingsbeleid](https://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policyhttps://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policy) | Algemene beschikbaarheid <br/> [Meer informatie](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)      |
|Azure Synapse Analytics (voorheen Azure SQL Data Warehouse)| Alle openbare regio's <br/> Alle Government-regio's |  Ondersteund voor [proxy-verbindingsbeleid](https://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policyhttps://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policy) |Algemene beschikbaarheid <br/> [Meer informatie](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)|
|Azure Cosmos DB|  Alle openbare regio's<br/> Alle Government-regio's | |Algemene beschikbaarheid <br/> [Meer informatie](https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints)|
|  Azure Database for PostgreSQL - één server         | Alle openbare regio's <br/> Alle Government-regio's     | Prijscategorieën Ondersteund voor algemeen gebruik en Geoptimaliseerd voor geheugen | Algemene beschikbaarheid <br/> [Meer informatie](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link)      |
|  Azure Database for MySQL         | Alle openbare regio's<br/> Alle Government-regio's      |  | Algemene beschikbaarheid <br/> [Meer informatie](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)     |
|  Azure Database for MariaDB         | Alle openbare regio's<br/> Alle Government-regio's     |  | Algemene beschikbaarheid <br/> [Meer informatie](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link)      |
|  Azure Key Vault         | Alle openbare regio's<br/> Alle Government-regio's      |  | Algemene beschikbaarheid   <br/> [Meer informatie](https://docs.microsoft.com/azure/key-vault/private-link-service)   |
|Azure Kubernetes Service - Kubernetes API | Alle openbare regio's      |  | Algemene beschikbaarheid   <br/> [Meer informatie](https://docs.microsoft.com/azure/aks/private-clusters)   |
|Azure Search | Alle openbare regio's <br/> Alle Government-regio's | Ondersteund met service in de privémodus | Algemene beschikbaarheid   <br/> [Meer informatie](https://docs.microsoft.com/azure/search/search-security-overview#endpoint-access)    |
|Azure Container Registry | Alle openbare regio's<br/> Alle Government-regio's    | Ondersteund met de Premium-laag van Container Registry. [Klikken voor lagen](https://docs.microsoft.com/azure/container-registry/container-registry-skus)| Algemene beschikbaarheid   <br/> [Meer informatie](https://docs.microsoft.com/azure/container-registry/container-registry-private-link)   |
|Azure App Configuration | Alle openbare regio's      |  | Preview   |
|Azure Backup | Alle openbare regio's<br/> Alle Government-regio's   |  | Algemene beschikbaarheid   <br/> [Meer informatie](https://docs.microsoft.com/azure/backup/private-endpoints)   |
|Azure Event Hub | Alle openbare regio's<br/>Alle Government-regio's      |   | Algemene beschikbaarheid   <br/> [Meer informatie](https://docs.microsoft.com/azure/event-hubs/private-link-service)  |
|Azure Service Bus | Alle openbare regio's<br/>Alle Government-regio's  | Ondersteund met de Premium-laag van Azure Service Bus. [Klikken voor lagen](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-premium-messaging) | Algemene beschikbaarheid   <br/> [Meer informatie](https://docs.microsoft.com/azure/service-bus-messaging/private-link-service)    |
|Azure Relay | Alle openbare regio's      |  | Preview <br/> [Meer informatie](https://docs.microsoft.com/azure/service-bus-relay/private-link-service)  |
|Azure Event Grid| Alle openbare regio's<br/> Alle Government-regio's       |  | Algemene beschikbaarheid   <br/> [Meer informatie](https://docs.microsoft.com/azure/event-grid/network-security) |
|Azure Web Apps | Alle openbare regio's      | Ondersteund met PremiumV2 Windows en Linux en Elastic Premium-functies  | Preview   <br/> [Meer informatie](https://docs.microsoft.com/azure/app-service/networking/private-endpoint)   |
|Azure Machine Learning | VS - OOST, VS - WEST 2, VS - ZUID-CENTRAAL      |  | Preview   <br/> [Meer informatie](https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link)   |
| Azure Automation  | Alle openbare regio's |  | Preview | |
| Azure IoT Hub | Alle openbare regio's    |  | Algemene beschikbaarheid   <br/> [Meer informatie](https://docs.microsoft.com/azure/iot-hub/virtual-network-support ) |
| Azure SignalR | EAST US, WEST US 2, SOUTH CENTRAL US      |  | Preview   <br/> [Meer informatie](https://aka.ms/asrs/privatelink)   |
| Azure Monitor <br/>(Log Analytics en Application Insights) | Alle openbare regio's      |  | Algemene beschikbaarheid   <br/> [Meer informatie](https://docs.microsoft.com/azure/azure-monitor/platform/private-link-security)   | 
| Azure Batch | VS - OOST, VS - WEST 2, VS - ZUID-CENTRAAL, VS - WEST-CENTRAAL, FRANKRIJK - CENTRAAL, AZIË - OOST, VK - ZUID, US GOV - VIRGINIA, US GOV - ARIZONA  | | Algemene beschikbaarheid <br/> [Meer informatie](https://docs.microsoft.com/azure/batch/private-connectivity) |

Voor recente updates kijkt u op de pagina [Azure Private Link Updates](https://azure.microsoft.com/updates/?product=private-link) (Updates voor Azure Private Link).

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

Azure Private Link kan worden geïntegreerd met Azure Monitor. Met deze combinatie kunt u:

 - Logboeken in een opslagaccount archiveren.
 - Gebeurtenissen naar uw Event Hub streamen.
 - Logboekregistratie van Azure Monitor vastleggen.

U krijgt toegang tot de volgende informatie op Azure Monitor: 
- **Privé-eindpunt**: 
    - Gegevens verwerkt door het privé-eindpunt (IN/UIT)
 
- **Private Link-service**:
    - Gegevens verwerkt door de Private Link-service (IN/UIT)
    - Beschikbaarheid NAT-poort  
 
## <a name="pricing"></a>Prijzen   
Zie [prijzen van Azure Private Link](https://azure.microsoft.com/pricing/details/private-link/) voor meer informatie over prijzen.
 
## <a name="faqs"></a>Veelgestelde vragen  
Zie [Veelgestelde vragen over Azure Private Link](private-link-faq.md) voor veelgestelde vragen.
 
## <a name="limits"></a>Limieten  
Zie [limieten van Azure Private Link](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits) voor limieten.

## <a name="service-level-agreement"></a>Service Level Agreement
Zie [SLA voor Azure Private Link](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/) voor SLA.

## <a name="next-steps"></a>Volgende stappen

- [Snelstart: Een privé-eindpunt maken met behulp van de Azure-portal](create-private-endpoint-portal.md)
- [Snelstart: Een Private Link-service maken met behulp van de Azure-portal](create-private-link-service-portal.md)


 
