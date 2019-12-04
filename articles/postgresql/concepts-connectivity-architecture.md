---
title: Connectiviteits architectuur-Azure Database for PostgreSQL-één server
description: Beschrijft de connectiviteits architectuur van uw Azure Database for PostgreSQL-één server.
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 05/23/2019
ms.openlocfilehash: cbb3c3b3b91bf5111efcf6f2a53cfb40b48f3fe7
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2019
ms.locfileid: "74768874"
---
# <a name="connectivity-architecture-in-azure-database-for-postgresql"></a>Connectiviteits architectuur in Azure Database for PostgreSQL
In dit artikel wordt uitgelegd wat de Azure Database for PostgreSQL connectiviteits architectuur is en hoe het verkeer wordt omgeleid naar uw Azure Database for PostgreSQL data base-exemplaar van clients, zowel binnen als buiten Azure.

## <a name="connectivity-architecture"></a>Connectiviteitsarchitectuur
De verbinding met uw Azure Database for PostgreSQL wordt tot stand gebracht via een gateway die verantwoordelijk is voor de route ring van binnenkomende verbindingen naar de fysieke locatie van uw server in onze clusters. In het volgende diagram ziet u de verkeers stroom.

![Overzicht van de connectiviteits architectuur](./media/concepts-connectivity-architecture/connectivity-architecture-overview-proxy.png)

Wanneer een client verbinding maakt met de data base, krijgen ze een connection string die verbinding maakt met de gateway. Deze gateway heeft een openbaar IP-adres dat luistert naar poort 5432. In het database cluster verkeer wordt doorgestuurd naar de juiste Azure Database for PostgreSQL. Daarom is het nodig om verbinding te maken met uw server, zoals van bedrijfs netwerken, het is nood zakelijk om de firewall aan de client zijde te openen zodat uitgaand verkeer de gateways kan bereiken. Hieronder vindt u een volledige lijst met de IP-adressen die worden gebruikt door onze gateways per regio.

## <a name="azure-database-for-postgresql-gateway-ip-addresses"></a>IP-adressen van Azure Database for PostgreSQL gateway
De volgende tabel geeft een lijst van de primaire en secundaire IP-adressen van de Azure Database for PostgreSQL gateway voor alle gegevens regio's. Het primaire IP-adres is het huidige IP-adres van de gateway en het tweede IP-adres is een failover-IP-adres in geval van een storing van de primaire. Zoals gezegd, moeten klanten uitgaande berichten toestaan voor de IP-adressen. Het tweede IP-adres luistert op geen enkele services totdat het wordt geactiveerd door Azure Database for PostgreSQL om verbindingen te accepteren.

| **Regio naam** | **Primair IP-adres** | **Secundair IP-adres** |
|:----------------|:-------------|:------------------------|
| Australië Oost | 13.75.149.87 | 40.79.161.1 |
| Australië - zuidoost | 191.239.192.109 | 13.73.109.251 |
| Brazilië - Zuid | 104.41.11.5 | |
| Canada-Midden | 40.85.224.249 | |
| Canada-Oost | 40.86.226.166 | |
| VS - centraal | 23.99.160.139 | 13.67.215.62 |
| China-oost 1 | 139.219.130.35 | |
| China - oost 2 | 40.73.82.1 | |
| China-noord 1 | 139.219.15.17 | |
| China - noord 2 | 40.73.50.0 | |
| Azië - oost | 191.234.2.139 | 52.175.33.150 |
| VS-Oost 1 | 191.238.6.43 | 40.121.158.30 |
| VS - oost 2 | 191.239.224.107 | 40.79.84.180 * |
| Frankrijk - centraal | 40.79.137.0 | 40.79.129.1 |
| Duitsland - centraal | 51.4.144.100 | |
| India - centraal | 104.211.96.159 | |
| India - zuid | 104.211.224.146 | |
| India - west | 104.211.160.80 | |
| Japan - Oost | 191.237.240.43 | 13.78.61.196 |
| Japan - West | 191.238.68.11 | 104.214.148.156 |
| Korea - centraal | 52.231.32.42 | |
| Korea (Zuid) | 52.231.200.86 |  |
| VS - noord-centraal | 23.98.55.75 | 23.96.178.199 |
| Europa - noord | 191.235.193.75 | 40.113.93.91 |
| VS - zuid-centraal | 23.98.162.75 | 13.66.62.124 |
| Azië - zuidoost | 23.100.117.95 | 104.43.15.0 |
| Zuid-Afrika - noord | 102.133.152.0 | |
| Zuid-Afrika - west | 102.133.24.0 | |
| VAE - noord | 65.52.248.0 | |
| VK - zuid | 51.140.184.11 | |
| VK - west | 51.141.8.11| |
| Europa - west | 191.237.232.75 | 40.68.37.158 |
| VS-West 1 | 23.99.34.75 | 104.42.238.205 |
| VS - west 2 | 13.66.226.202 | |
||||

> [!NOTE]
> *VS-Oost 2* heeft ook een tertiair IP-adres van `52.167.104.0`.

## <a name="next-steps"></a>Volgende stappen

* [Azure Database for PostgreSQL firewall regels maken en beheren met behulp van de Azure Portal](./howto-manage-firewall-using-portal.md)
* [Azure Database for PostgreSQL firewall regels maken en beheren met Azure CLI](./howto-manage-firewall-using-cli.md)
