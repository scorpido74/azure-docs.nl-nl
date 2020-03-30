---
title: Apache Spark streaming met Apache Kafka - Azure HDInsight
description: Meer informatie over het gebruik van Apache Spark om gegevens naar of uit Apache Kafka te streamen met DStreams. In dit voorbeeld gaat u met behulp van een Jupyter Notebook gegevens streamen van Spark naar HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 11/21/2019
ms.openlocfilehash: d868cdd346c79cf77d4f8c1ea6e4b20adcd99b6c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "74327395"
---
# <a name="apache-spark-streaming-dstream-example-with-apache-kafka-on-hdinsight"></a>Apache Spark streaming (DStream) voorbeeld met Apache Kafka op HDInsight

Meer informatie over het gebruik van [Apache Spark](https://spark.apache.org/) om gegevens te streamen naar of uit [Apache Kafka](https://kafka.apache.org/) op HDInsight met Behulp van [DStreams](https://spark.apache.org/docs/latest/api/java/org/apache/spark/streaming/dstream/DStream.html). In dit voorbeeld wordt een [Jupyter-notitieblok](https://jupyter.org/) gebruikt dat wordt uitgevoerd op het Spark-cluster.

> [!NOTE]  
> Met de stappen in dit document wordt een Azure-resourcegroep gemaakt die zowel een Spark in HDInsight- als een Kafka in HDInsight-cluster bevat. Deze clusters bevinden zich beide binnen een Azure Virtual Network, waardoor het Spark-cluster rechtstreeks kan communiceren met het Kafka-cluster.
>
> Nadat u de stappen in dit document hebt doorlopen, moet u niet vergeten de clusters te verwijderen om overtollige kosten te voorkomen.

> [!IMPORTANT]  
> In dit voorbeeld wordt dstreams gebruikt, een oudere Spark-streamingtechnologie. Zie het document Spark Structured Streaming with Apache Kafka voor een voorbeeld dat nieuwere [Spark-streamingfuncties](hdinsight-apache-kafka-spark-structured-streaming.md) gebruikt.

## <a name="create-the-clusters"></a>De clusters maken

Apache Kafka op HDInsight biedt geen toegang tot de Kafka makelaars via het openbare internet. Alles wat met Kafka praat, moet zich in hetzelfde Azure virtuele netwerk bevinden als de knooppunten in het Kafka-cluster. In dit voorbeeld bevinden zowel de clusters Kafka als Spark zich in een virtueel Azure-netwerk. In het volgende diagram ziet u hoe de communicatie tussen de clusters verloopt:

![Diagram van Spark- en Kafka-clusters in een Azure Virtual Network](./media/hdinsight-apache-spark-with-kafka/apache-spark-kafka-vnet.png)

> [!NOTE]  
> Hoewel Kafka zelf beperkt is tot communicatie binnen het virtuele netwerk, zijn andere diensten op het cluster, zoals SSH en Ambari, toegankelijk via het internet. Zie [Poorten en URI's die worden gebruikt door HDInsight](hdinsight-hadoop-port-settings-for-services.md) voor meer informatie over de openbare poorten die beschikbaar zijn voor HDInsight.

Hoewel u handmatig een Azure-virtueel netwerk, Kafka en Spark-clusters maken, is het eenvoudiger om een Azure Resource Manager-sjabloon te gebruiken. Gebruik de volgende stappen om een Azure virtueel netwerk, Kafka en Spark-clusters te implementeren in uw Azure-abonnement.

1. Gebruik de volgende knop om u aan te melden bij Azure en de sjabloon in de Azure Portal te openen.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Farmtemplates%2Fcreate-linux-based-kafka-spark-cluster-in-vnet-v4.1.json" target="_blank"><img src="./media/hdinsight-apache-spark-with-kafka/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

    De sjabloon Azure Resource **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-kafka-spark-cluster-in-vnet-v4.1.json**Manager bevindt zich op .

    > [!WARNING]  
    > Om beschikbaarheid van Kafka op HDInsight te garanderen, moet uw cluster ten minste drie werkknooppunten bevatten. Met deze sjabloon maakt u een Kafka-cluster dat drie werkknooppunten bevat.

    Deze sjabloon maakt een HDInsight 3.6-cluster voor zowel Kafka als Spark.

1. Gebruik de volgende gegevens om de vermeldingen in de sectie **Aangepaste implementatie** in te vullen:

    |Eigenschap |Waarde |
    |---|---|
    |Resourcegroep|Maak een groep of selecteer een bestaande groep.|
    |Locatie|Selecteer een locatie die geografisch dicht bij u staat.|
    |Naam basiscluster|Deze waarde wordt gebruikt als basisnaam voor de spark- en kafkaclusters. Als u bijvoorbeeld **hdistreaming** binnenvoert, wordt een Spark-cluster met de naam __spark-hdistreaming__ en een Kafka-cluster met de naam **kafka-hdistreaming .**|
    |Gebruikersnaam voor clusteraanmelding|De beheerdersgebruikersnaam voor de spark- en kafkaclusters.|
    |Wachtwoord voor clusteraanmelding|Het beheerdersgebruikerswachtwoord voor de spark- en kafkaclusters.|
    |SSH-gebruikersnaam|De SSH-gebruiker om te maken voor de Spark- en Kafka-clusters.|
    |SSH-wachtwoord|Het wachtwoord voor de SSH-gebruiker voor de Spark- en Kafka-clusters.|

    ![Parameters voor aangepaste implementatie van HDInsight](./media/hdinsight-apache-spark-with-kafka/hdinsight-parameters.png)

1. Lees de **voorwaarden** en schakel vervolgens het selectievakje **Ik ga akkoord met de bovenstaande voorwaarden** in.

1. Selecteer Tot slot **Aankoop**. Het duurt ongeveer 20 minuten om de clusters te maken.

Zodra de bronnen zijn gemaakt, wordt een overzichtspagina weergegeven.

![Samenvatting van resourcegroepen voor het vnet en clusters](./media/hdinsight-apache-spark-with-kafka/hdinsight-group-blade.png)

> [!IMPORTANT]  
> Merk op dat de namen van de **HDInsight-clusters spark-BASENAME** en **kafka-BASENAME**zijn, waarbij BASENAME de naam is die u aan de sjabloon hebt opgegeven. U gebruikt deze namen in latere stappen wanneer u verbinding maakt met de clusters.

## <a name="use-the-notebooks"></a>De notitieblokken gebruiken

De code voor het voorbeeld dat [https://github.com/Azure-Samples/hdinsight-spark-scala-kafka](https://github.com/Azure-Samples/hdinsight-spark-scala-kafka)in dit document wordt beschreven, is beschikbaar op .

## <a name="delete-the-cluster"></a>Het cluster verwijderen

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Aangezien de stappen in dit document beide clusters in dezelfde Azure-brongroep maken, u de brongroep in de Azure-portal verwijderen. Als u de groep verwijdert, worden alle resources verwijderd die zijn gemaakt door dit document, het Azure Virtual Network en het opslagaccount te volgen dat door de clusters wordt gebruikt.

## <a name="next-steps"></a>Volgende stappen

In dit voorbeeld heb je geleerd hoe je Spark gebruiken om te lezen en naar Kafka te schrijven. Gebruik de volgende koppelingen om andere manieren te ontdekken om met Kafka te werken:

* [Aan de slag met Apache Kafka op HDInsight](kafka/apache-kafka-get-started.md)
* [Gebruik MirrorMaker om een replica van Apache Kafka te maken op HDInsight](kafka/apache-kafka-mirroring.md)
* [Gebruik Apache Storm met Apache Kafka op HDInsight](hdinsight-apache-storm-with-kafka.md)
