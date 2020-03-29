---
title: Netwerktopologieën voor SQL-beheerde instantiemigraties
titleSuffix: Azure Database Migration Service
description: Meer informatie over de bron- en doelconfiguraties voor azure SQL Database beheerde instantiemigraties met behulp van de Azure Database Migration Service.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78254963"
---
# <a name="network-topologies-for-azure-sql-db-managed-instance-migrations-using-azure-database-migration-service"></a>Netwerktopologieën voor Azure SQL DB Managed Instance-migraties met Azure Database Migration Service

In dit artikel worden verschillende netwerktopologieën besproken waarmee Azure Database Migration Service kan werken om een uitgebreide migratie-ervaring te bieden van on-premises SQL Servers naar Azure SQL Database Managed Instance.

## <a name="azure-sql-database-managed-instance-configured-for-hybrid-workloads"></a>Azure SQL Database Managed Instance geconfigureerd voor hybride workloads 

Gebruik deze topologie als uw Azure SQL Database Managed Instance is verbonden met uw on-premises netwerk. Deze aanpak biedt de meest vereenvoudigde netwerkroutering en levert maximale gegevensdoorvoer op tijdens de migratie.

![Netwerktopologie voor hybride workloads](media/resource-network-topologies/hybrid-workloads.png)

**Vereisten**

- In dit scenario worden de instantie Azure SQL Database beheerd en de instantie Azure Database Migration Service gemaakt in hetzelfde Microsoft Azure Virtual Network, maar ze gebruiken verschillende subnetten.  
- Het virtuele netwerk dat in dit scenario wordt gebruikt, is ook verbonden met het on-premises netwerk door [expressroute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) of [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)te gebruiken.

## <a name="azure-sql-database-managed-instance-isolated-from-the-on-premises-network"></a>Azure SQL Database Managed Instance geïsoleerd van het on-premises netwerk

Gebruik deze netwerktopologie als uw omgeving een of meer van de volgende scenario's vereist:

- De azure SQL Database beheerde instantie is geïsoleerd van on-premises connectiviteit, maar uw Azure Database Migration Service-instantie is verbonden met het on-premises netwerk.
- Als rbac-beleid (Role Based Access Control) is ingevoerd en u de gebruikers moet beperken tot toegang tot hetzelfde abonnement dat de beheerde instantie Azure SQL Database host.
- De virtuele netwerken die worden gebruikt voor het Azure SQL Database Managed Instance en Azure Database Migration Service zijn in verschillende abonnementen.

![Netwerktopologie voor beheerde instantie geïsoleerd van het on-premises netwerk](media/resource-network-topologies/mi-isolated-workload.png)

**Vereisten**

- Het virtuele netwerk dat Azure Database Migration Service voor dit scenario gebruikt, moethttps://docs.microsoft.com/azure/expressroute/expressroute-introduction) ook worden verbonden met het on-premises netwerk met behulp van (of [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)).
- [VNet-netwerkpeering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) instellen tussen het virtuele netwerk dat wordt gebruikt voor azure SQL Database-beheerde instantie en Azure Database Migration Service.

## <a name="cloud-to-cloud-migrations-shared-virtual-network"></a>Cloud-to-cloud migraties: gedeeld virtueel netwerk

Gebruik deze topologie als de bron SQL Server wordt gehost in een Azure VM en hetzelfde virtuele netwerk deelt met Azure SQL Database managed instance en Azure Database Migration Service.

![Netwerktopologie voor cloud-to-cloudmigraties met een gedeeld VNet](media/resource-network-topologies/cloud-to-cloud.png)

**Vereisten**

- Geen aanvullende eisen.

## <a name="cloud-to-cloud-migrations-isolated-virtual-network"></a>Migraties over cloud naar cloud: geïsoleerd virtueel netwerk

Gebruik deze netwerktopologie als uw omgeving een of meer van de volgende scenario's vereist:

