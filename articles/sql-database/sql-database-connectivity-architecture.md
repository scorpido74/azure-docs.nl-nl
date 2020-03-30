---
title: Connectiviteitsarchitectuur
description: In dit document wordt de Azure SQL-connectiviteitsarchitectuur voor databaseverbindingen van binnen Azure of van buiten Azure uitgelegd.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: fasttrack-edit
titleSuffix: Azure SQL Database and SQL Data Warehouse
ms.devlang: ''
ms.topic: conceptual
author: rohitnayakmsft
ms.author: rohitna
ms.reviewer: carlrab, vanto
ms.date: 03/09/2020
ms.openlocfilehash: 6fdfbce6dce2428a8f2757b0755e6f982f02240f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256417"
---
# <a name="azure-sql-connectivity-architecture"></a>Connectiviteitsarchitectuur van Azure SQL
> [!NOTE]
> Dit artikel is van toepassing op Azure SQL-server en op zowel SQL Database- als SQL Data Warehouse-databases die zijn gemaakt op de Azure SQL-server. Voor het gemak wordt de term 'SQL Database' gebruikt wanneer er wordt verwezen naar zowel SQL Database als SQL Data Warehouse.

> [!IMPORTANT]
> Dit artikel is *niet* van toepassing op **Azure SQL Database Managed Instance**. Raadpleeg [connectiviteitsarchitectuur voor een beheerde instantie](sql-database-managed-instance-connectivity-architecture.md).

In dit artikel wordt uitgelegd welke architectuur van verschillende componenten het netwerkverkeer naar het Azure SQL Database of SQL Data Warehouse leidt. Het legt ook verschillende verbindingsbeleidsregels uit en hoe dit gevolgen heeft voor clients die verbinding maken vanuit Azure en clients die verbinding maken van buiten Azure. 

## <a name="connectivity-architecture"></a>Connectiviteitsarchitectuur

Het volgende diagram biedt een overzicht op hoog niveau van de Azure SQL Database-connectiviteitsarchitectuur.

![architectuuroverzicht](./media/sql-database-connectivity-architecture/connectivity-overview.png)

In de volgende stappen wordt beschreven hoe een verbinding tot stand wordt gebracht met een Azure SQL-database:

- Clients maken verbinding met de gateway, die een openbaar IP-adres heeft en luistert op poort 1433.
- De gateway, afhankelijk van het effectieve verbindingsbeleid, leidt of proxy's het verkeer naar de juiste databasecluster.
- Binnen het databaseclusterwordt het verkeer doorgestuurd naar de juiste Azure SQL-database.

## <a name="connection-policy"></a>Verbindingsbeleid

Azure SQL Database ondersteunt de volgende drie opties voor de instelling van het verbindingsbeleid van een SQL Database-server:

- **Omleiden (aanbevolen):** Clients leggen rechtstreeks verbindingen met het knooppunt dat de database host, wat leidt tot verminderde latentie en verbeterde doorvoer. Voor verbindingen om deze modus te gebruiken, moeten clients:
   - Gebruik uitgaande communicatie van de client naar alle Azure IP-adressen in de regio op poorten in het bereik van 11000 11999. Gebruik de Service Tags voor SQL om dit eenvoudiger te beheren.  
   - Gebruik uitgaande communicatie van de client naar AZURE SQL Database-gateway-IP-adressen op poort 1433.

- **Proxy:** In deze modus worden alle verbindingen geproxied via de Azure SQL Database-gateways, wat leidt tot verhoogde latentie en verminderde doorvoer. Voor verbindingen die deze modus gebruiken, moeten clients uitgaande communicatie van de client naar AZURE SQL Database-gateway-IP-adressen op poort 1433 toestaan.

