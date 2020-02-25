---
title: Wat is Azure Private Link?
description: Informatie over het gebruik van een persoonlijke Azure-koppeling voor toegang tot Azure PaaS-Services (bijvoorbeeld Azure Storage en SQL Database) en Azure gehoste klanten/partner services via een persoonlijk eind punt in uw virtuele netwerk.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 01/09/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: aea424d4e74f0744f5891a0d7b3b08008fa227b5
ms.sourcegitcommit: dd3db8d8d31d0ebd3e34c34b4636af2e7540bd20
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/22/2020
ms.locfileid: "77562034"
---
# <a name="what-is-azure-private-link"></a>Wat is Azure Private Link? 
Met Azure private link kunt u toegang krijgen tot Azure PaaS-Services (bijvoorbeeld Azure Storage, Azure Cosmos DB en SQL Database) en Azure hostende klanten/partner services via een [persoonlijk eind punt](private-endpoint-overview.md) in uw virtuele netwerk. Verkeer tussen uw virtuele netwerk en de services wordt via het backbonenetwerk van Microsoft geleid, waarmee de risico's van het openbare internet worden vermeden. U kunt ook uw eigen [privé koppelings service](private-link-service-overview.md) maken in uw virtuele netwerk (VNet) en deze privé leveren aan uw klanten. De installatie en consumptie-ervaring met behulp van persoonlijke Azure-koppeling is consistent voor Azure PaaS-, klanten-en Shared partner services.

> [!IMPORTANT]
> Privé-koppeling van Azure is nu algemeen beschikbaar. Zowel privé-eind punten als persoonlijke koppelings service (Service achter standaard load balancer) zijn algemeen beschikbaar. Met een andere Azure-PaaS wordt de persoonlijke Azure-koppeling op verschillende schema's uitgevoerd. Raadpleeg de sectie [Beschik baarheid](https://docs.microsoft.com/azure/private-link/private-link-overview#availability) hieronder voor een nauw keurige status van Azure PaaS op een privé-koppeling. Zie [Private endpoint](private-endpoint-overview.md#limitations) en [Private Link service](private-link-service-overview.md#limitations)voor bekende beperkingen. 

