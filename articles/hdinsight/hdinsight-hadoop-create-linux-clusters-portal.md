---
title: Apache Hadoop clusters maken met behulp van webbrowser, Azure HDInsight
description: Meer informatie over het maken van Apache Hadoop-, Apache HBase-, Apache Storm-of Apache Spark-clusters op Linux voor HDInsight met behulp van een webbrowser en de Azure Portal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 09/28/2019
ms.openlocfilehash: 3a64ba088a24f6202b74ae84ad7fb6c011d3bcbf
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494760"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Op Linux gebaseerde clusters maken in HDInsight met behulp van de Azure Portal

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

De Azure Portal is een webgebaseerd hulp programma voor het beheren van services en resources die worden gehost in de Microsoft Azure Cloud. In dit artikel leert u hoe u Azure HDInsight-clusters op basis van Linux maakt met behulp van de portal.

## <a name="prerequisites"></a>Vereisten

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

* **Een Azure-abonnement**. Zie [gratis proef versie van Azure voor het testen van Hadoop in HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Een moderne webbrowser**. De Azure Portal maakt gebruik van HTML5 en Java script. Het werkt mogelijk niet goed in oudere webbrowsers.

## <a name="create-clusters"></a>Clusters maken

De Azure Portal bevat de meeste cluster eigenschappen. Door Azure Resource Manager sjablonen te gebruiken, kunt u veel Details verbergen. Zie [Apache Hadoop clusters in HDInsight maken met behulp van Resource Manager-sjablonen](hdinsight-hadoop-create-linux-clusters-arm-templates.md)voor meer informatie.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

1. Ga in het menu links naar **+ een resource maken** >  **Analytics** > **HDInsight**.

    ![Een nieuw cluster maken in de Azure Portal](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "Een nieuw cluster maken in de Azure Portal")

1. Selecteer op de pagina **HDInsight-cluster maken** de optie **Ga naar klassieke maken-ervaring**.

    ![Ga naar klassieke ervaring maken](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-create-classic.png)

1. Op de **HDInsight** -pagina selecteert u **aangepast (grootte, instellingen, apps)** .

1. Selecteer **1 basis**. Voer vervolgens de volgende informatie in.

    ![Basis beginselen van HDInsight-cluster maken](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "Een nieuw cluster maken in de Azure Portal")

    * Voer de **naam**van het cluster in. Deze naam moet wereldwijd uniek zijn.

    * Selecteer in de vervolg keuzelijst **abonnement** het Azure-abonnement dat wordt gebruikt voor het cluster.

    * Selecteer een **cluster type**. Selecteer vervolgens het type cluster dat u wilt maken. Voor beelden zijn Hadoop en Apache Spark. Het **besturings systeem** is **Linux**. Selecteer vervolgens een cluster type versie. Gebruik de standaard versie als u niet weet wat u moet kiezen. Zie [HDInsight-clusterversies](hdinsight-component-versioning.md) voor meer informatie.
     
        > [!IMPORTANT]  
        > HDInsight-clusters zijn beschikbaar in verschillende typen. Ze komen overeen met de werk belasting of technologie waarvoor het cluster is afgestemd. Er wordt geen ondersteunde methode gebruikt voor het maken van een cluster waarin meerdere typen worden gecombineerd. Voor beelden zijn Storm en HBase op één cluster.
        
    * Geef voor de gebruikers **naam** en het wacht **woord**voor cluster aanmelding de gebruikers naam en het wacht woord op voor de gebruiker met beheerders rechten.

    * Voer een **SSH-gebruikers naam**in. Als u hetzelfde SSH-wacht woord wilt instellen als het beheerders wachtwoord dat u eerder hebt opgegeven, schakelt u het selectie vakje **hetzelfde wacht woord als cluster aanmelding gebruiken** in. Als dat niet het geval is, geeft u een **wacht woord** of een **open bare sleutel** op om de SSH-gebruiker te verifiëren. Een open bare sleutel is de aanbevolen methode. Kies onder **selecteren** om de configuratie van de referenties op te slaan.
   
        Zie voor meer informatie [verbinding maken met HDInsight (Apache Hadoop) met behulp van SSH](hdinsight-hadoop-linux-use-ssh-unix.md).

    * Geef bij **Resourcegroep** op of u een nieuwe resourcegroep wilt maken of een bestaande wilt gebruiken.

    * Geef een **locatie** op van het Data Center waar het cluster wordt gemaakt.

    * Selecteer **volgende** om naar de volgende pagina te gaan.

1. U kunt met behulp van de beschik bare vervolg keuzelijst van **2 beveiliging en netwerken**uw cluster verbinden met een virtueel netwerk. Selecteer een virtueel Azure-netwerk en het subnet als u het cluster in een virtueel netwerk wilt plaatsen. Zie [een implementatie van een virtueel netwerk plannen voor Azure HDInsight-clusters](hdinsight-plan-virtual-network-deployment.md)voor meer informatie over het gebruik van HDInsight met een virtueel netwerk. Het artikel bevat specifieke configuratie vereisten voor het virtuele netwerk.

    Als u de **Enterprise Security Package**wilt gebruiken, volgt u deze instructies: [een HDInsight-cluster met Enterprise Security Package configureren met behulp van Azure Active Directory Domain Services](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

    Selecteer **volgende** om naar de volgende pagina te gaan.

1. Geef bij **3 opslag**op of u Azure Storage of Azure data Lake Storage als standaard opslag wilt. Zie de volgende tabel voor meer informatie.

     ![HDInsight-cluster opslag maken](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "Een nieuw cluster maken in de Azure Portal")

     | Storage                                      | Beschrijving |
     |----------------------------------------------|-------------|
     | **Blobs Azure Storage als standaard opslag**   | <ul><li>Selecteer **Azure Storage**voor het **primaire opslag type**. Kies bij **selectie methode** **Mijn abonnementen** als u een opslag account wilt opgeven dat deel uitmaakt van uw Azure-abonnement. Selecteer vervolgens het opslag account. Selecteer anders **toegangs sleutel**. Geef vervolgens de informatie op voor het opslag account dat u wilt kiezen buiten uw Azure-abonnement.</li><li>Voor de **standaard container**kiest u de standaard container naam die wordt voorgesteld door de portal of geeft u uw eigen.</li><li>Als Azure Blob-opslag uw standaard opslag is, kunt u ook **extra opslag accounts** selecteren om extra opslag accounts op te geven die u aan het cluster wilt koppelen. Selecteer voor **Azure Storage sleutels** **een opslag sleutel toevoegen**. Vervolgens kunt u een opslag account opgeven vanuit uw Azure-abonnementen of van andere abonnementen. Geef de toegangs sleutel voor het opslag account op.</li><li>Als Blob Storage uw standaard opslag is, kunt u ook **Data Lake Storage toegang** selecteren om Azure data Lake Storage als extra opslag ruimte op te geven. Zie [Quick Start: clusters instellen in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)voor meer informatie.</li></ul> |
     | **Azure Data Lake Storage als standaard opslag** | Selecteer **Azure data Lake Storage gen1** of **Azure data Lake Storage Gen2**voor het **primaire opslag type**. Raadpleeg vervolgens het artikel [Snelstartgids: clusters in HDInsight instellen](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) voor instructies. |
     | **Externe meta Stores**                      | Geef als optie een SQL database op om Apache Hive en Apache Oozie-meta gegevens te bewaren die aan het cluster zijn gekoppeld. Selecteer een SQL database voor **een Hive-SQL database**selecteren. Geef vervolgens de gebruikers naam en het wacht woord op voor de data base. Herhaal deze stappen voor Oozie-meta gegevens.<br><br>Hier volgen enkele aandachtspunten voor het gebruik van Azure SQL database voor meta Stores: <ul><li>De Azure-SQL database die wordt gebruikt voor de meta Store, moet verbinding maken met andere Azure-Services, waaronder Azure HDInsight. Selecteer aan de rechter kant van het dash board van Azure SQL database de naam van de server. Dit is de server waarop het SQL database-exemplaar wordt uitgevoerd. Nadat u in de server weergave bent, selecteert u **configureren**. Selecteer vervolgens bij **Azure**-Services **Ja**. Selecteer vervolgens **Opslaan**.</li><li>Wanneer u een meta Store maakt, moet u een Data Base geen naam met streepjes of afbreek streepjes. Deze tekens kunnen ertoe leiden dat het proces voor het maken van een cluster mislukt.</li></ul> |

     > [!WARNING]  
     > Het gebruik van een extra opslag account in een andere locatie dan het HDInsight-cluster wordt niet ondersteund.

     Selecteer **volgende** om naar de volgende pagina te gaan.

1. Selecteer de gewenste toepassingen van **4 toepassingen (optioneel)** . Micro soft, Independent Software Vendors (Isv's), of u kunt deze toepassingen ontwikkelen. Zie [toepassingen installeren tijdens het maken](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation)van een cluster voor meer informatie.

    Selecteer **volgende** om naar de volgende pagina te gaan.

1. **5 cluster grootte** geeft informatie weer over de knoop punten die worden gebruikt voor dit cluster. Stel het aantal worker-knoop punten in dat u nodig hebt voor het cluster. De geschatte kosten voor het uitvoeren van het cluster worden ook weer gegeven.

    ![HDInsight-cluster knooppunten maken](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-nodes.png "Aantal cluster knooppunten opgeven")

   > [!IMPORTANT]  
   > Als u meer dan 32 worker-knoop punten wilt plannen, selecteert u een hoofd knooppunt grootte met ten minste 8 kernen en 14 GB RAM. De knoop punten plannen bij het maken van een cluster of door het cluster na het maken te schalen.
   >
   > Zie [prijzen voor Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/)voor meer informatie over de grootte van knoop punten en de bijbehorende kosten.

    Selecteer **volgende** om naar de volgende pagina te gaan.

1. Vanuit **6 script acties**kunt u een cluster aanpassen om aangepaste onderdelen te installeren. Deze optie werkt als u een aangepast script wilt gebruiken voor het aanpassen van een cluster, wanneer het cluster wordt gemaakt. Zie [HDInsight-clusters op basis van Linux aanpassen met behulp van script acties](hdinsight-hadoop-customize-cluster-linux.md)voor meer informatie over script acties.

   Selecteer **volgende** om naar de volgende pagina te gaan.

1. Controleer in **7 samen vatting**de informatie die u eerder hebt ingevoerd. Selecteer vervolgens **Maken**.

     ![HDInsight-cluster overzicht maken](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-summary.png "Aantal cluster knooppunten opgeven")
    
    > [!NOTE]  
    > Meestal duurt het genereren van het cluster ongeveer 20 minuten. Bewaak **meldingen** om het inrichtings proces te controleren.

1. Nadat het proces is gemaakt, selecteert **u naar resource gaan** vanuit de melding **implementatie geslaagd** . Het cluster venster bevat de volgende informatie:

    ![Overzicht van HDI-Azure Portal cluster](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Cluster eigenschappen")

    De pictogrammen in het venster worden als volgt beschreven:

    * Het tabblad **overzicht** bevat alle essentiële informatie over het cluster. Voor beelden zijn de naam, de resource groep waartoe deze behoort, de locatie, het besturings systeem en de URL voor het cluster dashboard.
    * Het **dash board** stuurt u naar de Ambari-portal die aan het cluster is gekoppeld.
    * **Beveiligde shell** biedt informatie die nodig is voor toegang tot het cluster met behulp van SSH.
    * Met behulp van **Scale cluster**kunt u het aantal worker-knoop punten verhogen dat aan het cluster is gekoppeld.
    * Met **verwijderen** wordt het HDInsight-cluster verwijderd.

## <a name="customize-clusters"></a>Clusters aanpassen
* [HDInsight-clusters aanpassen met Boots trap](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Op Linux gebaseerde HDInsight-clusters aanpassen met behulp van script acties](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Het cluster verwijderen

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="troubleshoot"></a>Problemen oplossen

Zie [Vereisten voor toegangsbeheer](hdinsight-hadoop-create-linux-clusters-portal.md) als u problemen ondervindt met het maken van HDInsight-clusters.

## <a name="next-steps"></a>Volgende stappen

U hebt een HDInsight-cluster gemaakt. Meer informatie over het werken met uw cluster.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop clusters

* [Apache Hive gebruiken met HDInsight](hadoop/hdinsight-use-hive.md)
* [Apache Pig gebruiken met HDInsight](hadoop/hdinsight-use-pig.md)
* [MapReduce gebruiken met HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase-clusters

* [Aan de slag met Apache HBase in HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Ontwikkel Java-toepassingen voor Apache HBase in HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm clusters

* [Java-topologieën ontwikkelen voor Apache Storm op HDInsight](storm/apache-storm-develop-java-topology.md)
* [Python-onderdelen gebruiken in Apache Storm in HDInsight](storm/apache-storm-develop-python-topology.md)
* [Topologieën implementeren en bewaken met Apache Storm op HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark clusters

* [Een zelfstandige toepassing maken met behulp van scala](spark/apache-spark-create-standalone-application.md)
* [Taken op afstand uitvoeren op een Apache Spark cluster met behulp van Apache livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark met BI: interactieve gegevens analyses uitvoeren met behulp van Spark in HDInsight met BI-hulpprogram ma's](spark/apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Spark in HDInsight gebruiken om voedsel inspectie resultaten te voors pellen](spark/apache-spark-machine-learning-mllib-ipython.md)
