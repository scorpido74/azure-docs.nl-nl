---
title: Azure HDInsight ondersteunde knooppuntconfiguraties
description: Meer informatie over de minimale en aanbevolen configuraties voor HDInsight-clusterknooppunten.
keywords: vm-grootten, clustergroottes, clusterconfiguratie
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e75146266568001d8fee7be26898ac8bdfffb7fc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77484783"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Wat zijn de standaard- en aanbevolen knooppuntconfiguraties voor Azure HDInsight?

In dit artikel worden standaard- en aanbevolen knooppuntconfiguraties voor Azure HDInsight-clusters besproken.

## <a name="default-and-minimum-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Standaard- en minimale aanbevolen knooppuntconfiguratie en virtuele machineformaten voor clusters

In de volgende tabellen worden standaard- en aanbevolen vm-formaten (virtuele machines) weergegeven voor HDInsight-clusters.  Deze informatie is nodig om inzicht te krijgen in de VM-formaten die u wilt gebruiken bij het maken van PowerShell- of Azure CLI-scripts om HDInsight-clusters te implementeren.

Als u meer dan 32 werkknooppunten in een cluster nodig hebt, selecteert u een hoofdknooppuntgrootte met ten minste 8 cores en 14 GB RAM. 

De enige clustertypen met gegevensschijven zijn Kafka- en HBase-clusters met de functie Versnelde schrijfbewerkingen ingeschakeld. HDInsight ondersteunt in deze scenario's de schijfformaten P30 en S30.

De specificaties van alle minimumaanbevolen VM-typen die in dit document worden gebruikt, worden samengevat in de volgende tabel.

| Grootte              | vCPU | Geheugen: GiB | Tijdelijke opslag (SSD) GiB | Maximale tijdelijke opslagdoorvoer: IOPS / MBps lezen / MBps schrijven | Maximumaantal gegevensschijven / doorvoer: IOPS | Max NIC's / Verwachte netwerkbandbreedte (Mbps) |
|-------------------|-----------|-------------|----------------|----------------------------------------------------------|-----------------------------------|------------------------------|
| Standard_D3_v2 | 4    | 14          | 200                    | 12.000 / 187 / 93                                           | 16 / 16x500           | 4 / 3000                                       |
| Standard_D4_v2 | 8    | 28          | 400                    | 24.000 / 375 / 187                                          | 32 / 32x500           | 8 / 6000                                       |
| Standard_D5_v2 | 16   | 56          | 800                    | 48.000 / 750 / 375                                          | 64 / 64x500           | 8 / 12000                                    |
| Standard_D12_v2   | 4         | 28          | 200            | 12.000 / 187 / 93                                         | 16 / 16 x 500                         | 4 / 3000                     |
| Standard_D13_v2   | 8         | 56          | 400            | 24.000 / 375 / 187                                        | 32 / 32 x 500                       | 8 / 6000                     |
| Standard_D14_v2   | 16        | 112         | 800            | 48.000 / 750 / 375                                        | 64 / 64x500                       | 8 / 12000          |
| Standard_A1_v2  | 1         | 2           | 10             | 1000 / 20 / 10                                           | 2 / 2 x 500               | 2 / 250                 |
| Standard_A2_v2  | 2         | 4           | 20             | 2000 / 40 / 20                                           | 4 / 4 x 500               | 2 / 500                 |
| Standard_A4_v2  | 4         | 8           | 40             | 4000 / 80 / 40                                           | 8 / 8 x 500               | 4 / 1000                     |

Zie de volgende documenten voor meer informatie over de specificaties van elk VM-type:

* [Algemene doeleinden virtuele machine maten: Dv2 serie 1-5](../virtual-machines/dv2-dsv2-series.md)
* [Geheugen geoptimaliseerde virtuele machine maten: Dv2 serie 11-15](../virtual-machines/dv2-dsv2-series-memory.md)
* [Algemene gebruik virtuele machine maten: Av2 serie 1-8](../virtual-machines/av2-series.md)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Alle ondersteunde regio's behalve Brazilië zuid en Japan westen

> [!Note]
> Als u de SKU-id wilt krijgen voor `Standard_` gebruik in powershell en andere scripts, voegt u in de onderstaande tabellen aan het begin van alle VM-SKU's toe. Bijvoorbeeld, `D12_v2` zou `Standard_D12_v2`worden .

| Clustertype | Hadoop | HBase | Interactive Query | Storm | Spark | ML-server | Kafka |
|---|---|---|---|---|---|---|---|
| Hoofd: standaard VM-grootte | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2. <br/>D13_v2* | D12_v2 | D3_v2 |
| Hoofd: minimale aanbevolen VM-formaten | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2. <br/>D13_v2* | D12_v2 | D3_v2 |
| Werknemer: standaardVM-grootte | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 met 2 S30 schijven per broker |
| Werknemer: minimale aanbevolen VM-formaten | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 | D3_v2 |
| ZooKeeper: standaard VM-grootte |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: minimale aanbevolen VM-maten |  | A4_v2 | A4_v2 | A2_v2 |  | A2_v2 | A4_v2 |
| ML-services: standaardVM-grootte |  |  |  |  |  | D4_v2 |  |
| ML-services: minimale aanbevolen VM-grootte |  |  |  |  |  | D4_v2 |  |

\*= VM-formaten voor ESP-clusters (Spark Enterprise Security Package)

### <a name="brazil-south-and-japan-west-only"></a>Brazilië zuid en Japan west alleen

| Clustertype | Hadoop | HBase | Interactive Query | Storm | Spark | ML Services |
|---|---|---|---|---|---|---|
| Hoofd: standaard VM-grootte | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Hoofd: minimale aanbevolen VM-formaten | D5_v2 | D3_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 |
| Werknemer: standaardVM-grootte | D4 | D4 | D14 | D3 | D13 | D4 |
| Werknemer: minimale aanbevolen VM-formaten | D5_v2 | D3_v2 | D13_v2 | D3_v2 | D12_v2 | D4_v2 |
| ZooKeeper: standaard VM-grootte |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: minimale aanbevolen VM-maten |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ML-services: standaardVM-formaten |  |  |  |  |  | D4 |
| ML-services: minimale aanbevolen VM-formaten |  |  |  |  |  | D4_v2 |

> [!NOTE]
> - Head staat bekend als *Nimbus* voor het clustertype Storm.
> - Worker staat bekend als *Supervisor* voor het clustertype Storm.
> - Worker staat bekend als *Regio* voor het clustertype HBase.

## <a name="next-steps"></a>Volgende stappen

* [Wat zijn de Apache Hadoop componenten en versies beschikbaar met HDInsight?](hdinsight-component-versioning.md)
