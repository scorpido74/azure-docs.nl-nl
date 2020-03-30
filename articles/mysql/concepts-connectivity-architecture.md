---
title: Connectiviteitsarchitectuur - Azure Database voor MySQL
description: Beschrijft de connectiviteitsarchitectuur voor uw Azure Database voor MySQL-server.
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/16/2020
ms.openlocfilehash: e7098056356c84469d3dc6bd4e3222fbb5b7f908
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79474285"
---
# <a name="connectivity-architecture-in-azure-database-for-mysql"></a>Connectiviteitsarchitectuur in Azure Database voor MySQL
In dit artikel wordt de Azure Database for MySQL-connectiviteitsarchitectuur uitgelegd en hoe het verkeer wordt doorgestuurd naar uw Azure Database voor MySQL-instantie van clients binnen en buiten Azure.

## <a name="connectivity-architecture"></a>Connectiviteitsarchitectuur
Verbinding met uw Azure Database for MySQL wordt tot stand gebracht via een gateway die verantwoordelijk is voor het routeren van binnenkomende verbindingen naar de fysieke locatie van uw server in onze clusters. Het volgende diagram illustreert de verkeersstroom.

![Overzicht van de connectiviteitsarchitectuur](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Als client verbinding maakt met de database, krijgen ze een verbindingstekenreeks die verbinding maakt met de gateway. Deze gateway heeft een openbaar IP-adres dat luistert naar poort 3306. Binnen het databasecluster wordt verkeer doorgestuurd naar de juiste Azure Database voor MySQL. Daarom is het noodzakelijk om de firewall aan de clientzijde te openen om uitgaand verkeer onze gateways te kunnen bereiken. Hieronder vindt u een volledige lijst van de IP-adressen die worden gebruikt door onze gateways per regio.

## <a name="azure-database-for-mysql-gateway-ip-addresses"></a>Azure Database voor IP-adressen van MySQL-gateway
In de volgende tabel worden de primaire en secundaire IP's van de Azure Database for MySQL-gateway voor alle gegevensregio's weergegeven. Het primaire IP-adres is het huidige IP-adres van de gateway en het tweede IP-adres is een failover IP-adres in geval van uitval van de primaire. Zoals gezegd moeten klanten uitgaand naar beide IP-adressen toestaan. Het tweede IP-adres luistert pas mee met services als het door Azure Database is geactiveerd om verbindingen te accepteren.

| **Naam regio** | **IP-adressen van gateway** |
|:----------------|:-------------|
| Australië - centraal| 20.36.105.0     |
| Australië Centraal2     | 20.36.113.0     |
| Australië - oost | 13.75.149.87, 40.79.161.1     |
| Australië - zuidoost |191.239.192.109, 13.73.109.251     |
| Brazilië - zuid | 104.41.11.5, 191.233.201.8, 191.233.200.16     |
| Canada - midden |40.85.224.249     |
| Canada - oost | 40.86.226.166     |
| VS - centraal | 23.99.160.139, 13.67.215.62     |
| China East | 139.219.130.35     |
| China Oost 2 | 40.73.82.1     |
| China - noord | 139.219.15.17     |
| China Noord 2 | 40.73.50.0     |
| Azië - oost | 191.234.2.139, 52.175.33.150, 13.75.33.20, 13.75.33.21     |
| VS - oost | 40.121.158.30, 191.238.6.43     |
| VS - oost 2 |40.79.84.180, 191.239.224.107, 52.177.185.181     |
| Frankrijk - centraal | 40.79.137.0, 40.79.129.1     |
| Duitsland - centraal | 51.4.144.100     |
| Duitsland Noord-Oost | 51.5.144.179     |
| India - centraal | 104.211.96.159     |
| India - zuid | 104.211.224.146     |
| India - west | 104.211.160.80     |
| Japan - oost | 13.78.61.196, 191.237.240.43     |
| Japan - west | 104.214.148.156, 191.238.68.11, 40.74.96.7, 40.74.96.6 |
| Korea - centraal | 52.231.32.42     |
| Korea - zuid | 52.231.200.86     |
| VS - noord-centraal | 23.96.178.199, 23.98.55.75, 52.162.104.35, 52.162.104.36     |
| Europa - noord | 40.113.93.91, 191.235.193.75     |
| Zuid-Afrika Noord  | 102.133.152.0     |
| Zuid-Afrika West    | 102.133.24.0     |
| VS - zuid-centraal |13.66.62.124, 23.98.162.75, 104.214.16.39, 20.45.120.0     |
| Azië - zuidoost | 104.43.15.0, 23.100.117.95, 40.78.233.2, 23.98.80.12     |
| VAE Centraal | 20.37.72.64     |
| VAE Noord | 65.52.248.0     |
| Verenigd Koninkrijk Zuid | 51.140.184.11     |
| Verenigd Koninkrijk West | 51.141.8.11     |
| VS - west-centraal | 13.78.145.25     |
| Europa -west | 40.68.37.158, 191.237.232.75     |
| VS - west | 104.42.238.205, 23.99.34.75     |
| VS - west 2 | 13.66.226.202     |
||||

## <a name="connection-redirection"></a>Omleiding van verbinding

Azure Database voor MySQL ondersteunt een extra verbindingsbeleid, **omleiding,** dat helpt om de netwerklatentie tussen clienttoepassingen en MySQL-servers te verminderen. Met deze functie retourneert de server het backendadres van het knooppunt dat de MySQL-server host, nadat de eerste TCP-sessie is ingesteld op de Azure Database voor MySQL-server. Daarna stromen alle volgende pakketten rechtstreeks naar de server, waarmee de gateway wordt omzeild. Naarmate pakketten rechtstreeks naar de server stromen, zijn de prestaties en doorvoer verbeterd.

Deze functie wordt ondersteund in Azure Database voor MySQL-servers met engineversies 5.6, 5.7 en 8.0.

Ondersteuning voor omleiding is beschikbaar in de [PHP-mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) extensie, ontwikkeld door Microsoft, en is beschikbaar op [PECL](https://pecl.php.net/package/mysqlnd_azure). Zie [het artikel voor omleiding configureren](./howto-redirection.md) voor meer informatie over het gebruik van omleiding in uw toepassingen.

> [!IMPORTANT]
> Ondersteuning voor omleiding in de PHP [mysqlnd_azure](https://github.com/microsoft/mysqlnd_azure) extensie is momenteel in preview.

## <a name="next-steps"></a>Volgende stappen

* [Azure Database voor MySQL-firewallregels maken en beheren met behulp van de Azure-portal](./howto-manage-firewall-using-portal.md)
* [Azure Database voor MySQL-firewallregels maken en beheren met Azure CLI](./howto-manage-firewall-using-cli.md)
* [Omleiding configureren met Azure Database voor MySQL](./howto-redirection.md)