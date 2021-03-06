---
title: Wat is Azure Private Link?
description: Overzicht van Azure Private Link-functies, -architectuur en -implementatie. Meer informatie over hoe Azure privé-eindpunten en de Azure Private Link-service werken en hoe u deze kunt gebruiken.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 09/03/2020
ms.author: allensu
ms.custom: fasttrack-edit, references_regions
ms.openlocfilehash: 1ce0368bb86134bd52473b80eabbfaf87e36148d
ms.sourcegitcommit: 541bb46e38ce21829a056da880c1619954678586
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/11/2020
ms.locfileid: "91939644"
---
# <a name="what-is-azure-private-link"></a>Wat is Azure Private Link? 
Met Azure Private Link hebt u via een [privé-eindpunt](private-endpoint-overview.md) in uw virtuele netwerk toegang tot Azure PaaS-services (bijvoorbeeld Azure Storage en SQL Database) en in Azure gehoste services van klanten of partners.

Verkeer tussen uw virtuele netwerk en de service wordt via het Microsoft-backbonenetwerk verplaatst. U hoeft uw service niet langer bloot te stellen aan het openbare internet. U kunt een eigen [Private Link-service](private-link-service-overview.md) maken in uw virtuele netwerk en deze aanbieden bij klanten. Het instellen en gebruiken van Azure Private Link is consistent voor Azure PaaS-services, services die eigendom zijn van klanten en gedeelde partnerservices.

