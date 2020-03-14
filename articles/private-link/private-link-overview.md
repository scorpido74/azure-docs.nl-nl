---
title: Wat is Azure Private Link?
description: Overzicht van Azure-functies voor persoonlijke koppelingen, architectuur en implementatie. Meer informatie over hoe Azure private endpoints en de Azure Private Link-service werken en hoe u deze kunt gebruiken.
services: private-link
author: malopMSFT
ms.service: private-link
ms.topic: overview
ms.date: 02/27/2020
ms.author: allensu
ms.custom: fasttrack-edit
ms.openlocfilehash: 8d226b67c0b438ac726fc3abf6452db68fb10dce
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2020
ms.locfileid: "79140702"
---
# <a name="what-is-azure-private-link"></a>Wat is Azure Private Link? 
Met persoonlijke Azure-koppeling kunt u toegang krijgen tot Azure PaaS-Services (bijvoorbeeld Azure Storage en SQL Database) en Azure gehoste klanten-partner services via een [persoonlijk eind punt](private-endpoint-overview.md) in uw virtuele netwerk.

Verkeer tussen uw virtuele netwerk en de service wordt het micro soft-backbone-netwerk verplaatst. Het beschikbaar maken van uw service voor het open bare Internet is niet langer nodig. U kunt uw eigen [persoonlijke koppelings service](private-link-service-overview.md) in uw virtuele netwerk maken en deze aan uw klanten leveren. Het instellen en gebruiken van een persoonlijke koppeling van Azure is consistent voor Azure PaaS-, klanten-en Shared partner services.

> [!IMPORTANT]
> Privé-koppeling van Azure is nu algemeen beschikbaar. Zowel privé-eind punten als persoonlijke koppelings service (Service achter standaard load balancer) zijn algemeen beschikbaar. Met een andere Azure-PaaS wordt de persoonlijke Azure-koppeling op verschillende schema's uitgevoerd. Raadpleeg de sectie [Beschik baarheid](https://docs.microsoft.com/azure/private-link/private-link-overview#availability) hieronder voor een nauw keurige status van Azure PaaS op een privé-koppeling. Zie [Private endpoint](private-endpoint-overview.md#limitations) en [Private Link service](private-link-service-overview.md#limitations)voor bekende beperkingen. 

![Overzicht van privé-eind punten](media/private-link-overview/private-endpoint.png)

## <a name="key-benefits"></a>Belangrijkste voordelen
Persoonlijke Azure-koppeling biedt de volgende voor delen:  
- **Persoonlijke toegang tot services op het Azure-platform**: koppel uw virtuele netwerk aan services in azure zonder een openbaar IP-adres bij de bron of bestemming. Service providers kunnen hun services in hun eigen virtuele netwerk weer geven en consumenten hebben toegang tot deze services in hun lokale virtuele netwerk. Het persoonlijke koppelings platform zorgt voor de connectiviteit tussen de consumer en de services via het Azure-backbone netwerk. 
 
- **On-premises en peered netwerken**: Access Services die worden uitgevoerd in azure vanuit on-premises ExpressRoute persoonlijke peering, VPN-tunnels en gekoppelde virtuele netwerken met behulp van privé-eind punten. U hoeft geen open bare peering in te stellen of door te bladeren op internet om de service te bereiken. Persoonlijke koppeling biedt een veilige manier om workloads naar Azure te migreren.
 
- **Bescherming tegen gegevens lekkage**: een persoonlijk eind punt wordt toegewezen aan een instantie van een Paas-bron in plaats van de gehele service. Consumenten kunnen alleen verbinding maken met de specifieke resource. Toegang tot andere resources in de service is geblokkeerd. Dit mechanisme biedt beveiliging tegen lekkage van gegevens. 
 
- **Wereld wijd bereik**: verbinding maken tussen privé en services die in andere regio's worden uitgevoerd. Het virtuele netwerk van de consument kan zich in regio A bevinden en kan verbinding maken met Services achter een particuliere koppeling in regio B.  
 
- **Breid uw eigen services**uit: Stel dezelfde ervaring en functionaliteit in om uw service privé te maken voor consumenten in Azure. Als u uw service achter een standaard Azure Load Balancer plaatst, kunt u deze inschakelen voor privé-koppeling. De gebruiker kan vervolgens rechtstreeks verbinding maken met uw service met behulp van een persoonlijk eind punt in hun eigen virtuele netwerk. U kunt de verbindings aanvragen beheren met een goedkeurings oproep stroom. Persoonlijke Azure-koppeling werkt voor consumenten en services die deel uitmaken van verschillende Azure Active Directory-tenants. 

## <a name="availability"></a>Beschikbaarheid 
 De volgende tabel geeft een lijst van de services voor persoonlijke koppelingen en de regio's waar ze beschikbaar zijn. 

|Scenario  |Ondersteunde services  |Beschik bare regio's | Status  |
|:---------|:-------------------|:-----------------|:--------|
|Persoonlijke koppeling voor services die eigendom zijn van de klant|Persoonlijke koppelings Services achter standaard Azure Load Balancer | Alle open bare regio's  | Algemene beschikbaarheid <br/> [Meer informatie](https://docs.microsoft.com/azure/private-link/private-link-service-overview) |
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

Persoonlijke Azure-koppeling is geïntegreerd met Azure Monitor. Met deze combi natie kunt u:

 - Archiveren van logboeken naar een opslag account.
 - Streaming van gebeurtenissen naar uw event hub.
 - Azure Monitor logboek registratie.

U krijgt toegang tot de volgende informatie op Azure Monitor: 
- **Persoonlijk eind punt**: 
    - Gegevens verwerkt door het persoonlijke eind punt (IN/uit)
 
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

- [Snelstartgids: een persoonlijk eind punt maken met Azure Portal](create-private-endpoint-portal.md)
- [Snelstartgids: een persoonlijke koppelings service maken met behulp van de Azure Portal](create-private-link-service-portal.md)


 
