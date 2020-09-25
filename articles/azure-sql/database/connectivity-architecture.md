---
title: Azure SQL Database-connectiviteitsarchitectuur
description: In dit document wordt de Azure SQL Database connectiviteits architectuur voor database verbindingen vanuit Azure of van buiten Azure uitgelegd.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit, sqldbrb=1
titleSuffix: Azure SQL Database and Azure Synapse Analytics
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: sstein, vanto
ms.date: 06/26/2020
ms.openlocfilehash: 71bd250cbfb2642a291d495273c4cd66ebb2c350
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91325382"
---
# <a name="azure-sql-database-and-azure-synapse-analytics-connectivity-architecture"></a>Azure SQL Database-en Azure Synapse Analytics-connectiviteits architectuur
[!INCLUDE[appliesto-sqldb-asa](../includes/appliesto-sqldb-asa.md)]

In dit artikel wordt de architectuur van verschillende onderdelen beschreven die het netwerk verkeer naar een server in Azure SQL Database of Azure Synapse Analytics omleiden. Er wordt ook uitgelegd dat er verschillende verbindings beleidsregels zijn en hoe deze invloed heeft op clients die verbinding maken vanuit Azure en clients die verbinding maken van buiten Azure.

> [!IMPORTANT]
> Dit artikel is *niet* van toepassing op **Azure SQL Managed Instance**. Raadpleeg [de verbindings architectuur voor een beheerd exemplaar](../managed-instance/connectivity-architecture-overview.md).

## <a name="connectivity-architecture"></a>Connectiviteitsarchitectuur

Het volgende diagram biedt een overzicht van de connectiviteits architectuur op hoog niveau.

![architectuur overzicht](./media/connectivity-architecture/connectivity-overview.png)

In de volgende stappen wordt beschreven hoe een verbinding tot stand wordt gebracht Azure SQL Database:

- Clients maken verbinding met de gateway, die een openbaar IP-adres heeft en luistert op poort 1433.
- De gateway, afhankelijk van het effectief verbindings beleid, leidt het verkeer om naar het juiste database cluster.
- In het database cluster verkeer wordt doorgestuurd naar de juiste data base.

## <a name="connection-policy"></a>Verbindings beleid

Servers in SQL Database en Azure Synapse ondersteunen de volgende drie opties voor de verbindings beleids instelling van de server:

- **Omleiden (aanbevolen):** Clients maken verbinding rechtstreeks met het knoop punt dat als host fungeert voor de data base, waardoor er minder latentie is en een verbeterde door voer. Als u verbinding wilt maken met deze modus, moeten clients:
  - Uitgaande communicatie van de client naar alle Azure SQL-IP-adressen in de regio voor poorten in het bereik van 11000 11999 toestaan. Gebruik de service tags voor SQL om dit eenvoudiger te beheren.  
  - Uitgaande communicatie toestaan van de client om IP-adressen van de gateway te Azure SQL Database op poort 1433.

- **Proxy:** In deze modus worden alle verbindingen via de gateways van de Azure SQL Database verzonden, waardoor de latentie wordt verhoogd en de door Voer is gereduceerd. Voor verbindingen om deze modus te gebruiken moeten clients uitgaande communicatie toestaan van de client om IP-adressen van de gateway te Azure SQL Database op poort 1433.

- **Standaard:** Dit is het verbindings beleid dat wordt toegepast op alle servers na het maken, tenzij u het verbindings beleid expliciet wijzigt naar ofwel `Proxy` of `Redirect` . Het standaard beleid is `Redirect` voor alle client verbindingen die afkomstig zijn van Azure (bijvoorbeeld van een virtuele machine van Azure) en `Proxy` voor alle client verbindingen die afkomstig zijn van buiten (bijvoorbeeld verbindingen van uw lokale werk station).

