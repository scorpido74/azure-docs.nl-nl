---
title: Ondersteunde knooppunt configuraties van Azure HDInsight
description: Meer informatie over de minimale en aanbevolen configuraties voor HDInsight-cluster knooppunten.
keywords: VM-grootten, cluster grootte, cluster configuratie
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,hdiseo17may2017
ms.topic: conceptual
ms.date: 08/26/2019
ms.openlocfilehash: e482cf9b5367beba00784e69c5bad88142df5225
ms.sourcegitcommit: 82499878a3d2a33a02a751d6e6e3800adbfa8c13
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 08/28/2019
ms.locfileid: "70076210"
---
# <a name="what-are-the-default-and-recommended-node-configurations-for-azure-hdinsight"></a>Wat zijn de standaard en aanbevolen knooppunt configuraties voor Azure HDInsight?

In dit artikel worden de standaard-en aanbevolen knooppunt configuraties voor Azure HDInsight-clusters besproken.

## <a name="default-and-recommended-node-configuration-and-virtual-machine-sizes-for-clusters"></a>Standaard en aanbevolen knooppunt configuratie en grootte van virtuele machines voor clusters

In de volgende tabellen worden de standaard en aanbevolen VM-grootten (Virtual Machine) weer geven voor HDInsight-clusters.  Deze informatie is nodig om inzicht te krijgen in de VM-grootten die moeten worden gebruikt bij het maken van Power shell-of Azure CLI-scripts voor het implementeren van HDInsight-clusters. 

Als u meer dan 32 worker-knoop punten in een cluster nodig hebt, selecteert u een hoofd knooppunt grootte met ten minste 8 kernen en 14 GB aan RAM-geheugen. 

De enige cluster typen met gegevens schijven zijn Kafka-en HBase-clusters waarvoor de functie voor versneld schrijven is ingeschakeld. HDInsight ondersteunt de P30-en S30-schijf grootten in deze scenario's.

Raadpleeg de volgende documenten voor meer informatie over de specificatie van elk VM-type:

* [Grootte van virtuele machines voor algemene doel einden: Dv2-serie 1-5](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#dv2-series)
* [Grootte van virtuele machines geoptimaliseerd voor geheugen: Dv2-serie 11-15](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory#dv2-series-11-15)
* [Grootte van virtuele machines voor algemene doel einden: Av2-serie 1-8](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-general#av2-series)

### <a name="all-supported-regions-except-brazil-south-and-japan-west"></a>Alle ondersteunde regio's met uitzonde ring van Brazilië-Zuid en Japan-West

> [!Note]
> Als u de SKU-id wilt ophalen voor gebruik in Power shell en `Standard_` andere scripts, voegt u aan het begin van alle VM-sku's in de onderstaande tabellen toe. Dit wordt bijvoorbeeld `D12_v2`. `Standard_D12_v2`

| Clustertype | Hadoop | HBase | Interactive Query | Storm | Spark | ML Server | Kafka |
|---|---|---|---|---|---|---|---|
| Head: standaard VM-grootte | D12_v2 | D12_v2 | D13_v2 | A4_v2 | D12_v2 | D12_v2 | D3_v2 |
| Kop: aanbevolen VM-grootten | D13_v2,<br/>D14_v2,<br/>D5_v2 | D3_v2,<br/>D4_v2,<br/>D12_v2 | D13_v2,<br/>D14_v2 | A4_v2,<br/>A8_v2 | D12_v2,<br/>D13_v2,<br/>D14_v2 | D12_v2,<br/>D13_v2,<br/>D14_v2 | D3_v2,<br/>D4_v2,<br/>D12_v2 |
| Werk nemer: standaard VM-grootte | D4_v2 | D4_v2 | D14_v2 | D3_v2 | D13_v2 | D4_v2 | 4 D12_v2 met 2 S30 schijven per Broker |
| Werk nemer: aanbevolen VM-grootten | D5_v2,<br>D12_v2,<br/>D13_v2 | D3_v2,<br/>D4_v2,<br/>D13_v2 | D13_v2,<br/>D14_v2 | D3_v2<br/>D4_v2,<br/>D12_v2 | D12_v2,<br>D13_v2,<br>D14_v2 | D4_v2,<br/>D12_v2,<br>D13_v2,<br>D14_v2 | D3_v2,<br/>D4_v2,<br/>DS3_v2,<br/>DS4_v2 |
| ZooKeeper: standaard VM-grootte |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 | A4_v2 |
| ZooKeeper: aanbevolen VM-grootten |  | A4_v2, <br/>A8_v2, <br/>A2m_v2 | A4_v2,<br/>A8_v2,<br/>A2m_v2 | A4_v2,<br/>A2_v2,<br/>A8_v2 |  | A2_v2 | A4_v2,<br/> A8_v2,<br/>A2m_v2 |
| ML Services: standaard VM-grootte |  |  |  |  |  | D4_v2 |  |
| ML Services: aanbevolen VM-grootte |  |  |  |  |  | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/>D14_v2 |  |

### <a name="brazil-south-and-japan-west-only"></a>Alleen Brazilië-Zuid en Japan-West

| Clustertype | Hadoop | HBase | Interactive Query | Storm | Spark | ML-services |
|---|---|---|---|---|---|---|
| Head: standaard VM-grootte | D12 | D12 | D13 | A4_v2 | D12 | D12 |
| Kop: aanbevolen VM-grootten | D5_v2,<br/> D13_v2,<br/> D14_v2 | D3_v2,<br/> D4_v2,<br/> D12_v2 | D13_v2,<br/> D14_v2 | A4_v2,<br/> A8_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 |
| Werk nemer: standaard VM-grootte | D4 | D4 | D14 | D3 | D13 | D4 |
| Werk nemer: aanbevolen VM-grootten | D5_v2,<br/> D12_v2,<br/> D13_v2 | D3_v2,<br/> D4_v2,<br/> D13_v2 | D13_v2,<br/> D14_v2 | D3_v2,<br/> D4_v2,<br/> D12_v2 | D12_v2,<br/> D13_v2,<br/> D14_v2 | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/> D14_v2 |
| ZooKeeper: standaard VM-grootte |  | A4_v2 | A4_v2 | A4_v2 |  | A2_v2 |
| ZooKeeper: aanbevolen VM-grootten |  | A4_v2,<br/> A8_v2,<br/> A2m_v2 | A4_v2,<br/> A8_v2,<br/> A2m_v2 | A4_v2,<br/> A8_v2 |  | A2_v2 |
| ML Services: standaard VM-grootten |  |  |  |  |  | D4 |
| ML Services: aanbevolen VM-grootten |  |  |  |  |  | D4_v2,<br/> D12_v2,<br/> D13_v2,<br/> D14_v2 |

> [!NOTE]
> - Head staat bekend als *Nimbus* voor het Storm-cluster type.
> - Werk nemer staat bekend als *Super Visor* voor het Storm-cluster type.
> - De werk nemer staat bekend als *regio* voor het cluster type HBase.

## <a name="next-steps"></a>Volgende stappen

* [Wat zijn de Apache Hadoop-onderdelen en versies die beschikbaar met HDInsight?](hdinsight-component-versioning.md)
