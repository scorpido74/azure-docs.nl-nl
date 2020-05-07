---
title: Virtueel netwerk apparaat configureren in azure HDInsight
description: Meer informatie over het configureren van een aantal extra functies voor uw virtuele netwerk apparaat in azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/06/2020
ms.openlocfilehash: cbc2104ae3c55ae3670867b7a253d812f3a4be0e
ms.sourcegitcommit: 602e6db62069d568a91981a1117244ffd757f1c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/06/2020
ms.locfileid: "82864706"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Virtueel netwerk apparaat configureren in azure HDInsight

> [!Important]
> De volgende informatie is **alleen** vereist als u een andere virtuele netwerk apparaten (NVA) dan Azure firewall wilt configureren.

Azure Firewall wordt automatisch geconfigureerd om verkeer toe te staan voor veel van de algemene belang rijke scenario's. Als u een ander virtueel netwerk apparaat wilt gebruiken, moet u een aantal extra functies configureren. Houd rekening met de volgende factoren wanneer u uw virtuele netwerk apparaat configureert:

* Services die geschikt zijn voor service-eind punten moeten worden geconfigureerd met Service-eind punten.
* Afhankelijkheden van IP-adressen zijn voor niet-HTTP/S-verkeer (TCP-en UDP-verkeer).
* FQDN HTTP/HTTPS-eind punten kunnen worden geplaatst op uw NVA-apparaat.
* Joker teken-HTTP/HTTPS-eind punten zijn afhankelijkheden die kunnen variëren op basis van een aantal kwalificaties.
* Wijs de route tabel toe die u hebt gemaakt voor uw HDInsight-subnet.

## <a name="service-endpoint-capable-dependencies"></a>Afhankelijkheden voor service-eind punten

| **Endpoints** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>Afhankelijkheden van IP-adressen

| **Endpoints** | **Details** |
|---|---|
| \*: 123 | NTP-klok controle. Verkeer wordt gecontroleerd op meerdere eind punten op poort 123 |
| [Hier](hdinsight-management-ip-addresses.md) gepubliceerde ip's | Deze IP-adressen zijn HDInsight-service |
| Privé-IP-adressen van AAD-DS voor ESP-clusters |
| \*: 16800 voor KMS Windows-activering |
| \*12000 voor Log Analytics |

### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS-afhankelijkheden

> [!Important]
> De onderstaande lijst bevat alleen enkele van de belangrijkste FQDN-namen. U kunt aanvullende FQDN-namen (voornamelijk Azure Storage en Azure Service Bus) verkrijgen voor het configureren [van uw NVA in dit bestand](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json).

| **Endpoints**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |
| wawsinfraprodbay063.blob.core.windows.net:443                         |
| registry-1.docker.io:443                                              |
| auth.docker.io:443                                                    |
| production.cloudflare.docker.com:443                                  |
| download.docker.com:443                                               |
| us.archive.ubuntu.com:80                                              |
| download.mono-project.com:80                                          |
| packages.treasuredata.com:80                                          |
| security.ubuntu.com:80                                                |
| azure.archive.ubuntu.com:80                                           |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>Volgende stappen

* [Firewall gebruiken om uitgaand verkeer te beperken](./hdinsight-restrict-outbound-traffic.md)
* [Azure HDInsight Virtual Network-architectuur](hdinsight-virtual-network-architecture.md)
