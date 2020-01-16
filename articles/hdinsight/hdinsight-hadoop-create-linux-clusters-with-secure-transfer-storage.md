---
title: Apache Hadoop & beveiligde overdrachts opslag-Azure HDInsight
description: Informatie over het maken van HDInsight-clusters met Azure-opslag-accounts voor veilige overdracht.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/04/2019
ms.openlocfilehash: bcb0e9551f4415b2aac9eb2d641c91df9f692437
ms.sourcegitcommit: 3dc1a23a7570552f0d1cc2ffdfb915ea871e257c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/15/2020
ms.locfileid: "75979123"
---
# <a name="create-apache-hadoop-cluster-with-secure-transfer-storage-accounts-in-azure-hdinsight"></a>Apache Hadoop cluster maken met opslag accounts voor veilige overdracht in azure HDInsight

De functie [Veilige overdracht vereist](../storage/common/storage-require-secure-transfer.md) verhoogt de beveiliging van uw Azure-opslagaccount door alle aanvragen naar uw account af te dwingen via een beveiligde verbinding. Deze functie en het wasbs-schema worden alleen ondersteund door HDInsight-clusterversie 3.6 of nieuwer.

## <a name="prerequisites"></a>Vereisten

Voordat u aan dit artikel begint, hebt u het volgende nodig:

* Azure-abonnement: als u een gratis proef account voor één maand wilt maken, gaat u naar [Azure.Microsoft.com/Free](https://azure.microsoft.com/free).
* Een Azure Storage-account waarvoor beveiligde overdracht is ingeschakeld. Zie [Een opslagaccount maken](../storage/common/storage-account-create.md) en [Veilige overdracht vereisen](../storage/common/storage-require-secure-transfer.md) voor instructies. Voor het inschakelen van beveiligde opslag overdracht na het maken van een cluster zijn extra stappen vereist die niet in dit artikel worden besproken.
* Een BLOB-container op het opslag account.

## <a name="create-cluster"></a>Cluster maken

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

In deze sectie maakt u een Hadoop-cluster in HDInsight met behulp van een [Azure Resource Manager-sjabloon](../azure-resource-manager/templates/deploy-powershell.md). De sjabloon bevindt zich in [github](https://azure.microsoft.com/resources/templates/101-hdinsight-linux-with-existing-default-storage-account/). De Resource Manager-sjabloon ervaring is niet vereist voor het volgen van dit artikel. Zie [HDInsight-clusters maken](hdinsight-hadoop-provision-linux-clusters.md)voor andere methoden voor het maken van een cluster en over de eigenschappen die in dit artikel worden gebruikt.

1. Klik op de volgende afbeelding om u aan te melden bij Azure en de Resource Manager-sjabloon in Azure Portal te openen.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-hdinsight-linux-with-existing-default-storage-account%2Fazuredeploy.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage/hdi-deploy-to-azure1.png" alt="Deploy to Azure button for new cluster"></a>

2. Volg de instructies voor het maken van het cluster met de volgende specificaties:

    * Geef HDInsight versie 3.6 op. Versie 3.6 of hoger is vereist.
    * Geef een opslagaccount met veilige overdracht op.
    * Gebruik de korte naam voor het opslagaccount.
    * Zowel het opslagaccount als de blob-container moeten vooraf zijn gemaakt.

      Zie [Cluster maken](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) voor instructies.

Als u een scriptactie gebruikt om uw eigen configuratiebestanden te verstrekken, moet u wasbs gebruiken in de volgende instellingen:

* fs.defaultFS (core-site)
* spark.eventLog.dir
* spark.history.fs.logDirectory

## <a name="add-additional-storage-accounts"></a>Extra opslagaccounts toevoegen

Er zijn verschillende opties om extra opslagaccounts met veilige overdracht toe te voegen:

* De Azure Resource Manager-sjabloon in de laatste sectie wijzigen.
* Een cluster maken met [Azure Portal](https://portal.azure.com) en een gekoppelde opslagaccount opgeven.
* Een scriptactie gebruiken om extra opslagaccounts met veilige overdracht toe te voegen aan een bestaand HDInsight-cluster. Zie [Extra opslagaccounts toevoegen aan HDInsight](hdinsight-hadoop-add-storage.md) voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een HDInsight-cluster maakt en hoe u beveiligde overdracht naar de opslag accounts inschakelt.

Zie de volgende artikelen voor meer informatie over het analyseren van gegevens met HDInsight:

* Zie [Apache Hive gebruiken met hdinsight](hadoop/hdinsight-use-hive.md)voor meer informatie over het gebruik van [Apache Hive](https://hive.apache.org/) met hdinsight, waaronder het uitvoeren van Hive-query's vanuit Visual Studio.
* Zie [Apache Hadoop MapReduce gebruiken met HDInsight](hadoop/hdinsight-use-mapreduce.md)voor meer informatie over [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), een manier om Program ma's te schrijven die gegevens verwerken op Hadoop.
* Zie aan de [slag met Visual studio Apache Hadoop-hulpprogram ma's voor hdinsight voor](hadoop/apache-hadoop-visual-studio-tools-get-started.md)meer informatie over het gebruik van Hdinsight-hulpprogram Ma's voor Visual Studio voor het analyseren van gegevens op hdinsight.

Zie de volgende artikelen voor meer informatie over hoe HDInsight gegevens opslaat en over het ophalen van gegevens in HDInsight:

* Zie [Azure Storage gebruiken met HDInsight](hdinsight-hadoop-use-blob-storage.md) voor meer informatie over hoe HDInsight Azure Storage gebruikt.
* Zie [Gegevens uploaden naar HDInsight](hdinsight-upload-data.md) voor meer informatie over het uploaden van gegevens naar HDInsight.

Zie de volgende artikelen voor meer informatie over het maken of beheren van een HDInsight-cluster:

* Zie [HDInsight-clusters beheren met Apache Ambari](hdinsight-hadoop-manage-ambari.md) voor meer informatie over het beheren van uw HDInsight-cluster op basis van Linux.
* Zie voor meer informatie over de opties die u kunt selecteren bij het maken van een HDInsight-cluster [HDInsight op Linux maken met aangepaste opties](hdinsight-hadoop-provision-linux-clusters.md).
* Zie [werken met HDInsight op Linux](hdinsight-hadoop-linux-information.md)als u bekend bent met Linux en Apache Hadoop, maar u meer wilt weten over Hadoop op de HDInsight. Dit artikel biedt informatie zoals:

  * Url's voor services die worden gehost op het cluster, zoals [Apache Ambari](https://ambari.apache.org/) en [WebHCat](https://cwiki.apache.org/confluence/display/Hive/WebHCat)
  * De locatie van [Apache Hadoop](https://hadoop.apache.org/) bestanden en voor beelden op het lokale bestands systeem
  * Het gebruik van Azure Storage (WASB) in plaats van [Apache HADOOP HDFS](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsUserGuide.html) als de standaard gegevens opslag