Het is raadzaam `Redirect` om het verbindings beleid `Proxy` voor de laagste latentie en de hoogste door Voer uit te voeren via het verbindings beleid. U moet echter wel voldoen aan de aanvullende vereisten voor het toestaan van netwerk verkeer zoals hierboven wordt beschreven. Als de client een virtuele machine van Azure is, kunt u dit doen met behulp van netwerk beveiligings groepen (NSG) met [service Tags](../../virtual-network/security-overview.md#service-tags). Als de client verbinding maakt met behulp van een on-premises werk station, moet u mogelijk samen werken met uw netwerk beheerder om netwerk verkeer via uw bedrijfs firewall toe te staan.

## <a name="connectivity-from-within-azure"></a>Connectiviteit vanuit Azure

Als u verbinding maakt vanuit Azure, hebben uw verbindingen standaard een verbindings beleid van `Redirect` . Een beleid `Redirect` waarbij wordt aangegeven dat nadat de TCP-sessie is ingesteld op Azure SQL database, de client sessie vervolgens wordt omgeleid naar het juiste database cluster met een wijziging in de virtuele doel-IP van die van de Azure SQL database gateway naar die van het cluster. Daarna stroomt alle volgende pakketten rechtstreeks naar het cluster, waarbij de Azure SQL Database gateway wordt omzeild. In het volgende diagram ziet u deze verkeers stroom.

![architectuur overzicht](./media/connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Connectiviteit van buiten Azure

Als u verbinding maakt vanuit buiten Azure, hebben uw verbindingen standaard een verbindings beleid van `Proxy` . Een beleid voor `Proxy` betekent dat de TCP-sessie tot stand is gebracht via de Azure SQL database gateway en dat alle volgende pakketten via de gateway stromen. In het volgende diagram ziet u deze verkeers stroom.

![architectuur overzicht](./media/connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Daarnaast open TCP-poorten 1434 en 14000-14999 om [verbinding te maken met DAC](https://docs.microsoft.com/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators?view=sql-server-2017#connecting-with-dac)

## <a name="gateway-ip-addresses"></a>IP-adressen van Gateway

In de volgende tabel worden de IP-adressen van gateways per regio weer gegeven. Als u verbinding wilt maken met SQL Database of Azure Synapse, moet u netwerk verkeer naar en van **alle** gateways voor de regio toestaan.

Meer informatie over hoe verkeer moet worden gemigreerd naar nieuwe gateways in specifieke regio's, vindt u in het volgende artikel: [Azure SQL database verkeer migratie naar nieuwere gateways](gateway-migration.md)

| Regio naam          | IP-adressen van Gateway |
| --- | --- |
| Australië - centraal    | 20.36.105.0 |
| Australië-Central2   | 20.36.113.0 |
| Australië - oost       | 13.75.149.87, 40.79.161.1, 13.70.112.9 |
| Australië - zuidoost | 191.239.192.109, 13.73.109.251, 13.77.48.10 |
| Brazil South         | 104.41.11.5, 191.233.200.14 |
| Canada - midden       | 40.85.224.249, 52.246.152.0, 20.38.144.1 |
| Canada - oost          | 40.86.226.166, 52.242.30.154 |
| Central US           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1 |
| China East           | 139.219.130.35     |
| China - oost 2         | 40.73.82.1         |
| China - noord          | 139.219.15.17      |
| China - noord 2        | 40.73.50.0         |
| Azië - oost            | 191.234.2.139, 52.175.33.150, 13.75.32.4 |
| VS - oost              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| VS - oost 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 |
| Frankrijk - centraal       | 40.79.137.0, 40.79.129.1 |
| Duitsland - centraal      | 51.4.144.100       |
| Duitsland-noord Oost   | 51.5.144.179       |
| Duitsland - west-centraal | 51.116.240.0, 51.116.248.0, 51.116.152.0 |
| India - centraal        | 104.211.96.159     |
| India - zuid          | 104.211.224.146    |
| India - west           | 104.211.160.80     |
| Japan East           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 |
| Japan - west           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 |
| Korea - centraal        | 52.231.32.42       |
| Korea - zuid          | 52.231.200.86      |
| VS - noord-centraal     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| Europa - noord         | 40.113.93.91, 191.235.193.75, 52.138.224.1, 13.74.104.113 |
| Noorwegen - oost          | 51.120.96.0        |
| Noorwegen - west          | 51.120.216.0       |
| Zuid-Afrika - noord   | 102.133.152.0, 102.133.120.2       |
| Zuid-Afrika - west    | 102.133.24.0       |
| South Central US     | 13.66.62.124, 23.98.162.75, 104.214.16.32, 20.45.121.1, 20.49.88.1   |
| Azië - zuidoost      | 104.43.15.0, 23.100.117.95, 40.78.232.3   |
| Zwitserland - noord    | 51.107.56.0, 51.107.57.0 |
| Zwitserland - west     | 51.107.152.0, 51.107.153.0 |
| UAE - centraal          | 20.37.72.64        |
| VAE - noord            | 65.52.248.0        |
| Verenigd Koninkrijk Zuid             | 51.140.184.11, 51.105.64.0 |
| Verenigd Koninkrijk West              | 51.141.8.11        |
| VS - west-centraal      | 13.78.145.25, 13.78.248.43        |
| Europa -west          | 40.68.37.158, 191.237.232.75, 104.40.168.105, 52.236.184.163  |
| VS - west              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| West US 2            | 13.66.226.202, 40.78.240.8, 40.78.248.10  |
|                      |                    |

## <a name="next-steps"></a>Volgende stappen

- Zie voor meer informatie over het wijzigen van het Azure SQL Database verbindings beleid voor een-server verbinding [-beleid](https://docs.microsoft.com/cli/azure/sql/server/conn-policy).
- Voor informatie over Azure SQL Database verbindings gedrag voor clients die gebruikmaken van ADO.NET 4,5 of een latere versie, Zie [poorten na 1433 voor ADO.NET 4,5](adonet-v12-develop-direct-route-ports.md).
- Zie [SQL database Application Development Overview (overzicht van toepassings ontwikkeling](develop-overview.md)) voor algemene informatie over het ontwikkelen van toepassingen.
