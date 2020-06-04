---
title: De Azure Databricks route tabellen en firewalls bijwerken met nieuwe MySQL-Ip's
description: Meer informatie over het bijwerken van uw Azure Databricks route tabellen en firewalls met nieuwe MySQL IP-adressen.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 06/02/2020
ms.openlocfilehash: 5bd4822c21e6d9dbe8f3534dd0171a6f785bd5ac
ms.sourcegitcommit: 58ff2addf1ffa32d529ee9661bbef8fbae3cddec
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/03/2020
ms.locfileid: "84325913"
---
# <a name="update-your-azure-databricks-route-tables-and-firewalls-with-new-mysql-ips"></a>De Azure Databricks route tabellen en firewalls bijwerken met nieuwe MySQL-Ip's

Alle IP-adressen van uw Azure Database for MySQL, die als uw Azure Databricks meta Store worden gebruikt, worden gewijzigd. Werk uw Azure Databricks route tabellen of firewalls bij met nieuwe MySQL-Ip's vóór 30 juni 2020 om onderbrekingen te voor komen.

Een [route tabel](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/udr#--metastore-artifact-blob-storage-log-blob-storage-and-event-hub-endpoint-ip-addresses) is gekoppeld aan een Azure Databricks werk ruimte die in uw eigen virtuele netwerk is geïmplementeerd. De route tabel mag rechtstreeks een Azure Databricks meta Store-IP-adres bevatten of een route naar een firewall of een proxy apparaat sturen dat white list het adres kan zijn.

## <a name="recommended-actions"></a>Aanbevolen acties

Als u wilt voor komen dat uw service wordt onderbroken, kunt u deze acties vóór 30 juni 2020 bekijken en Toep assen.

* Controleer of de route tabel wordt beïnvloed door een wijziging in de update van het Azure MySQL-IP-adres.

* Gebruik de tabel in de volgende sectie om de nieuwe IP-adressen op te zoeken. Voor elke regio kunnen er meerdere IP-adressen worden gebruikt als primaire en secundaire Ip's van de Azure Database for MySQL gateways. Het primaire IP-adres is het huidige IP-adres van de gateway en de tweede IP-adressen zijn failover-IP-adressen in geval van een storing van de primaire. Als u de service status wilt controleren, moet u uitgaand naar alle IP-adressen toestaan. Als u een [externe meta Store](https://docs.microsoft.com/azure/databricks/data/metastores/external-hive-metastore)gebruikt, moet u ervoor zorgen dat een geldig IP-adres per Azure mysql-melding wordt gerouteerd of white list.

* Werk de route tabel, de firewall of het proxy apparaat bij met de nieuwe Ip's.

## <a name="updated-mysql-ip-addresses"></a>Bijgewerkte MySQL-IP-adressen

De volgende tabel bevat alle IP-adressen die moeten worden white list. De IP-adressen die vet worden weer **gegeven** , zijn de nieuwe IP-adressen. 

| Regio naam          | IP-adressen van Gateway                                                                                       |
| -------------------- | ---------------------------------------------------------------------------------------------------------- |
| Australië - centraal    | 20.36.105.0                                                                                                |
| Australië - centraal 2  | 20.36.113.0                                                                                                |
| Australië - oost       | 13.75.149.87<br><br>40.79.161.1                                                                            |
| Australië - zuidoost | 191.239.192.109<br><br>13.73.109.251                                                                       |
| Brazilië - zuid         | 104.41.11.5 <br><br> 191.233.201.8 <br><br> **191.233.200.16**                                             |
| Canada - midden       | 40.85.224.249                                                                                              |
| Canada - oost          | 40.86.226.166                                                                                              |
| VS - centraal           | 23.99.160.139<br><br>13.67.215.62<br><br>**52.182.136.37**<br><br>**52.182.136.38**                        |
| China East           | 139.219.130.35                                                                                             |
| China-oost 2         | 40.73.82.1                                                                                                 |
| China - noord          | 139.219.15.17                                                                                              |
| China-noord 2        | 40.73.50.0                                                                                                 |
| Azië - oost            | 191.234.2.139<br><br>52.175.33.150<br><br>**13.75.33.20**<br><br>**13.75.33.21**                           |
| VS - oost              | 40.121.158.30<br>191.238.6.43                                                                              |
| VS - oost 2            | 40.79.84.180<br><br>191.239.224.107<br><br>52.177.185.181<br><br>**40.70.144.38**<br><br>**52.167.105.38** |
| Frankrijk - centraal       | 40.79.137.0<br><br>40.79.129.1                                                                             |
| Duitsland - centraal      | 51.4.144.100                                                                                               |
| Duitsland-noord Oost   | 51.5.144.179                                                                                               |
| India - centraal        | 104.211.96.159                                                                                             |
| India - zuid          | 104.211.224.146                                                                                            |
| India - west           | 104.211.160.80                                                                                             |
| Japan - oost           | 13.78.61.196<br><br>191.237.240.43                                                                         |
| Japan - west           | 104.214.148.156<br><br>191.238.68.11<br><br>**40.74.96.6**<br><br>**40.74.96.7**                           |
| Korea - centraal        | 52.231.32.42                                                                                               |
| Korea - zuid          | 52.231.200.86                                                                                              |
| VS - noord-centraal     | 23.96.178.199<br><br>23.98.55.75<br><br>**52.162.104.35**<br><br>**52.162.104.36**                         |
| Europa - noord         | 40.113.93.91<br><br>191.235.193.75<br><br>**52.138.224.6**<br><br>**52.138.224.7**                         |
| Zuid-Afrika - noord   | 102.133.152.0                                                                                              |
| Zuid-Afrika - west    | 102.133.24.0                                                                                               |
| VS - zuid-centraal     | 13.66.62.124<br><br>23.98.162.75<br><br>**104.214.16.39**<br><br>**20.45.120.0**                           |
| Azië - zuidoost      | 104.43.15.0<br><br>23.100.117.95<br><br>**40.78.233.2**<br><br>**23.98.80.12**                             |
| UAE - centraal          | 20.37.72.64                                                                                                |
| UAE - noord            | 65.52.248.0                                                                                                |
| Verenigd Koninkrijk Zuid             | 51.140.184.11                                                                                              |
| Verenigd Koninkrijk West              | 51.141.8.11                                                                                                |
| VS - west-centraal      | 13.78.145.25                                                                                               |
| Europa -west          | 40.68.37.158<br><br>191.237.232.75<br><br>**13.69.105.208**                                                |
| VS - west              | 104.42.238.205<br><br>23.99.34.75                                                                          |
| VS - west 2            | 13.66.226.202                                                                                              |

## <a name="next-steps"></a>Volgende stappen

* [Azure Databricks implementeren in uw virtuele Azure-netwerk (VNet-injectie)](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject)
* [Externe Apache Hive meta Store](https://docs.microsoft.com/azure/databricks/data/metastores/external-hive-metastore)
