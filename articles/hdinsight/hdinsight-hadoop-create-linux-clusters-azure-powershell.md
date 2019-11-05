---
title: Apache Hadoop clusters maken met behulp van Power shell-Azure HDInsight
description: Meer informatie over het maken van Apache Hadoop-, Apache HBase-, Apache Storm-of Apache Spark-clusters op Linux voor HDInsight met behulp van Azure PowerShell.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 05/24/2019
ms.author: hrasheed
ms.openlocfilehash: a6847e75a0a6dcf944b033054ac466841294d28b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494786"
---
# <a name="create-linux-based-clusters-in-hdinsight-using-azure-powershell"></a>Op Linux gebaseerde clusters maken in HDInsight met behulp van Azure PowerShell

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

Azure PowerShell is een krachtige script omgeving die u kunt gebruiken om de implementatie en het beheer van uw workloads in Microsoft Azure te beheren en te automatiseren. Dit document bevat informatie over het maken van een HDInsight-cluster op basis van Linux met behulp van Azure PowerShell. Het bevat ook een voorbeeld script.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Voordat u deze procedure start, moet u het volgende hebben:

* Een Azure-abonnement. Zie [Gratis proefversie van Azure ophalen](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* [Azure PowerShell](/powershell/azure/install-Az-ps)

    > [!IMPORTANT]  
    > Azure PowerShell-ondersteuning voor het beheer van HDInsight-resources met behulp van Azure Service Manager is **afgeschaft** en per 1 januari 2017 verdwenen. In de stappen in dit document worden de nieuwe HDInsight-cmdlets gebruikt die met Azure Resource Manager werken.
    >
    > Volg de stappen in [Install Azure PowerShell](https://docs.microsoft.com/powershell/azure/install-Az-ps) om de meest recente versie van Azure PowerShell te installeren. Als u scripts hebt die moeten worden gewijzigd om ze te kunnen gebruiken met de nieuwe cmdlets die werken met Azure Resource Manager, raadpleegt u [Migrating to Azure Resource Manager-based development tools for HDInsight clusters](hdinsight-hadoop-development-using-azure-resource-manager.md) (Migreren naar op Azure Resource Manager gebaseerde hulpprogramma’s voor HDInsight-clusters) voor meer informatie.

## <a name="create-cluster"></a>Cluster maken

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Als u een HDInsight-cluster wilt maken met behulp van Azure PowerShell, moet u de volgende procedures uitvoeren:

* Een Azure-resource groep maken
* Een Azure Storage-account maken
* Een Azure Blob-container maken
* Een HDInsight-cluster maken

In het volgende script ziet u hoe u een nieuw cluster maakt:

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster.ps1?range=5-71)]

De waarden die u opgeeft voor de cluster aanmelding worden gebruikt voor het maken van het Hadoop-gebruikers account voor het cluster. Gebruik dit account om verbinding te maken met services die worden gehost op het cluster, zoals web-UIs of REST-Api's.

De waarden die u voor de SSH-gebruiker opgeeft, worden gebruikt voor het maken van de SSH-gebruiker voor het cluster. Gebruik dit account om een externe SSH-sessie op het cluster te starten en taken uit te voeren. Zie het document [SSH gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) voor meer informatie.

> [!IMPORTANT]  
> Als u van plan bent om meer dan 32 werk knooppunten te gebruiken (bij het maken van een cluster of door het cluster te schalen na het maken), moet u ook een hoofd knooppunt grootte met ten minste 8 kernen en 14 GB RAM opgeven.
>
> Zie [Prijsdetails voor Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) voor meer informatie over knooppuntgrootten en de bijbehorende kosten.

Het kan Maxi maal 20 minuten duren om een cluster te maken.

## <a name="create-cluster-configuration-object"></a>Cluster maken: configuratie object

U kunt ook een HDInsight-configuratie object maken met behulp van `New-AzHDInsightClusterConfig`-cmdlet. U kunt dit configuratie object vervolgens wijzigen om aanvullende configuratie opties voor uw cluster in te scha kelen. Gebruik ten slotte de para meter `-Config` van de cmdlet `New-AzHDInsightCluster` om de configuratie te gebruiken.

Met het volgende script maakt u een configuratie object voor het configureren van een R Server in het HDInsight-cluster type. Met de configuratie kunt u een Edge-knoop punt, RStudio en een extra opslag account maken.

[!code-powershell[main](../../powershell_scripts/hdinsight/create-cluster/create-cluster-with-config.ps1?range=59-99)]

> [!WARNING]  
> Het gebruik van een opslag account op een andere locatie dan het HDInsight-cluster wordt niet ondersteund. Wanneer u dit voor beeld gebruikt, maakt u het extra opslag account op dezelfde locatie als de-server.

## <a name="customize-clusters"></a>Clusters aanpassen

* Zie [HDInsight-clusters aanpassen met Boots trap](hdinsight-hadoop-customize-cluster-bootstrap.md#use-azure-powershell).
* Zie [HDInsight-clusters aanpassen met script actie](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="delete-the-cluster"></a>Het cluster verwijderen

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Problemen oplossen

Zie [Vereisten voor toegangsbeheer](hdinsight-hadoop-create-linux-clusters-portal.md) als u problemen ondervindt met het maken van HDInsight-clusters.

## <a name="next-steps"></a>Volgende stappen

Nu u een HDInsight-cluster hebt gemaakt, gebruikt u de volgende bronnen voor meer informatie over het werken met uw cluster.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop clusters

* [Apache Hive gebruiken met HDInsight](hadoop/hdinsight-use-hive.md)
* [Apache Pig gebruiken met HDInsight](hadoop/hdinsight-use-pig.md)
* [MapReduce gebruiken met HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-clusters

* [Aan de slag met Apache HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Ontwikkel Java-toepassingen voor Apache HBase in HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="storm-clusters"></a>Storm-clusters

* [Java-topologieën ontwikkelen voor Storm op HDInsight](storm/apache-storm-develop-java-topology.md)
* [Python-onderdelen gebruiken in Storm op HDInsight](storm/apache-storm-develop-python-topology.md)
* [Topologieën implementeren en bewaken met Storm op HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark clusters

* [Een zelfstandige toepassing maken met behulp van Scala](spark/apache-spark-create-standalone-application.md)
* [Apache Livy gebruiken om taken op afstand uit te voeren in een Apache Spark-cluster](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark met BI: interactieve gegevens analyses uitvoeren met behulp van Spark in HDInsight met BI-hulpprogram ma's](spark/apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken om voedsel inspectie resultaten te voors pellen](spark/apache-spark-machine-learning-mllib-ipython.md)

