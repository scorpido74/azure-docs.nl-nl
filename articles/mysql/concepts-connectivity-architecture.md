---
title: Connectiviteits architectuur-Azure Database for MySQL
description: Beschrijft de connectiviteits architectuur voor uw Azure Database for MySQL-server.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 8d4713cba94971971558a72c680e3c8f80331662
ms.sourcegitcommit: b5d646969d7b665539beb18ed0dc6df87b7ba83d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/26/2020
ms.locfileid: "76760331"
---
# <a name="connectivity-architecture-in-azure-database-for-mysql"></a>Connectiviteits architectuur in Azure Database for MySQL
In dit artikel wordt uitgelegd wat de Azure Database for MySQL connectiviteits architectuur is en hoe het verkeer wordt omgeleid naar uw Azure Database for MySQL exemplaar van clients, zowel binnen als buiten Azure.

## <a name="connectivity-architecture"></a>Connectiviteitsarchitectuur
De verbinding met uw Azure Database for MySQL wordt tot stand gebracht via een gateway die verantwoordelijk is voor de route ring van binnenkomende verbindingen naar de fysieke locatie van uw server in onze clusters. In het volgende diagram ziet u de verkeers stroom.

![Overzicht van de connectiviteits architectuur](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Wanneer een client verbinding maakt met de data base, krijgen ze een connection string die verbinding maakt met de gateway. Deze gateway heeft een openbaar IP-adres dat luistert naar poort 3306. Binnen het database cluster wordt verkeer doorgestuurd naar de juiste Azure Database for MySQL. Daarom is het nodig om verbinding te maken met uw server, zoals van bedrijfs netwerken, het is nood zakelijk om de firewall aan de client zijde te openen zodat uitgaand verkeer de gateways kan bereiken. Hieronder vindt u een volledige lijst met de IP-adressen die worden gebruikt door onze gateways per regio.

## <a name="azure-database-for-mysql-gateway-ip-addresses"></a>IP-adressen van Azure Database for MySQL gateway
De volgende tabel geeft een lijst van de primaire en secundaire IP-adressen van de Azure Database for MySQL gateway voor alle gegevens regio's. Het primaire IP-adres is het huidige IP-adres van de gateway en het tweede IP-adres is een failover-IP-adres in geval van een storing van de primaire. Zoals gezegd, moeten klanten uitgaande berichten toestaan voor de IP-adressen. Het tweede IP-adres luistert op geen enkele services totdat het wordt geactiveerd door Azure Database for MySQL om verbindingen te accepteren.

| **Regio naam** | **IP-adressen van Gateway** |
|:----------------|:-------------|
| Australië - centraal| 20.36.105.0     |
| Australië-Central2     | 20.36.113.0   |
| Australië - oost | 13.75.149.87, 40.79.161.1     |
| Australië - zuidoost |191.239.192.109, 13.73.109.251   |
| Brazilië - Zuid | 104.41.11.5, 191.233.201.8, 191.233.200.16  |
| Canada-Midden |40.85.224.249  |
| Canada-Oost | 40.86.226.166    |
| US - centraal | 23.99.160.139, 13.67.215.62   |
| China - oost | 139.219.130.35    |
| China - oost 2 | 40.73.82.1  |
| China - noord | 139.219.15.17    |
| China - noord 2 | 40.73.50.0     |
| Azië - oost | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     |
| VS - oost | 40.121.158.30, 191.238.6.43  |
| VS - oost 2 |40.79.84.180, 191.239.224.107, 52.177.185.181   |
| Frankrijk - centraal | 40.79.137.0, 40.79.129.1  |
| Duitsland - centraal | 51.4.144.100     |
| Duitsland-noord Oost | 51.5.144.179  |
| India - centraal | 104.211.96.159     |
| India - zuid | 104.211.224.146  |
| India - west | 104.211.160.80    |
| Japan - Oost | 13.78.61.196, 191.237.240.43  |
| Japan - West | 104.214.148.156, 191.238.68.11    |
| Korea - centraal | 52.231.32.42   |
| Korea - zuid | 52.231.200.86    |
| US - noord-centraal | 23.96.178.199, 23.98.55.75, 52.162.104.35, 52.162.104.36    |
| Europa - noord | 40.113.93.91, 191.235.193.75    |
| Zuid-Afrika - noord  | 102.133.152.0    |
| Zuid-Afrika - west | 102.133.24.0   |
| US - zuid-centraal |13.66.62.124, 23.98.162.75   |
| Azië - zuidoost | 104.43.15.0, 23.100.117.95, 40.78.233.2, 23.98.80.12     |
| VAE - centraal | 20.37.72.64  |
| VAE - noord | 65.52.248.0    |
| UK - zuid | 51.140.184.11   |
| UK - west | 51.141.8.11  |
| US - west-centraal | 13.78.145.25     |
| Europa - west | 40.68.37.158, 191.237.232.75     |
| US - west | 104.42.238.205, 23.99.34.75  |
| US - west 2 | 13.66.226.202  |
||||

## <a name="next-steps"></a>Volgende stappen

* [Azure Database for MySQL firewall regels maken en beheren met behulp van de Azure Portal](./howto-manage-firewall-using-portal.md)
* [Azure Database for MySQL firewall regels maken en beheren met Azure CLI](./howto-manage-firewall-using-cli.md)
