---
title: Netwerktopologieën voor migraties van SQL-beheerde exemplaren
titleSuffix: Azure Database Migration Service
description: Meer informatie over de bron-en doel configuraties voor de migratie van Azure SQL Database beheerde exemplaren met behulp van de Azure Database Migration Service.
services: database-migration
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 01/08/2020
ms.openlocfilehash: 48485b7ba0f846afa737454b092a6c1ee986b737
ms.sourcegitcommit: d4a4f22f41ec4b3003a22826f0530df29cf01073
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2020
ms.locfileid: "78254963"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>Netwerk topologieën voor migraties van beheerde exemplaren van Azure SQL data base met behulp van Azure Database Migration Service

In dit artikel worden verschillende netwerktopologieën beschreven waarmee Azure Database Migration Service kunnen werken om een uitgebreide migratie-ervaring te bieden van on-premises SQL-servers naar Azure SQL Database beheerde instantie.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Azure SQL Database beheerde instantie geconfigureerd voor hybride werk belastingen 

Gebruik deze topologie als uw Azure SQL Database beheerde instantie is verbonden met uw on-premises netwerk. Deze aanpak biedt de meest vereenvoudigde netwerk Routering en levert de maximale gegevens doorvoer tijdens de migratie.

![Netwerk topologie voor hybride werk belastingen](media/resource-network-topologies/hybrid-workloads.png)

**Vereisten**

- In dit scenario worden de Azure SQL Database beheerde instantie en de Azure Database Migration Service instantie gemaakt in dezelfde Microsoft Azure Virtual Network, maar ze gebruiken verschillende subnetten.  
- Het virtuele netwerk dat in dit scenario wordt gebruikt, is ook verbonden met het on-premises netwerk met behulp van [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Azure SQL Database beheerde instantie geïsoleerd van het on-premises netwerk

Gebruik deze netwerk topologie als uw omgeving een of meer van de volgende scenario's vereist:

- Het beheerde exemplaar van Azure SQL Database is geïsoleerd van on-premises connectiviteit, maar uw Azure Database Migration Service-exemplaar is verbonden met het on-premises netwerk.
- Als op rollen gebaseerd beleid voor Access Control (RBAC) is ingesteld en u de gebruikers toegang wilt geven tot hetzelfde abonnement dat als host fungeert voor het beheerde exemplaar van Azure SQL Database.
- De virtuele netwerken die worden gebruikt voor de Azure SQL Database beheerde instantie en Azure Database Migration Service bevinden zich in verschillende abonnementen.

![Netwerk topologie voor beheerde instantie die is geïsoleerd van het on-premises netwerk](media/resource-network-topologies/mi-isolated-workload.png)

**Vereisten**

- Het virtuele netwerk dat Azure Database Migration Service voor dit scenario gebruikt, moet ook worden verbonden met het on-premises netwerk met behulp van ofwel (https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).
- Stel [VNet-netwerk peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) in tussen het virtuele netwerk dat wordt gebruikt voor Azure SQL database beheerde instantie en Azure database Migration service.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>Cloud-naar-Cloud-migraties: gedeeld virtueel netwerk

Gebruik deze topologie als de bron SQL Server wordt gehost in een Azure-VM en hetzelfde virtuele netwerk deelt met Azure SQL Database beheerde instantie en Azure Database Migration Service.

![Netwerk topologie voor Cloud-naar-Cloud migraties met een gedeeld VNet](media/resource-network-topologies/cloud-to-cloud.png)

**Vereisten**

- Geen aanvullende vereisten.

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>Cloud migraties: geïsoleerd virtueel netwerk

Gebruik deze netwerk topologie als uw omgeving een of meer van de volgende scenario's vereist:

- Het beheerde exemplaar van Azure SQL Database is ingericht in een geïsoleerd virtueel netwerk.
- Als op rollen gebaseerd beleid voor Access Control (RBAC) is ingesteld en u de gebruikers toegang wilt geven tot hetzelfde abonnement dat als host fungeert voor het beheerde exemplaar van Azure SQL Database.
- De virtuele netwerken die worden gebruikt voor Azure SQL Database beheerde instantie en Azure Database Migration Service bevinden zich in verschillende abonnementen.

![Netwerk topologie voor Cloud-naar-Cloud migraties met een geïsoleerd VNet](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Vereisten**

- Stel [VNet-netwerk peering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) in tussen het virtuele netwerk dat wordt gebruikt voor Azure SQL database beheerde instantie en Azure database Migration service.

## <a name="inbound-security-rules"></a>Inkomende beveiligingsregels

| **NAAM**   | **IMPORTEER** | **PROTOCOLSUBSTATUS** | **Bron** | **BEOOGDE** | **OPTREDEN** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Alle      | Alle          | DMS-SUBNET | Alle             | Toestaan      |

## <a name="outbound-security-rules"></a>Uitgaande beveiligings regels

| **NAAM**                  | **IMPORTEER**                                              | **PROTOCOLSUBSTATUS** | **Bron** | **BEOOGDE**           | **OPTREDEN** | **Reden voor regel**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| management                | 443, 9354                                              | TCP          | Alle        | Alle                       | Toestaan      | Beheer vlak communicatie via Service Bus en Azure Blob-opslag. <br/>(Als micro soft-peering is ingeschakeld, hebt u deze regel mogelijk niet nodig.)                                                             |
| Diagnostische gegevens               | 12000                                                 | TCP          | Alle        | Alle                       | Toestaan      | DMS gebruikt deze regel voor het verzamelen van diagnostische gegevens voor het oplossen van problemen.                                                                                                                      |
| SQL-bron server         | 1433 (of TCP IP-poort waarnaar SQL Server luistert) | TCP          | Alle        | On-premises adresruimte | Toestaan      | SQL Server bron connectiviteit vanuit DMS <br/>(Als u site-naar-site-connectiviteit hebt, hebt u deze regel mogelijk niet nodig.)                                                                                       |
| SQL Server benoemd exemplaar | 1434                                                  | UDP          | Alle        | On-premises adresruimte | Toestaan      | SQL Server bron connectiviteit van een benoemde instantie van DMS <br/>(Als u site-naar-site-connectiviteit hebt, hebt u deze regel mogelijk niet nodig.)                                                                        |
| SMB-share                 | 445                                                   | TCP          | Alle        | On-premises adresruimte | Toestaan      | SMB-netwerk share voor DMS voor het opslaan van back-upbestanden van data bases voor migraties naar Azure SQL Database MI-en SQL-servers op Azure VM <br/>(Als u site-naar-site-connectiviteit hebt, hebt u deze regel mogelijk niet nodig). |
| DMS_subnet                | Alle                                                   | Alle          | Alle        | DMS_Subnet                | Toestaan      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Zie ook

- [SQL Server migreren naar Azure SQL Database beheerde instantie](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Overzicht van vereisten voor het gebruik van Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Een virtueel netwerk maken met Azure Portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Volgende stappen

- Zie het artikel [Wat is er Azure database Migration service?](dms-overview.md)voor een overzicht van Azure database Migration service.
- Zie de pagina [beschik bare producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) voor actuele informatie over de regionale Beschik baarheid van Azure database Migration service.
