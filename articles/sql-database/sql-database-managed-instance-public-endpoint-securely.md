---
title: Beheerde beheerde objecteindpunten beveiligen
description: Openbare eindpunten veilig gebruiken in Azure met een beheerde instantie
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.topic: conceptual
author: srdan-bozovic-msft
ms.author: srbozovi
ms.reviewer: vanto, carlrab
ms.date: 05/08/2019
ms.openlocfilehash: f11c19ba33ee2fbae0fef265371bedad2fe29cb7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684954"
---
# <a name="use-an-azure-sql-database-managed-instance-securely-with-public-endpoints"></a>Een Azure SQL Database-beheerde instantie veilig gebruiken met openbare eindpunten

Azure SQL Database beheerde exemplaren kunnen gebruikersconnectiviteit bieden via [openbare eindpunten.](../virtual-network/virtual-network-service-endpoints-overview.md) In dit artikel wordt uitgelegd hoe u deze configuratie veiliger maken.

## <a name="scenarios"></a>Scenario's

Een SQL Database-beheerde instantie biedt een privéeindpunt om connectiviteit vanuit het virtuele netwerk toe te staan. De standaardoptie is om maximale isolatie te bieden. Er zijn echter scenario's waarin u een openbare eindpuntverbinding moet bieden:

- De beheerde instantie moet worden geïntegreerd met het PaaS-aanbod (multi-tenant-only platform-as-a-service).
- U hebt een hogere doorvoer van gegevensuitwisseling nodig dan mogelijk is wanneer u een VPN gebruikt.
- Het bedrijfsbeleid verbiedt PaaS binnen bedrijfsnetwerken.

## <a name="deploy-a-managed-instance-for-public-endpoint-access"></a>Een beheerde instantie implementeren voor openbare eindpunttoegang

Hoewel niet verplicht, is het algemene implementatiemodel voor een beheerde instantie met openbare eindpunttoegang het maken van de instantie in een speciaal geïsoleerd virtueel netwerk. In deze configuratie wordt het virtuele netwerk alleen gebruikt voor virtuele clusterisolatie. Het maakt niet uit of de IP-adresruimte van de beheerde instantie overlapt met de IP-adresruimte van een bedrijfsnetwerk.

## <a name="secure-data-in-motion"></a>Gegevens in beweging beveiligen

Beheerd instantiegegevensverkeer wordt altijd versleuteld als het clientstuurprogramma versleuteling ondersteunt. Gegevens die worden verzonden tussen de beheerde instantie en andere Azure-virtuele machines of Azure-services, verlaten nooit de backbone van Azure. Als er een verbinding is tussen de beheerde instantie en een on-premises netwerk, raden we u aan Azure ExpressRoute te gebruiken. ExpressRoute helpt u voorkomen dat gegevens via het openbare internet worden verplaatst. Voor beheerde bijvoorbeeld privéconnectiviteit kan alleen privé-peering worden gebruikt.

## <a name="lock-down-inbound-and-outbound-connectivity"></a>Binnenkomende en uitgaande connectiviteit vergrendelen

In het volgende diagram worden de aanbevolen beveiligingsconfiguraties weergegeven:

![Beveiligingsconfiguraties voor het vergrendelen van inkomende en uitgaande connectiviteit](media/sql-database-managed-instance-public-endpoint-securely/managed-instance-vnet.png)

Een beheerde instantie heeft een [specifiek openbaar eindpuntadres](sql-database-managed-instance-find-management-endpoint-ip-address.md). Stel dit openbare eindpunt-IP-adres in de outbound firewall aan de clientzijde en in de regels van de netwerkbeveiligingsgroep in om de uitgaande connectiviteit te beperken.

Om ervoor te zorgen dat het verkeer naar de beheerde instantie afkomstig is van vertrouwde bronnen, raden we u aan verbinding te maken via bronnen met bekende IP-adressen. Gebruik een netwerkbeveiligingsgroep om de toegang tot het beheerde openbare eindpunt van de instantie op poort 3342 te beperken.

Wanneer clients een verbinding moeten starten vanuit een on-premises netwerk, moet u ervoor zorgen dat het oorspronkelijke adres wordt vertaald naar een bekende set IP-adressen. Als u dit niet doen (bijvoorbeeld een mobiel personeelsbestand is een typisch scenario), raden we u aan [point-to-site VPN-verbindingen en een privéeindpunt te](sql-database-managed-instance-configure-p2s.md)gebruiken.

Als verbindingen vanuit Azure worden gestart, raden we aan dat verkeer afkomstig is van een bekend [toegewezen virtueel IP-adres](../virtual-network/virtual-networks-reserved-public-ip.md) (bijvoorbeeld een virtuele machine). Als u het beheren van virtuele IP-adressen (VIP)-adressen eenvoudiger wilt maken, u [openbare IP-adresvoorvoegsels](../virtual-network/public-ip-address-prefix.md)gebruiken.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het configureren van openbaar eindpunt voor beheerinstanties: [Openbaar eindpunt configureren](sql-database-managed-instance-public-endpoint-configure.md)
