---
title: IP-adressen van Azure Integration Runtime
description: Ontdek van welke IP-adressen u binnenkomend verkeer moet toestaan om firewalls voor het beveiligen van netwerktoegang tot gegevensopslag correct te configureren.
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
ms.openlocfilehash: b0ba47ff28208bce1a6fa6ec300a261d788167de
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81415601"
---
# <a name="azure-integration-runtime-ip-addresses"></a>IP-adressen van Azure Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

De IP-adressen die wordt gebruikt voor Azure Integration Runtime, is afhankelijk van de regio waar de runtime van uw Azure-integratie zich bevindt. *Alles* Azure-integratie-runtimes die zich in dezelfde regio bevinden, gebruiken dezelfde IP-adresbereiken.

> [!IMPORTANT]  
> Gegevensstromen maken momenteel geen gebruik van deze IP's. 
>
> U deze IP-bereiken gebruiken voor uitvoeringen van gegevensverkeer, pijplijn en externe activiteiten. Deze IP-bereiken kunnen worden gebruikt voor whitelisting in data stores/ Network Security Group (NSG)/ Firewalls voor inkomende toegang vanuit Azure Integration runtime. 

## <a name="azure-integration-runtime-ip-addresses-specific-regions"></a>Ip-adressen voor runtime azure-integratie: specifieke regio's

Verkeer toestaan vanaf de IP-adressen die worden vermeld voor de runtime azure-integratie in het specifieke Azure-gebied waar uw resources zich bevinden:

|                | Regio              | IP-adressen                                                 |
| -------------- | ------------------- | ------------------------------------------------------------ |
| Azië           | Azië - oost           | 20.189.104.128/25, </br>20.189.106.0/26, </br>13.75.39.112/28 |
| &nbsp;         | Azië - zuidoost      | 20.43.128.128/25, </br>20.43.130.0/26, </br>40.78.236.176/28 |
| Australië      | Australië - oost      | 20.37.193.0/25,</br>20.37.193.128/26,</br>13.70.74.144/28    |
| &nbsp;         | Australië - zuidoost | 20.42.225.0/25,</br>20.42.225.128/26,</br>13.77.53.160/28    |
| Brazilië         | Brazilië - zuid        | 191.235.224.128/25,</br>191.235.225.0/26,</br>191.233.205.160/28 |
| Canada         | Canada - midden      | 52.228.80.128/25,</br>52.228.81.0/26,</br>13.71.175.80/28    |
| China          | China Oost 2        | 40.73.172.48/28,</br>52.130.0.128/25,</br>52.130.1.0/26      |
| Europa         | Europa - noord        | 20.38.82.0/23,</br>20.38.80.192/26,</br>13.69.230.96/28      |
| &nbsp;         | Europa -west         | 40.74.26.0/23,</br>40.74.24.192/26,</br>13.69.67.192/28      |
| Frankrijk         | Frankrijk - centraal      | 20.43.40.128/25,</br>20.43.41.0/26,</br>40.79.132.112/28     |
| India          | India - centraal       | 52.140.104.128/25,</br>52.140.105.0/26,</br>20.43.121.48/28  |
| Japan          | Japan - oost          | 20.43.64.128/25,</br>20.43.65.0/26,</br>13.78.109.192/28     |
| Korea          | Korea - centraal       | 20.41.64.128/25,</br>20.41.65.0/26,</br>52.231.20.64/28      |
| Zuid-Afrika   | Zuid-Afrika Noord  | 102.133.124.104/29,</br>102.133.216.128/25,</br>102.133.217.0/26 |
| Verenigd Koninkrijk | Verenigd Koninkrijk Zuid            | 51.104.24.128/25,</br>51.104.25.0/26,</br>51.104.9.32/28     |
| Verenigde Staten  | VS - centraal          | 20.37.154.0/23,</br>20.37.156.0/26,</br>20.44.10.64/28       |
|                | VS - oost             | 20.42.2.0/23,</br>20.42.4.0/26,</br>40.71.14.32/28           |
|                | VS - oost 2            | 20.41.2.0/23,</br>20.41.4.0/26,</br>20.44.17.80/28           |
|                | Oost-VS 2 EUAP      | 20.39.8.128/26,</br>20.39.8.96/27,</br>40.75.35.144/28       |
|                | VS - noord-centraal    | 40.80.185.0/24,</br>40.80.186.0/25,</br>52.162.111.48/28      |
|                | VS - zuid-centraal    | 40.119.9.0/25,</br>40.119.9.128/26,</br>13.73.244.32/28      |
|                | VS - west-centraal     | 52.150.137.128/25,</br>52.150.136.192/26,</br>13.71.199.0/28 |
|                | VS - west             | 40.82.250.0/23,</br>40.82.249.64/26,</br>13.86.219.208/28    |
|                | VS - west 2            | 20.42.132.0/23,</br>20.42.129.64/26,</br>13.66.143.128/28    |
|                | VS (overheid) - Virginia     | 52.127.45.96/28,</br>52.127.48.128/25,</br>52.127.49.0/26    |

## <a name="known-issue-with-azure-storage"></a>Bekend probleem met Azure Storage

* Wanneer u verbinding maakt met azure storage-account, hebben IP-netwerkregels geen effect op aanvragen die afkomstig zijn van de runtime van Azure-integratie in dezelfde regio als het opslagaccount. Voor meer informatie, [verwijzen wij u dit artikel](https://docs.microsoft.com/azure/storage/common/storage-network-security#grant-access-from-an-internet-ip-range). 

  In plaats daarvan raden we u aan vertrouwde services te gebruiken [tijdens een verbinding met Azure Storage.](https://techcommunity.microsoft.com/t5/azure-data-factory/data-factory-is-now-a-trusted-service-in-azure-storage-and-azure/ba-p/964993) 

## <a name="next-steps"></a>Volgende stappen

* [Beveiligingsoverwegingen voor gegevensverkeer in Azure Data Factory](data-movement-security-considerations.md)
