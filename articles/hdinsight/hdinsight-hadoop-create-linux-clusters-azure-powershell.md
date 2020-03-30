---
title: Apache Hadoop-clusters maken met PowerShell - Azure HDInsight
description: Meer informatie over het maken van Apache Hadoop-, Apache HBase-, Apache Storm- of Apache Spark-clusters op Linux voor HDInsight met Azure PowerShell.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: 3c93eca493275612ac14a995140b2e91cc40fe98
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75644661"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Linux-gebaseerde clusters maken in HDInsight met Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell is een krachtige scriptomgeving die u gebruiken om de implementatie en het beheer van uw workloads in Microsoft Azure te beheren en te automatiseren. In dit document vindt u informatie over het maken van een HDInsight-cluster op basis van Linux met Azure PowerShell. Het bevat ook een voorbeeld script.

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

[Azure PowerShell](/powershell/azure/install-Az-ps) Az module.

## <a name="create-cluster"></a>Cluster maken

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Als u een HDInsight-cluster wilt maken met Azure PowerShell, moet u de volgende procedures voltooien:

* Een Azure-brongroep maken
* Een Azure Storage-account maken
* Een Azure Blob-container maken
* Een HDInsight-cluster maken

> [!NOTE]
> PowerShell gebruiken om een HDInsight-cluster te maken met Azure Data Lake Storage Gen2 wordt momenteel niet ondersteund.

In het volgende script wordt uitgelegd hoe u een nieuw cluster maakt:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

De waarden die u opgeeft voor de clusteraanmelding worden gebruikt om het Hadoop-gebruikersaccount voor het cluster te maken. Gebruik dit account om verbinding te maken met services die op het cluster worden gehost, zoals web-UI's of REST-API's.

De waarden die u opgeeft voor de SSH-gebruiker worden gebruikt om de SSH-gebruiker voor het cluster te maken. Gebruik dit account om een externe SSH-sessie op het cluster te starten en taken uit te voeren. Zie het document [SSH gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) voor meer informatie.

> [!IMPORTANT]  
> Als u van plan bent meer dan 32 werkknooppunten te gebruiken (bij het maken van het cluster of door het cluster na het maken te schalen), moet u ook een hoofdknooppuntgrootte opgeven met ten minste 8 cores en 14 GB RAM.
>
> Zie [Prijsdetails voor Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) voor meer informatie over knooppuntgrootten en de bijbehorende kosten.

Het kan tot 20 minuten duren voordat een cluster is gemaakt.

## <a name="create-cluster-configuration-object"></a>Cluster maken: object Configuratie

U ook een HDInsight-configuratieobject maken met behulp van [`New-AzHDInsightClusterConfig`](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightclusterconfig) cmdlet. Vervolgens u dit configuratieobject wijzigen om extra configuratieopties voor uw cluster in te schakelen. Gebruik ten `-Config` slotte de [`New-AzHDInsightCluster`](https://docs.microsoft.com/powershell/module/az.hdinsight/new-azhdinsightcluster) parameter van de cmdlet om de configuratie te gebruiken.

Met het volgende script wordt een configuratieobject gemaakt om een R-server op HET CLUSTERtype HDInsight te configureren. De configuratie maakt een edge node, RStudio en een extra opslagaccount mogelijk.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-99)]

> [!WARNING]  
> Het gebruik van een opslagaccount op een andere locatie dan het HDInsight-cluster wordt niet ondersteund. Maak bij het gebruik van dit voorbeeld het extra opslagaccount op dezelfde locatie als de server.

## <a name="customize-clusters"></a>Clusters aanpassen

* Zie [HDInsight-clusters aanpassen met Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Zie [HDInsight-clusters aanpassen met Scriptactie](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Het cluster verwijderen

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Problemen oplossen

Zie [Vereisten voor toegangsbeheer](hdinsight-hadoop-create-linux-clusters-portal.md) als u problemen ondervindt met het maken van HDInsight-clusters.

## <a name="next-steps"></a>Volgende stappen

Nu u een HDInsight-cluster hebt gemaakt, gebruikt u de volgende bronnen om te leren hoe u met uw cluster werken.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop clusters

* [Apache Hive gebruiken met HDInsight](hadoop/hdinsight-use-hive.md)
* [MapReduce gebruiken met HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase clusters

* [Aan de slag met Apache HBase op HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Java-toepassingen ontwikkelen voor Apache HBase op HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Stormclusters

* [Ontwikkelen Java topologieën voor Storm op HDInsight](storm/apache-storm-develop-java-topology.md)
* [Python-componenten gebruiken in Storm op HDInsight](storm/apache-storm-develop-python-topology.md)
* [Topologieën implementeren en monitoren met Storm op HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark clusters

* [Een zelfstandige toepassing maken met behulp van Scala](spark/apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark met BI: interactieve data-analyse uitvoeren met Spark in HDInsight met BI-tools](spark/apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight om de resultaten van voedselinspectie te voorspellen](spark/apache-spark-machine-learning-mllib-ipython.md)