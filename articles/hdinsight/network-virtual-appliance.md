---
title: Virtueel netwerk apparaat configureren in azure HDInsight
description: Meer informatie over het configureren van een aantal extra functies voor uw virtuele netwerk apparaat in azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.date: 06/30/2020
ms.openlocfilehash: b9e33ba8d9610067cb9e844477ec273391fbdb0b
ms.sourcegitcommit: 62717591c3ab871365a783b7221851758f4ec9a4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/22/2020
ms.locfileid: "88751720"
---
# <a name="configure-network-virtual-appliance-in-azure-hdinsight"></a>Virtueel netwerk apparaat configureren in azure HDInsight

> [!Important]
> De volgende informatie is **alleen** vereist als u een andere virtuele netwerk apparaten (NVA) dan Azure firewall wilt configureren.

Azure Firewall wordt automatisch geconfigureerd om verkeer toe te staan voor veel van de algemene belang rijke scenario's. Als u een ander virtueel netwerk apparaat wilt gebruiken, moet u een aantal extra functies configureren. Houd rekening met de volgende factoren wanneer u uw virtuele netwerk apparaat configureert:

* Services die geschikt zijn voor service-eind punten kunnen worden geconfigureerd met Service-eind punten die leiden tot het overs laan van de NVA, meestal voor kosten-of prestatie overwegingen.
* Afhankelijkheden van IP-adressen zijn voor niet-HTTP/S-verkeer (TCP-en UDP-verkeer).
* FQDN HTTP/HTTPS-eind punten kunnen worden white list in uw NVA-apparaat.
* Wijs de route tabel toe die u hebt gemaakt voor uw HDInsight-subnet.

## <a name="service-endpoint-capable-dependencies"></a>Afhankelijkheden voor service-eind punten

U kunt eventueel een of meer van de volgende service-eind punten inschakelen, waardoor de NVA wordt overgeslagen. Deze optie kan handig zijn voor grote hoeveel heden gegevens overdracht om kosten te besparen en ook voor prestatie optimalisaties. 

| **Eindpunt** |
|---|
| Azure SQL |
| Azure Storage |
| Azure Active Directory |

### <a name="ip-address-dependencies"></a>Afhankelijkheden van IP-adressen

| **Eindpunt** | **Details** |
|---|---|
| [Hier](hdinsight-management-ip-addresses.md) gepubliceerde ip's | Deze IP-adressen zijn voor HDInsight Control Place en moeten worden opgenomen in de UDR om asymmetrische route ring te voor komen |
| Privé-IP-adressen van AAD-DS | Alleen nodig voor ESP-clusters|


### <a name="fqdn-httphttps-dependencies"></a>FQDN HTTP/HTTPS-afhankelijkheden

> [!Important]
> De onderstaande lijst bevat alleen enkele FQDN-namen die nodig kunnen zijn voor besturingssysteem-en beveiligings patches of validatie van certificaten nadat het cluster is gemaakt en tijdens de levens duur van cluster bewerkingen. U kunt de lijst met afhankelijkheden van FQDN-namen (voornamelijk Azure Storage en Azure Service Bus) voor het configureren van uw NVA [in dit bestand](https://github.com/Azure-Samples/hdinsight-fqdn-lists/blob/master/HDInsightFQDNTags.json)ophalen. Deze afhankelijkheden worden gebruikt door de HDInsight-resource provider (RP) om clusters te maken en te controleren en te beheren. Dit zijn onder andere telemetrie/Diagnostische logboeken, het inrichten van meta gegevens, aan het cluster gerelateerde configuraties, scripts, ARM-sjablonen, enzovoort. De lijst met FQDN-afhankelijkheden kan worden gewijzigd met het vrijgeven van toekomstige HDIngisht-updates.

| **Eindpunt**                                                          |
|---|
| azure.archive.ubuntu.com:80                                           |
| security.ubuntu.com:80                                                |
| ocsp.msocsp.com:80                                                    |
| ocsp.digicert.com:80                                                  |

## <a name="next-steps"></a>Volgende stappen

* [Firewall gebruiken om uitgaand verkeer te beperken](./hdinsight-restrict-outbound-traffic.md)
* [Azure HDInsight Virtual Network-architectuur](hdinsight-virtual-network-architecture.md)