![Overzicht van privé-eind punten](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>Belangrijkste voordelen
Persoonlijke Azure-koppeling biedt de volgende voor delen:  
- **Privé toegang tot services op het Azure-platform**: Verbind uw virtuele netwerk met services die worden uitgevoerd in azure, zonder dat hiervoor een openbaar IP-adres op de bron of het doel nodig is. Service providers kunnen hun services persoonlijk weer geven in hun eigen virtuele netwerk en consumenten kunnen deze services privé openen in hun lokale virtuele netwerk. Het persoonlijke koppelings platform zorgt voor de connectiviteit tussen de consumer en de services via het Azure-backbone netwerk. 
 
- **On-premises en peered netwerken**: Access Services die worden uitgevoerd in azure vanuit on-premises ExpressRoute persoonlijke peering/VPN-tunnels (van on-premises) en gekoppelde virtuele netwerken met behulp van privé-eind punten. U hoeft geen open bare peering in te stellen of door te bladeren op internet om de service te bereiken. Deze mogelijkheid biedt een veilige manier om workloads naar Azure te migreren.
 
- **Bescherming tegen gegevens exfiltration**: met een persoonlijke Azure-koppeling, wordt het persoonlijke eind punt in het VNet toegewezen aan een specifiek exemplaar van de Paas-resource van de klant, in plaats van de volledige service. Het gebruik van de privé-eindpunt gebruikers kan alleen verbinding maken met de specifieke resource en niet naar een andere resource in de service. Dit in het ingebouwde mechanisme biedt beveiliging tegen gegevens exfiltration Risico's. 
 
- **Wereld wijd bereik**: verbinding maken tussen privé en services die in andere regio's worden uitgevoerd. Dit betekent dat het virtuele netwerk van de Consumer deel kan uitmaken van regio A en dat er verbinding kan worden gemaakt met Services achter de privé koppeling in regio B.  
 
- **Breid uw eigen services**uit: gebruik dezelfde ervaring en functionaliteit om uw eigen service privé te maken voor uw consumenten in Azure. Als u uw service achter een Standard Load Balancer plaatst, kunt u deze inschakelen voor privé-koppeling. De consument kan vervolgens rechtstreeks verbinding maken met uw service met behulp van een persoonlijk eind punt in hun eigen VNet. U kunt deze verbindings aanvragen beheren met een eenvoudige goedkeurings oproep stroom. Persoonlijke Azure-koppeling werkt ook voor consumenten en services die deel uitmaken van verschillende Active Directory tenants. 

## <a name="availability"></a>Beschikbaarheid 
 De volgende tabel geeft een lijst van de services voor persoonlijke koppelingen en de regio's waar ze beschikbaar zijn. 

|Scenario  |Ondersteunde services  |Beschik bare regio's | Status  |
|:---------|:-------------------|:-----------------|:--------|
|Persoonlijke koppeling voor services die eigendom zijn van de klant|Persoonlijke koppelings Services achter Standard Load Balancer | Alle open bare regio's  | Algemene beschikbaarheid <br/> [Meer informatie](https://docs.microsoft.com/azure/private-link/private-link-service-overview) |
|Persoonlijke koppeling voor Azure PaaS Services   | Azure Storage        |  Alle open bare regio's      | Preview <br/> [Meer informatie](/azure/storage/common/storage-private-endpoints)  |
|  | Azure Data Lake Storage Gen2        |  Alle open bare regio's      | Preview <br/> [Meer informatie](/azure/storage/common/storage-private-endpoints)  |
|  |  Azure SQL Database         | Alle open bare regio's      |   Preview <br/> [Meer informatie](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)      |
||Azure SQL Data Warehouse| Alle open bare regio's |Preview <br/> [Meer informatie](https://docs.microsoft.com/azure/sql-database/sql-database-private-endpoint-overview)|
||Azure Cosmos DB| West-Centraal VS, Westus, Noord-Centraal VS |Preview <br/> [Meer informatie](https://docs.microsoft.com/azure/cosmos-db/how-to-configure-private-endpoints)|
|  |  Azure Database for PostgreSQL-één server         | Alle open bare regio's      |   Preview <br/> [Meer informatie](https://docs.microsoft.com/azure/postgresql/concepts-data-access-and-security-private-link)      |
|  |  Azure Database for MySQL         | Alle open bare regio's      |   Preview <br/> [Meer informatie](https://docs.microsoft.com/azure/mysql/concepts-data-access-security-private-link)     |
|  |  Azure Database for MariaDB         | Alle open bare regio's      |   Preview <br/> [Meer informatie](https://docs.microsoft.com/azure/mariadb/concepts-data-access-security-private-link)      |
|  |  Azure Key Vault         | Alle open bare regio's      |   Preview   <br/> [Meer informatie](https://docs.microsoft.com/azure/key-vault/private-link-service)   |

Controleer de [pagina Azure Virtual Network updates](https://azure.microsoft.com/updates/?product=virtual-network)voor de meest recente meldingen.

## <a name="logging-and-monitoring"></a>Logboekregistratie en bewaking

Persoonlijke Azure-koppeling is geïntegreerd met Azure Monitor waarmee u Logboeken kunt archiveren in een opslag account, gebeurtenissen kunt streamen naar uw event hub of naar Azure Monitor-Logboeken kunt verzenden. U krijgt toegang tot de volgende informatie op Azure Monitor: 
- **Persoonlijk eind punt**: gegevens verwerkt door het persoonlijke eind punt (in/uit)
 
- **Privé koppelings service**:
    - Gegevens verwerkt door de privé koppelings service (IN/uit)
    - Beschik baarheid NAT-poort  
 
## <a name="pricing"></a>Prijzen   
Zie [prijzen voor persoonlijke Azure-koppelingen](https://azure.microsoft.com/pricing/details/private-link/)voor prijs informatie.
 
## <a name="faqs"></a>Veelgestelde vragen  
Zie [Veelgestelde vragen over Azure private link](private-link-faq.md)voor veelgestelde vragen.
 
## <a name="limits"></a>Limieten  
Zie [limieten voor persoonlijke Azure-koppelingen](../azure-resource-manager/management/azure-subscription-service-limits.md#private-link-limits)voor limieten.

## <a name="service-level-agreement"></a>Service Level Agreement
Zie [Sla voor Azure private link](https://azure.microsoft.com/support/legal/sla/private-link/v1_0/)voor sla.

## <a name="next-steps"></a>Volgende stappen
- [Een persoonlijk eind punt voor SQL Database Server maken met behulp van portal](create-private-endpoint-portal.md)
- [Een persoonlijk eind punt voor SQL Database Server maken met behulp van Power shell](create-private-endpoint-powershell.md)
- [Een persoonlijk eind punt voor SQL Database Server maken met behulp van CLI](create-private-endpoint-cli.md)
- [Een persoonlijk eind punt maken voor opslag account met behulp van portal](create-private-endpoint-storage-portal.md)
- [Een persoonlijk eind punt voor Azure Cosmos-account maken met behulp van portal](../cosmos-db/how-to-configure-private-endpoints.md)
- [Uw eigen persoonlijke koppelings service maken met behulp van Azure PowerShell](create-private-link-service-powershell.md)


 
