---
title: Apache Kafka verhogen schaal - Azure HDInsight
description: Leer hoe u beheerde schijven voor een Apache Kafka-cluster configureert in Azure HDInsight om de schaalbaarheid te verhogen.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/09/2019
ms.openlocfilehash: 56c25b7c77809a5cb7f4e539cff8e1815cd9976f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77031699"
---
# <a name="configure-storage-and-scalability-for-apache-kafka-on-hdinsight"></a>Opslag en schaalbaarheid configureren voor Apache Kafka in HDInsight

Meer informatie over het configureren van het aantal beheerde schijven dat [door Apache Kafka](https://kafka.apache.org/) wordt gebruikt op HDInsight.

Kafka in HDInsight maakt gebruik van de lokale schijf van de virtuele machines in het HDInsight-cluster. Aangezien Kafka veel gebruikmaakt van invoer/uitvoer, wordt [Azure Managed Disks](../../virtual-machines/windows/managed-disks-overview.md) gebruikt voor een hoge doorvoer en meer opslag per knooppunt. Als u de traditionele VHD (virtuele harde schijven) gebruikt voor Kafka, heeft elk knooppunt een limiet van 1 TB. Met beheerde schijven kunt u meerdere schijven gebruiken en zodat elk knooppunt in het cluster een limiet heeft van 16 TB.

In het volgende diagram ziet u een vergelijking tussen Kafka in HDInsight voordat beheerde schijven werden gebruikt, en Kafka in HDInsight met beheerde schijven:

![kafka met beheerde schijven architectuur](./media/apache-kafka-scalability/kafka-with-managed-disks-architecture.png)

## <a name="configure-managed-disks-azure-portal"></a>Beheerde schijven configureren: Azure Portal

1. Volg de stappen in [Een HDInsight-cluster maken](../hdinsight-hadoop-create-linux-clusters-portal.md) voor de algemene stappen voor het maken van een cluster met behulp van de portal. Voltooi het proces voor het maken van portalen niet.

2. Gebruik in de sectie **Configuration & Pricing** het veld Aantal __knooppunten__ om het aantal schijven te configureren.

    > [!NOTE]  
    > Het type beheerde schijf is __Standaard__ (HDD) of __Premium__ (SSD). Premium-schijven worden gebruikt met virtuele machines uit de DS- en GS-reeks. Alle andere VM-typen gebruiken standaardschijven.

    ![clustergroottesectie met de schijven per schijfknooppunt gemarkeerd](./media/apache-kafka-scalability/azure-portal-cluster-configuration-pricing-kafka-disks.png)

## <a name="configure-managed-disks-resource-manager-template"></a>Beheerde schijven configureren: Resource Manager-sjabloon

Als u het aantal schijven wilt beheren dat wordt gebruikt in de werkknooppunten van een Kafka-cluster, gebruikt u de volgende sectie van de sjabloon:

```json
"dataDisksGroups": [
    {
        "disksPerNode": "[variables('disksPerWorkerNode')]"
    }
    ],
```

U een volledige sjabloon vinden die laat [https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json](https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-mirror-cluster-in-vnet-v2.1.json)zien hoe u beheerde schijven configureert op.

## <a name="next-steps"></a>Volgende stappen

Zie de volgende documenten voor meer informatie over het werken met Apache Kafka op HDInsight:

* [Gebruik MirrorMaker om een replica van Apache Kafka te maken op HDInsight](apache-kafka-mirroring.md)
* [Gebruik Apache Storm met Apache Kafka op HDInsight](../hdinsight-apache-storm-with-kafka.md)
* [Apache Spark gebruiken met Apache Kafka op HDInsight](../hdinsight-apache-spark-with-kafka.md)
* [Verbinding maken met Apache Kafka via een Azure Virtual Network](apache-kafka-connect-vpn-gateway.md)

* [HDInsight blog over beheerde schijven met Apache Kafka](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/)
