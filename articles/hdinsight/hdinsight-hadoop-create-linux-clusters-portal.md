---
title: Apache Hadoop clusters maken met behulp van webbrowser, Azure HDInsight
description: Meer informatie over het maken van Apache Hadoop-, Apache HBase-, Apache Storm-of Apache Spark-clusters op Linux voor HDInsight met behulp van een webbrowser en de Azure Portal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 11/13/2019
ms.openlocfilehash: 820ddb8d06cfd2aac2b053305f23ad330e4fd7c3
ms.sourcegitcommit: d6b68b907e5158b451239e4c09bb55eccb5fef89
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2019
ms.locfileid: "74215919"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Op Linux gebaseerde clusters maken in HDInsight met behulp van de Azure Portal

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

De Azure Portal is een webgebaseerd hulp programma voor het beheren van services en resources die worden gehost in de Microsoft Azure Cloud. In dit artikel leert u hoe u Azure HDInsight-clusters op basis van Linux maakt met behulp van de portal.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="prerequisites"></a>Vereisten

* **Een Azure-abonnement**. Zie [gratis proef versie van Azure voor het testen van Hadoop in HDInsight](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* **Een moderne webbrowser**. De Azure Portal maakt gebruik van HTML5 en Java script. Het werkt mogelijk niet goed in oudere webbrowsers.

## <a name="create-clusters"></a>Clusters maken

De Azure Portal bevat de meeste cluster eigenschappen. Door Azure Resource Manager sjablonen te gebruiken, kunt u veel Details verbergen. Zie [Apache Hadoop clusters in HDInsight maken met behulp van Resource Manager-sjablonen](hdinsight-hadoop-create-linux-clusters-arm-templates.md)voor meer informatie.

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Meld u aan bij de [Azure Portal](https://portal.azure.com).

1. Ga in het menu links naar **+ een resource maken** >  **Analytics** > **Azure HDInsight**.

    ![Een nieuw cluster maken in de Azure Portal](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster.png "Een nieuw cluster maken in de Azure Portal")

1. Selecteer op de pagina **HDInsight-cluster maken** de optie **Ga naar klassieke maken-ervaring**.

    ![Ga naar klassieke ervaring maken](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-create-classic.png)

1. Op de **HDInsight** -pagina selecteert u **aangepast (grootte, instellingen, apps)** .

1. Selecteer **1 basis**. Voer vervolgens de volgende gegevens in:

    |Eigenschap |Beschrijving |
    |---|---|
    |Clusternaam|Deze naam moet wereldwijd uniek zijn.|
    |Subscription|Selecteer in de vervolg keuzelijst het Azure-abonnement dat wordt gebruikt voor het cluster.|
    |Clustertype|Selecteer het type cluster dat u wilt maken. Voor beelden zijn Hadoop en Apache Spark. Het **besturings systeem** is **Linux**. Selecteer vervolgens een cluster type versie. Gebruik de standaard versie als u niet weet wat u moet kiezen. Zie [HDInsight-clusterversies](hdinsight-component-versioning.md) voor meer informatie.|
    |Gebruikersnaam voor clusteraanmeldgegevens|Geef de gebruikers naam op. de standaard instelling is **admin**.|
    |Wachtwoord voor clusteraanmeldgegevens|Geef het wacht woord op.|
    |SSH-gebruikersnaam (Secure Shell)|De standaard waarde is **sshuser**. Als u hetzelfde SSH-wacht woord wilt gebruiken als het beheerders wachtwoord dat u eerder hebt opgegeven, schakelt u het selectie vakje **wacht woord voor cluster aanmelden voor SSH** in. Als dat niet het geval is, geeft u een **wacht woord** of een **open bare sleutel** op om de SSH-gebruiker te verifiëren. Een open bare sleutel is de aanbevolen methode. Kies onder **selecteren** om de configuratie van de referenties op te slaan.  Zie voor meer informatie [verbinding maken met HDInsight (Apache Hadoop) met behulp van SSH](hdinsight-hadoop-linux-use-ssh-unix.md).|
    |Resourcegroep|Geef aan of u een nieuwe resourcegroep wilt maken of een bestaande groep wilt gebruiken.|
    |Locatie|Geef een Data Center op waar het cluster wordt gemaakt.|

    ![Basis beginselen van HDInsight-cluster maken](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-basics.png "Een nieuw cluster maken in de Azure Portal")

    > [!IMPORTANT]  
    > HDInsight-clusters zijn beschikbaar in verschillende typen. Ze komen overeen met de werk belasting of technologie waarvoor het cluster is afgestemd. Er wordt geen ondersteunde methode gebruikt voor het maken van een cluster waarin meerdere typen worden gecombineerd. Voor beelden zijn Storm en HBase op één cluster.

    Selecteer **volgende** om naar de volgende pagina te gaan.

1. U kunt met behulp van de beschik bare vervolg keuzelijst van **2 beveiliging en netwerken**uw cluster verbinden met een virtueel netwerk. Selecteer een virtueel Azure-netwerk en het subnet als u het cluster in een virtueel netwerk wilt plaatsen. Zie [een implementatie van een virtueel netwerk plannen voor Azure HDInsight-clusters](hdinsight-plan-virtual-network-deployment.md)voor meer informatie over het gebruik van HDInsight met een virtueel netwerk. Het artikel bevat specifieke configuratie vereisten voor het virtuele netwerk.

    Als u de **Enterprise Security Package**wilt gebruiken, volgt u deze instructies: [een HDInsight-cluster met Enterprise Security Package configureren met behulp van Azure Active Directory Domain Services](https://docs.microsoft.com/azure/hdinsight/domain-joined/apache-domain-joined-configure-using-azure-adds).

    Selecteer **volgende** om naar de volgende pagina te gaan.

1. Geef in **3 opslag**, voor de instellingen van het **opslag account**, op of u Azure Storage of Azure data Lake Storage als standaard opslag wilt. Zie de volgende tabel voor meer informatie.

    | Primair opslag type | Beschrijving |
    |------------------|-------------|
    | Azure Storage   |  * Kies bij **selectie methode** **Mijn abonnementen** als u een opslag account wilt opgeven dat deel uitmaakt van uw Azure-abonnement. Selecteer vervolgens het opslag account. Selecteer anders **toegangs sleutel**. Geef vervolgens de informatie op voor het opslag account dat u wilt kiezen buiten uw Azure-abonnement.</br></br> * Kies voor **standaard container**de standaard container naam die wordt voorgesteld door de portal of geef uw eigen op.</br></br> * Als Azure Blob-opslag uw standaard opslag is, kunt u ook **extra opslag accounts** selecteren om extra opslag accounts op te geven die u aan het cluster wilt koppelen. Selecteer voor **Azure Storage sleutels** **een opslag sleutel toevoegen**. Vervolgens kunt u een opslag account opgeven vanuit uw Azure-abonnementen of van andere abonnementen. Geef de toegangs sleutel voor het opslag account op.</br></br> * Als Blob Storage is ingesteld als standaard opslag, kunt u ook **Data Lake Storage Access** selecteren om Azure data Lake Storage als extra opslag ruimte op te geven. Zie [Quick Start: clusters instellen in HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md)voor meer informatie.</li></ul> |
    | Azure Data Lake Storage | Selecteer **Azure data Lake Storage gen1** of **Azure data Lake Storage Gen2**. Raadpleeg vervolgens het artikel [Snelstartgids: clusters in HDInsight instellen](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md) voor instructies. |

    **Meta Store-instellingen (optioneel)**

    Geef als optie een SQL database op om Apache Hive en Apache Oozie-meta gegevens te bewaren die aan het cluster zijn gekoppeld. Selecteer een SQL database voor **een Hive-SQL database**selecteren. Geef vervolgens de gebruikers naam en het wacht woord op voor de data base. Herhaal deze stappen voor Oozie-meta gegevens.

    Hier volgen enkele aandachtspunten voor het gebruik van Azure SQL database voor meta Stores:
    * De Azure-SQL database die wordt gebruikt voor de meta Store, moet verbinding maken met andere Azure-Services, waaronder Azure HDInsight. Selecteer aan de rechter kant van het dash board van Azure SQL database de naam van de server. Dit is de server waarop het SQL database-exemplaar wordt uitgevoerd. Nadat u in de server weergave bent, selecteert u **configureren**. Selecteer vervolgens bij **Azure**-Services **Ja**. Selecteer vervolgens **Opslaan**.
    * Wanneer u een meta Store maakt, moet u een Data Base geen naam met streepjes of afbreek streepjes. Deze tekens kunnen ertoe leiden dat het proces voor het maken van een cluster mislukt.

    ![HDInsight-cluster opslag maken](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-storage.png "Een nieuw cluster maken in de Azure Portal")

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

    Enkele van de pictogrammen in het venster worden als volgt uitgelegd:

    |Eigenschap | Beschrijving |
    |---|---|
    |Overzicht|Bevat alle essentiële informatie over het cluster. Voor beelden zijn de naam, de resource groep waartoe deze behoort, de locatie, het besturings systeem en de URL voor het cluster dashboard.|
    |Clusterdashboards|Stuurt u naar de Ambari-portal die aan het cluster is gekoppeld.|
    |Aanmelden bij SSH en cluster|Bevat informatie die nodig is voor toegang tot het cluster met behulp van SSH.|
    |Verwijderen|Hiermee verwijdert u het HDInsight-cluster.|

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