- **Standaard:** Dit is het verbindingsbeleid dat van kracht is op alle servers `Proxy` `Redirect`na het maken, tenzij u het verbindingsbeleid expliciet wijzigt in een van beide of . Het standaardbeleid`Redirect` is voor alle clientverbindingen die afkomstig zijn van Azure `Proxy`(bijvoorbeeld van een Azure Virtual Machine) en voor alle clientverbindingen die van buiten afkomstig zijn (bijvoorbeeld verbindingen van uw lokale werkstation).

 We raden `Redirect` het verbindingsbeleid `Proxy` ten zeerste aan ten opzichte van het verbindingsbeleid voor de laagste latentie en de hoogste doorvoer. U moet echter wel voldoen aan de aanvullende vereisten voor het toestaan van netwerkverkeer zoals hierboven beschreven. Als de client een Azure Virtual Machine is, u dit uitvoeren met behulp van Network Security Groups (NSG) met [servicetags.](../virtual-network/security-overview.md#service-tags) Als de client verbinding maakt vanaf een on-premises werkstation, moet u mogelijk samenwerken met uw netwerkbeheerder om netwerkverkeer via uw bedrijfsfirewall toe te staan.

## <a name="connectivity-from-within-azure"></a>Connectiviteit vanuit Azure

Als u verbinding maakt vanuit Azure, hebben `Redirect` uw verbindingen standaard een verbindingsbeleid. Een beleid `Redirect` van middelen dat nadat de TCP-sessie is ingesteld in de Azure SQL-database, de clientsessie vervolgens wordt doorgestuurd naar het juiste databasecluster met een wijziging in het virtuele IP-doel van de Azure SQL Database-gateway naar die van het cluster. Daarna stromen alle volgende pakketten rechtstreeks naar het cluster, waarmee de Azure SQL Database-gateway wordt omzeild. Het volgende diagram illustreert deze verkeersstroom.

![architectuuroverzicht](./media/sql-database-connectivity-architecture/connectivity-azure.png)

## <a name="connectivity-from-outside-of-azure"></a>Connectiviteit van buiten Azure

Als u verbinding maakt van buiten Azure, `Proxy` hebben uw verbindingen standaard een verbindingsbeleid. Een beleid `Proxy` van middelen dat de TCP-sessie wordt vastgesteld via de Azure SQL Database-gateway en alle volgende pakketten stromen via de gateway. Het volgende diagram illustreert deze verkeersstroom.

![architectuuroverzicht](./media/sql-database-connectivity-architecture/connectivity-onprem.png)

> [!IMPORTANT]
> Bovendien open poorten 14000-14999 om verbinding te maken [met DAC](https://docs.microsoft.com/sql/database-engine/configure-windows/diagnostic-connection-for-database-administrators?view=sql-server-2017#connecting-with-dac)


## <a name="azure-sql-database-gateway-ip-addresses"></a>IP-adressen van Azure SQL Database-gateway

In de onderstaande tabel worden de IP-adressen van gateways per regio weergegeven. Als u verbinding wilt maken met een Azure SQL-database, moet u het netwerkverkeer toestaan om te & van **alle** gateways voor de regio.

Details over hoe verkeer moet worden gemigreerd naar nieuwe gateways in specifieke regio's vindt u in het volgende artikel: [Azure SQL Database-verkeermigratie naar nieuwere gateways](sql-database-gateway-migration.md)


| Naam regio          | IP-adressen van gateway |
| --- | --- |
| Australië - centraal    | 20.36.105.0 |
| Australië Centraal2   | 20.36.113.0 |
| Australië - oost       | 13.75.149.87, 40.79.161.1 |
| Australië - zuidoost | 191.239.192.109, 13.73.109.251 |
| Brazilië - zuid         | 104.41.11.5, 191.233.200.14 |
| Canada - midden       | 40.85.224.249      |
| Canada - oost          | 40.86.226.166      |
| VS - centraal           | 13.67.215.62, 52.182.137.15, 23.99.160.139, 104.208.16.96, 104.208.21.1 | 
| China East           | 139.219.130.35     |
| China Oost 2         | 40.73.82.1         |
| China - noord          | 139.219.15.17      |
| China Noord 2        | 40.73.50.0         |
| Azië - oost            | 191.234.2.139, 52.175.33.150, 13.75.32.4 |
| VS - oost              | 40.121.158.30, 40.79.153.12, 191.238.6.43, 40.78.225.32 |
| VS - oost 2            | 40.79.84.180, 52.177.185.181, 52.167.104.0, 191.239.224.107, 104.208.150.3 | 
| Frankrijk - centraal       | 40.79.137.0, 40.79.129.1 |
| Duitsland - centraal      | 51.4.144.100       |
| Duitsland Noord-Oost   | 51.5.144.179       |
| India - centraal        | 104.211.96.159     |
| India - zuid          | 104.211.224.146    |
| India - west           | 104.211.160.80     |
| Japan - oost           | 13.78.61.196, 40.79.184.8, 13.78.106.224, 191.237.240.43, 40.79.192.5 | 
| Japan - west           | 104.214.148.156, 40.74.100.192, 191.238.68.11, 40.74.97.10 | 
| Korea - centraal        | 52.231.32.42       |
| Korea - zuid          | 52.231.200.86      |
| VS - noord-centraal     | 23.96.178.199, 23.98.55.75, 52.162.104.33 |
| Europa - noord         | 40.113.93.91, 191.235.193.75, 52.138.224.1 | 
| Noorwegen-Oost          | 51.120.96.0        |
| Noorwegen West          | 51.120.216.0       |
| Zuid-Afrika Noord   | 102.133.152.0      |
| Zuid-Afrika West    | 102.133.24.0       |
| VS - zuid-centraal     | 13.66.62.124, 23.98.162.75, 104.214.16.32   | 
| Azië - zuidoost      | 104.43.15.0, 23.100.117.95, 40.78.232.3   | 
| VAE Centraal          | 20.37.72.64        |
| VAE Noord            | 65.52.248.0        |
| Verenigd Koninkrijk Zuid             | 51.140.184.11      |
| Verenigd Koninkrijk West              | 51.141.8.11        |
| VS - west-centraal      | 13.78.145.25       |
| Europa -west          | 40.68.37.158, 191.237.232.75, 104.40.168.105  |
| VS - west              | 104.42.238.205, 23.99.34.75, 13.86.216.196   |
| VS - west 2            | 13.66.226.202      |
|                      |                    |



## <a name="next-steps"></a>Volgende stappen

- Zie [conn-policy](https://docs.microsoft.com/cli/azure/sql/server/conn-policy)voor informatie over het wijzigen van het verbindingsbeleid van Azure SQL Database voor een Azure SQL Database-server.
- Zie [Poorten na 1433 voor ADO.NET 4.5 voor](sql-database-develop-direct-route-ports-adonet-v12.md)informatie over het verbindingsgedrag van Azure SQL Database voor clients die ADO.NET 4.5 of een latere versie gebruiken.
- Zie SQL Database Application [Development Overview](sql-database-develop-overview.md)voor algemene overzichtsinformatie over toepassingsontwikkeling.