> [!IMPORTANT]
> Azure Private Link is nu algemeen beschikbaar. Zowel privé-eindpunten als de Private Link-service (service achter standaard load balancer) zijn algemeen beschikbaar. Met verschillende Azure PaaS wordt de Azure Private Link volgens verschillende planningen uitgevoerd. Raadpleeg het gedeelte [Beschikbaarheid](https://docs.microsoft.com/azure/private-link/private-link-overview#availability) in dit artikel voor de juiste status van Azure PaaS op Private Link. Zie [Privé-eindpunt](private-endpoint-overview.md#limitations) en [Private Link-service](private-link-service-overview.md#limitations) voor bekende beperkingen. 

## <a name="key-benefits"></a>Belangrijkste voordelen
Azure Private Link biedt de volgende voordelen:  
- **Privétoegang tot services op het Azure-platform**: Verbind uw virtuele netwerk met services in Azure zonder een openbaar IP-adres bij de bron of bestemming. Serviceproviders kunnen hun services weergeven in hun eigen virtuele netwerk en consumenten hebben toegang tot deze services in hun lokale virtuele netwerk. Het Private Link-platform zorgt voor de connectiviteit tussen de consument en de services via het Azure-backbonenetwerk. 
 
- **On-premises en peered netwerken**: Toegang tot services die worden uitgevoerd in Azure vanuit on-premises via ExpressRoute-privépeering, VPN-tunnels en gekoppelde virtuele netwerken met behulp van privé-eindpunten. U hoeft geen ExpressRoute Microsoft-peering in te stellen of door het internet te gaan om de service te bereiken. Private Link biedt een veilige manier om workloads naar Azure te migreren.
 
- **Bescherming tegen gegevenslekken**: Een privé-eindpunt wordt toegewezen aan een instantie van een PaaS-resource in plaats van de gehele service. Consumenten kunnen alleen verbinding maken met de specifieke resource. Toegang tot andere resources in de service is geblokkeerd. Dit mechanisme biedt beveiliging tegen risico's van gegevenslekken. 
 
- **Globaal bereik**: Maak privé verbinding met services die in andere regio's worden uitgevoerd. Het virtuele netwerk van de consument kan zich in regio A bevinden en kan verbinding maken met services achter een Private Link in regio B.  
 
- **Breid uit naar uw eigen services**: Schakel dezelfde ervaring en functionaliteit in om uw service privé te maken voor consumenten in Azure. Als u uw service achter een standaard Azure Load Balancer plaatst, kunt u deze inschakelen voor Private Link. De gebruiker kan vervolgens rechtstreeks verbinding maken met uw service met behulp van een privé-eindpunt in zijn eigen virtuele netwerk. U kunt de verbindingsaanvragen beheren met een goedkeuringsaanroepstroom. Azure Private Link werkt voor consumenten en services die deel uitmaken van verschillende Azure Active Directory-tenants. 

## <a name="availability"></a>Beschikbaarheid 
 De volgende tabel bevat de Private Link-services en de regio's waarin ze beschikbaar zijn. 

|Ondersteunde services  |Beschikbare regio's | Aanvullende overwegingen | Status  |
|:-------------------|:-----------------|:----------------|:--------|
|Private Link-services achter standaard Azure Load Balancer | Alle openbare regio's<br/> Alle Government-regio's<br/>Alle Chinese regio's  | Ondersteund op Standard Load Balancer | Algemene beschikbaarheid <br/> [Meer informatie over het maken van een Private Link-service.](create-private-link-service-portal.md) |
| Azure Blob Storage (inclusief Data Lake Storage Gen2)       |  Alle openbare regio's<br/> Alle Government-regio's       |  Ondersteund op Account Kind General Purpose V2 | Algemene beschikbaarheid <br/> [Meer informatie over het maken van een privé-eindpunt voor Blob Storage.](tutorial-private-endpoint-storage-portal.md)  |
| Azure Files | Alle openbare regio's<br/> Alle Government-regio's      | |   Algemene beschikbaarheid <br/> [Meer informatie over het maken van Azure Files-netwerkeindpunten.](../storage/files/storage-files-networking-endpoints.md)   |
| Azure File Sync | Alle openbare regio's      | |   Algemene beschikbaarheid <br/> [Meer informatie over het maken van Azure Files-netwerkeindpunten.](/azure/storage/files/storage-sync-files-networking-endpoints)   |
| Azure Queue Storage       |  Alle openbare regio's<br/> Alle Government-regio's       |  Ondersteund op Account Kind General Purpose V2 | Algemene beschikbaarheid <br/> [Meer informatie over het maken van een privé-eindpunt voor Queue Storage.](tutorial-private-endpoint-storage-portal.md) |
| Azure Table Storage       |  Alle openbare regio's<br/> Alle Government-regio's       |  Ondersteund op Account Kind General Purpose V2 | Algemene beschikbaarheid <br/> [Meer informatie over het maken van een privé-eindpunt voor Table Storage.](tutorial-private-endpoint-storage-portal.md)  |
|  Azure SQL Database         | Alle openbare regio's <br/> Alle Government-regio's<br/>Alle Chinese regio's      |  Ondersteund voor [proxy-verbindingsbeleid](https://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policy) | Algemene beschikbaarheid <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure SQL](create-private-endpoint-portal.md)      |
|Azure Synapse Analytics (voorheen Azure SQL Data Warehouse)| Alle openbare regio's <br/> Alle Government-regio's |  Ondersteund voor [proxy-verbindingsbeleid](https://docs.microsoft.com/azure/azure-sql/database/connectivity-architecture#connection-policy) |Algemene beschikbaarheid <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Synapse Analytics.](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)|
|Azure Cosmos DB|  Alle openbare regio's<br/> Alle Government-regio's</br> Alle Chinese regio's | |Algemene beschikbaarheid <br/> [Meer informatie over het maken van een privé-eindpunt voor Cosmos DB.](create-private-endpoint-cosmosdb-portal.md)|
|  Azure Database for PostgreSQL - één server         | Alle openbare regio's <br/> Alle Government-regio's<br/>Alle Chinese regio's     | Prijscategorieën Ondersteund voor algemeen gebruik en Geoptimaliseerd voor geheugen | Algemene beschikbaarheid <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Database for PostgreSQL.](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link)      |
|  Azure Database for MySQL         | Alle openbare regio's<br/> Alle Government-regio's<br/>Alle Chinese regio's      |  | Algemene beschikbaarheid <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Database for MySQL.](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)     |
|  Azure Database for MariaDB         | Alle openbare regio's<br/> Alle Government-regio's<br/>Alle Chinese regio's     |  | Algemene beschikbaarheid <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Database for MariaDB.](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link)      |
|  Azure Key Vault         | Alle openbare regio's<br/> Alle Government-regio's      |  | Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Key Vault.](https://docs.microsoft.com/azure/key-vault/private-link-service)   |
|Azure Kubernetes Service - Kubernetes API | Alle openbare regio's      |  | Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Kubernetes Service.](https://docs.microsoft.com/azure/aks/private-clusters)   |
|Azure Search | Alle openbare regio's <br/> Alle Government-regio's | Ondersteund met service in de privémodus | Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Search.](https://docs.microsoft.com/azure/search/service-create-private-endpoint)    |
|Azure Container Registry | Alle openbare regio's<br/> Alle Government-regio's    | Ondersteund met de Premium-laag van Container Registry. [Selecteren voor lagen](https://docs.microsoft.com/azure/container-registry/container-registry-skus)| Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Container Registry.](https://docs.microsoft.com/azure/container-registry/container-registry-private-link)   |
|Azure App Configuration | Alle openbare regio's      |  | Preview  </br> [Meer informatie over het maken van een privé-eindpunt voor Azure App Configuration](https://docs.microsoft.com/azure/azure-app-configuration/concept-private-endpoint) |
|Azure Backup | Alle openbare regio's<br/> Alle Government-regio's   |  | Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Backup.](https://docs.microsoft.com/azure/backup/private-endpoints)   |
|Azure Event Hub | Alle openbare regio's<br/>Alle Government-regio's      |   | Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Event Hub.](https://docs.microsoft.com/azure/event-hubs/private-link-service)  |
|Azure Service Bus | Alle openbare regio's<br/>Alle Government-regio's  | Ondersteund met de Premium-laag van Azure Service Bus. [Selecteren voor lagen](https://docs.microsoft.com/azure/service-bus-messaging/service-bus-premium-messaging) | Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Service Bus.](https://docs.microsoft.com/azure/service-bus-messaging/private-link-service)    |
|Azure Relay | Alle openbare regio's      |  | Preview <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Relay.](https://docs.microsoft.com/azure/azure-relay/private-link-service)  |
|Azure Event Grid| Alle openbare regio's<br/> Alle Government-regio's       |  | Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Event Grid.](https://docs.microsoft.com/azure/event-grid/network-security) |
|Azure Web Apps | Alle openbare regio's      | Ondersteund met een PremiumV2-, PremiumV3- of Function Premium-abonnement  | Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Web Apps.](https://docs.microsoft.com/azure/private-link/create-private-endpoint-webapp-portal)   |
|Azure Machine Learning | Alle openbare regio's    |  | Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Machine Learning.](https://docs.microsoft.com/azure/machine-learning/how-to-configure-private-link)   |
| Azure Automation  | Alle openbare regio's |  | Preview </br> [Meer informatie over het maken van een privé-eindpunt voor Azure Automation.](https://docs.microsoft.com/azure/automation/how-to/private-link-security)| |
| Azure IoT Hub | Alle openbare regio's    |  | Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure IoT Hub.](https://docs.microsoft.com/azure/iot-hub/virtual-network-support ) |
| Azure SignalR | VS - OOST, VS - ZUID-CENTRAAL,<br/>VS - WEST 2, alle Chinese regio's      |  | Preview   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure SignalR.](https://docs.microsoft.com/azure/azure-signalr/howto-private-endpoints)   |
| Azure Monitor <br/>(Log Analytics en Application Insights) | Alle openbare regio's      |  | Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Monitor.](https://docs.microsoft.com/azure/azure-monitor/platform/private-link-security)   | 
| Azure Batch | Alle openbare regio's, behalve: Duitsland CENTRAAL, Duitsland NOORDOOST <br/> Alle Government-regio's  | | Algemene beschikbaarheid <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Batch.](https://docs.microsoft.com/azure/batch/private-connectivity) |
|Azure Data Factory | Alle openbare regio's<br/> Alle Government-regio's<br/>Alle Chinese regio's    | Referenties moeten worden bewaard in een Azure-sleutelkluis| Algemene beschikbaarheid   <br/> [Meer informatie over het maken van een privé-eindpunt voor Azure Data Factory.](https://docs.microsoft.com/azure/data-factory/data-factory-private-link)   |



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


 