- De azure SQL Database beheerde instantie is ingericht in een geïsoleerd virtueel netwerk.
- Als rbac-beleid (Role Based Access Control) is ingevoerd en u de gebruikers moet beperken tot toegang tot hetzelfde abonnement dat de beheerde instantie Azure SQL Database host.
- De virtuele netwerken die worden gebruikt voor Azure SQL Database Managed Instance en Azure Database Migration Service zijn in verschillende abonnementen.

![Netwerktopologie voor cloud-to-cloudmigraties met een geïsoleerd VNet](media/resource-network-topologies/cloud-to-cloud-isolated.png)

**Vereisten**

- [VNet-netwerkpeering](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) instellen tussen het virtuele netwerk dat wordt gebruikt voor azure SQL Database-beheerde instantie en Azure Database Migration Service.

## <a name="inbound-security-rules"></a>Inkomende beveiligingsregels

| **NAAM**   | **Poort** | **Protocol** | **Bron** | **Bestemming** | **Actie** |
|------------|----------|--------------|------------|-----------------|------------|
| DMS_subnet | Alle      | Alle          | DMS-subnet | Alle             | Toestaan      |

## <a name="outbound-security-rules"></a>Uitgaande beveiligingsregels

| **NAAM**                  | **Poort**                                              | **Protocol** | **Bron** | **Bestemming**           | **Actie** | **Reden voor regel**                                                                                                                                                                              |
|---------------------------|-------------------------------------------------------|--------------|------------|---------------------------|------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| beheer                | 443,9354                                              | TCP          | Alle        | Alle                       | Toestaan      | Beheer vliegtuigcommunicatie via Service Bus en Azure blob-opslag. <br/>(Als Microsoft-peering is ingeschakeld, hebt u deze regel mogelijk niet nodig.)                                                             |
| Diagnostiek               | 12000                                                 | TCP          | Alle        | Alle                       | Toestaan      | DMS gebruikt deze regel om diagnostische informatie te verzamelen voor probleemoplossingsdoeleinden.                                                                                                                      |
| SQL Source-server         | 1433 (of TCP IP-poort waar SQL Server naar luistert) | TCP          | Alle        | On-premises adresruimte | Toestaan      | SQL Server-bronconnectiviteit van DMS <br/>(Als u verbinding hebt tussen locatie en locatie, hebt u deze regel mogelijk niet nodig.)                                                                                       |
| SQL Server met naam van instantie | 1434                                                  | UDP          | Alle        | On-premises adresruimte | Toestaan      | SQL Server named instance source connectivity from DMS <br/>(Als u verbinding hebt tussen locatie en locatie, hebt u deze regel mogelijk niet nodig.)                                                                        |
| Aandeel SMB                 | 445                                                   | TCP          | Alle        | On-premises adresruimte | Toestaan      | SMB-netwerkshare voor DMS om databaseback-upbestanden op te slaan voor migraties naar Azure SQL Database MI en SQL Servers op Azure VM <br/>(Als u site-to-site-connectiviteit hebt, hebt u deze regel mogelijk niet nodig). |
| DMS_subnet                | Alle                                                   | Alle          | Alle        | DMS_Subnet                | Toestaan      |                                                                                                                                                                                                  |

## <a name="see-also"></a>Zie ook

- [SQL Server migreren naar Azure SQL Database Managed Instance](https://docs.microsoft.com/azure/dms/tutorial-sql-server-to-managed-instance)
- [Overzicht van vereisten voor het gebruik van Azure Database Migration Service](https://docs.microsoft.com/azure/dms/pre-reqs)
- [Een virtueel netwerk maken met Azure Portal](https://docs.microsoft.com/azure/virtual-network/quick-create-portal)

## <a name="next-steps"></a>Volgende stappen

- Zie het artikel Wat is Azure [Database Migration Service voor](dms-overview.md)een overzicht van Azure Database Migration Service? .
- Zie de pagina [Producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=database-migration) voor actuele informatie over de regionale beschikbaarheid van Azure Database Migration Service.
