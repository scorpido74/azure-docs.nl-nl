---
title: IP-adressen van Azure Integration Runtime
description: Meer informatie over welke IP-adressen u binnenkomend verkeer moet toestaan, om firewalls correct te configureren voor het beveiligen van netwerk toegang tot gegevens archieven.
services: data-factory
ms.author: abnarain
author: nabhishek
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 01/06/2020
ms.openlocfilehash: e3fae34d0bb1aaaa2471f1cc9fd55ef9b4b73dfd
ms.sourcegitcommit: 3abadafcff7f28a83a3462b7630ee3d1e3189a0e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/30/2020
ms.locfileid: "82594284"
---
# <a name="azure-integration-runtime-ip-addresses"></a>IP-adressen van Azure Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

De IP-adressen die Azure Integration Runtime gebruikt, zijn afhankelijk van de regio waar uw Azure Integration runtime zich bevindt. *Alle* Azure Integration runtimes die zich in dezelfde regio bevinden, gebruiken dezelfde IP-adresbereiken.

> [!IMPORTANT]  
> Gegevens stromen bieden geen ondersteuning voor het gebruik van vaste IP-bereiken.
>
> U kunt deze IP-bereiken gebruiken voor het verplaatsen van gegevens, pijp lijnen en externe activiteiten. Deze IP-bereiken kunnen worden gebruikt voor White List in gegevens archieven/netwerk beveiligings groep (NSG)/firewalls voor inkomende toegang vanuit Azure Integration runtime. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Azure Integration Runtime IP-adressen: specifieke regio's

Verkeer toestaan van de IP-adressen die worden vermeld voor Azure Integration runtime in de specifieke Azure-regio waar uw resources zich bevinden:

|                | Regio              | IP-adressen                                                 |
| -------------- | ------------------- | ------------------------------------------------------------ |
| Azië           | Azië - oost           | 20.189.104.128/25, </br>20.189.106.0/26, </br>13.75.39.112/28 |
| &nbsp;         | Azië - zuidoost      | 20.43.128.128/25, </br>20.43.130.0/26, </br>40.78.236.176/28 |
| Australië      | Australië - oost      | 20.37.193.0/25,</br>20.37.193.128/26,</br>13.70.74.144/28    |
| &nbsp;         | Australië - zuidoost | 20.42.225.0/25,</br>20.42.225.128/26,</br>13.77.53.160/28    |
| Brazilië         | Brazilië - zuid        | 191.235.224.128/25,</br>191.235.225.0/26,</br>191.233.205.160/28 |
| Canada         | Canada - midden      | 52.228.80.128/25,</br>52.228.81.0/26,</br>13.71.175.80/28    |
| China          | China-oost 2        | 40.73.172.48/28,</br>52.130.0.128/25,</br>52.130.1.0/26      |
| Europa         | Europa - noord        | 20.38.82.0/23,</br>20.38.80.192/26,</br>13.69.230.96/28      |
| &nbsp;         | Europa -west         | 40.74.26.0/23,</br>40.74.24.192/26,</br>13.69.67.192/28      |
| Frankrijk         | Frankrijk - centraal      | 20.43.40.128/25,</br>20.43.41.0/26,</br>40.79.132.112/28     |
| India          | India - centraal       | 52.140.104.128/25,</br>52.140.105.0/26,</br>20.43.121.48/28  |
| Japan          | Japan - oost          | 20.43.64.128/25,</br>20.43.65.0/26,</br>13.78.109.192/28     |
| Korea          | Korea - centraal       | 20.41.64.128/25,</br>20.41.65.0/26,</br>52.231.20.64/28      |
| Zuid-Afrika   | Zuid-Afrika - noord  | 102.133.124.104/29,</br>102.133.216.128/25,</br>102.133.217.0/26 |
| Verenigd Koninkrijk | Verenigd Koninkrijk Zuid            | 51.104.24.128/25,</br>51.104.25.0/26,</br>51.104.9.32/28     |
| Verenigde Staten  | VS - centraal          | 20.37.154.0/23,</br>20.37.156.0/26,</br>20.44.10.64/28       |
|                | VS - oost             | 20.42.2.0/23,</br>20.42.4.0/26,</br>40.71.14.32/28           |
|                | VS - oost 2            | 20.41.2.0/23,</br>20.41.4.0/26,</br>20.44.17.80/28           |
|                | VS-Oost 2 EUAP      | 20.39.8.128/26,</br>20.39.8.96/27,</br>40.75.35.144/28       |
|                | VS - noord-centraal    | 40.80.185.0/24,</br>40.80.186.0/25,</br>52.162.111.48/28      |
|                | VS - zuid-centraal    | 40.119.9.0/25,</br>40.119.9.128/26,</br>13.73.244.32/28      |
|                | VS - west-centraal     | 52.150.137.128/25,</br>52.150.136.192/26,</br>13.71.199.0/28 |
|                | VS - west             | 40.82.250.0/23,</br>40.82.249.64/26,</br>13.86.219.208/28    |
|                | VS - west 2            | 20.42.132.0/23,</br>20.42.129.64/26,</br>13.66.143.128/28    |
|                | VS (overheid) - Virginia     | 52.127.45.96/28,</br>52.127.48.128/25,</br>52.127.49.0/26    |

## <a name="known-issue-with-azure-storage"></a>Bekend probleem met Azure Storage

* Wanneer u verbinding maakt met Azure Storage account, hebben IP-netwerk regels geen invloed op aanvragen die afkomstig zijn van de Azure Integration runtime in dezelfde regio als het opslag account. [Raadpleeg dit artikel](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range)voor meer informatie. 

  In plaats daarvan wordt u aangeraden [vertrouwde services te gebruiken terwijl u verbinding maakt met Azure Storage](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993). 

## <a name="next-steps"></a>Volgende stappen

* [Beveiligings overwegingen voor het verplaatsen van gegevens in Azure Data Factory](data-movement-security-considerations.md)
