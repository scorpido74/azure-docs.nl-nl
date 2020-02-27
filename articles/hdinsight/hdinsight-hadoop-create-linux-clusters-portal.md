---
title: Apache Hadoop clusters maken met behulp van webbrowser, Azure HDInsight
description: Meer informatie over het maken van Apache Hadoop-, Apache HBase-, Apache Storm-of Apache Spark-clusters op Linux voor HDInsight met behulp van een webbrowser en de Azure Portal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/19/2020
ms.openlocfilehash: bcc71b7244ba3498b2fcde023d372e67f41d6117
ms.sourcegitcommit: 5a71ec1a28da2d6ede03b3128126e0531ce4387d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/26/2020
ms.locfileid: "77623187"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Op Linux gebaseerde clusters maken in HDInsight met behulp van de Azure Portal

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

De Azure Portal is een webgebaseerd hulp programma voor het beheren van services en resources die worden gehost in de Microsoft Azure Cloud. In dit artikel leert u hoe u Azure HDInsight-clusters op basis van Linux maakt met behulp van de portal. Meer informatie over het [maken van HDInsight-clusters](./hdinsight-hadoop-provision-linux-clusters.md)is beschikbaar.

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

De Azure Portal bevat de meeste cluster eigenschappen. Door Azure Resource Manager sjablonen te gebruiken, kunt u veel Details verbergen. Zie [Apache Hadoop clusters in HDInsight maken met behulp van Resource Manager-sjablonen](hdinsight-hadoop-create-linux-clusters-arm-templates.md)voor meer informatie.

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="create-clusters"></a>Clusters maken

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Meld u aan bij de [Azure-portal](https://portal.azure.com).

1. Selecteer in het bovenste menu de optie **+ een resource maken**.

    ![Een nieuw cluster maken in de Azure Portal](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png "Een nieuw cluster maken in de Azure Portal")

1. Selecteer **Analytics** > **Azure HDInsight** om naar de pagina **HDInsight-cluster maken** te gaan.

## <a name="basics"></a>Basisbeginselen

![Basis beginselen van HDInsight-cluster maken](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png "Een nieuw cluster maken in de Azure Portal")

Geef op het tabblad **basis beginselen** de volgende informatie op:

|Eigenschap |Beschrijving |
|---|---|
|Abonnement|Selecteer in de vervolg keuzelijst het Azure-abonnement dat wordt gebruikt voor het cluster.|
|Resourcegroep|Selecteer in de vervolg keuzelijst de bestaande resource groep of selecteer **nieuwe maken**.|
|Clusternaam|Voer een wereld wijd unieke naam in.|
|Regio|Selecteer in de vervolg keuzelijst een regio waar het cluster wordt gemaakt.|
|Cluster type|Selecteer **cluster type selecteren** om een lijst te openen. Selecteer in de lijst het gewenste cluster type. HDInsight-clusters zijn beschikbaar in verschillende typen. Ze komen overeen met de werk belasting of technologie waarvoor het cluster is afgestemd. Er wordt geen ondersteunde methode gebruikt voor het maken van een cluster waarin meerdere typen worden gecombineerd.|
|Version|Selecteer een **versie**in de vervolg keuzelijst. Gebruik de standaard versie als u niet weet wat u moet kiezen. Zie [HDInsight-clusterversies](hdinsight-component-versioning.md) voor meer informatie.|
|Gebruikersnaam voor clusteraanmeldgegevens|Geef de gebruikers naam op. de standaard instelling is **admin**.|
|Wachtwoord voor clusteraanmeldgegevens|Geef het wacht woord op.|
|Wacht woord voor aanmelding bij cluster bevestigen|Voer het wacht woord opnieuw in|
|SSH-gebruikersnaam (Secure Shell)|Geef de gebruikers naam op. de standaard waarde is **sshuser**|
|Wacht woord voor cluster aanmelding gebruiken voor SSH|Als u hetzelfde SSH-wacht woord wilt gebruiken als het beheerders wachtwoord dat u eerder hebt opgegeven, schakelt u het selectie vakje **wacht woord voor cluster aanmelden voor SSH** in. Als dat niet het geval is, geeft u een **wacht woord** of een **open bare sleutel** op om de SSH-gebruiker te verifiëren. Een open bare sleutel is de aanbevolen methode. Kies onder **selecteren** om de configuratie van de referenties op te slaan.  Zie voor meer informatie [verbinding maken met HDInsight (Apache Hadoop) met behulp van SSH](hdinsight-hadoop-linux-use-ssh-unix.md).|

Selecteer **volgende: opslag > >** om naar het volgende tabblad te gaan.

## <a name="storage"></a>Opslag

![HDInsight-cluster opslag maken](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png "Een nieuw cluster maken in de Azure Portal-opslag")

### <a name="primary-storage"></a>Primaire opslag

Selecteer uw standaard type opslag in de vervolg keuzelijst voor het **primaire opslag type**. De volgende velden die moeten worden voltooid, variëren op basis van uw selectie. Voor **Azure Storage**:

1. Kies bij **selectie methode**de optie **selecteren in de lijst**of **Gebruik de toegangs sleutel**.
    * Selecteer **in de lijst selecteren**een **primair opslag account** in de vervolg keuzelijst of selecteer **nieuwe maken**.
    * Voer de naam van uw **opslag account**in als **u de toegangs sleutel wilt gebruiken**. Geef vervolgens de **toegangs sleutel**op.

1. Accepteer de standaard waarde voor **container**, of geef een nieuwe op.

### <a name="additional-azure-storage"></a>Aanvullende Azure Storage

Optioneel: Selecteer **Azure Storage toevoegen** voor extra cluster opslag. Het gebruik van een extra opslag account in een andere regio dan het HDInsight-cluster wordt niet ondersteund.

### <a name="metastore-settings"></a>Meta Store-instellingen

Optioneel: Geef een bestaand SQL Database op om Apache Hive-, Apache Oozie-en/of Apache Ambari-meta gegevens buiten het cluster op te slaan. De Azure SQL Database die wordt gebruikt voor de meta Store, moet verbinding maken met andere Azure-Services, waaronder Azure HDInsight. Wanneer u een meta Store maakt, moet u een Data Base geen naam met streepjes of afbreek streepjes. Deze tekens kunnen ertoe leiden dat het proces voor het maken van een cluster mislukt.

Selecteer **volgende: beveiliging en netwerk > >** om naar het volgende tabblad te gaan.

## <a name="security--networking"></a>Beveiliging en netwerken

![HDInsight cluster beveiligings netwerken maken](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png "HDInsight cluster beveiligings netwerken maken")

Op het tabblad **beveiliging en netwerk** geeft u de volgende informatie op:

|Eigenschap |Beschrijving |
|---|---|
|Enter prise-beveiligings pakket|Optioneel: Schakel het selectie vakje in om **Enterprise Security Package**te gebruiken. Zie [Configure a HDInsight Cluster with Enterprise Security Package met behulp van Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)voor meer informatie.|
|TLS|Optioneel: Selecteer een TLS-versie in de vervolg keuzelijst. Zie [Transport Layer Security](./hdinsight-plan-virtual-network-deployment.md#transport-layer-security)voor meer informatie.|
|Virtueel netwerk|Optioneel: Selecteer een bestaand virtueel netwerk en subnet in de vervolg keuzelijst. Zie [een implementatie van een virtueel netwerk plannen voor Azure HDInsight-clusters](hdinsight-plan-virtual-network-deployment.md)voor meer informatie. Het artikel bevat specifieke configuratie vereisten voor het virtuele netwerk.|
|Instellingen voor schijf versleuteling|Optioneel: Schakel het selectie vakje in om versleuteling te gebruiken. Zie door de [klant beheerde sleutel schijf versleuteling](./disk-encryption.md)voor meer informatie.|
|Kafka REST-proxy|Deze instelling is alleen beschikbaar voor cluster type Kafka. Zie [using a rest proxy](./kafka/rest-proxy.md)(Engelstalig) voor meer informatie.|
|Identiteit|Optioneel: Selecteer een bestaande door de gebruiker toegewezen Service-id in de vervolg keuzelijst. Zie [beheerde identiteiten in azure HDInsight](./hdinsight-managed-identities.md)voor meer informatie.|

Selecteer **volgende: Configuratie + prijzen > >** om naar het volgende tabblad te gaan.

## <a name="configuration--pricing"></a>Configuratie en prijzen

![HDInsight-cluster configuratie maken](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png "Tabblad Configuratie en prijzen")

Geef op het tabblad **configuratie en prijzen** de volgende informatie op:

|Eigenschap |Beschrijving |
|---|---|
|+ Toepassing toevoegen|Optioneel: Selecteer de gewenste toepassingen. Micro soft, Independent Software Vendors (Isv's), of u kunt deze toepassingen ontwikkelen. Zie [toepassingen installeren tijdens het maken](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation)van een cluster voor meer informatie.|
|Knooppunt grootte|Optioneel: Selecteer een knoop punt met verschillende afmetingen.|
|Aantal knoop punten|Optioneel: Voer het aantal knoop punten in voor het opgegeven knooppunt type. Als u meer dan 32 worker-knoop punten wilt plannen, selecteert u een hoofd knooppunt grootte met ten minste acht kernen en een RAM van 14 GB. De knoop punten plannen bij het maken van een cluster of door het cluster na het maken te schalen.|
|Automatisch schalen inschakelen|Optioneel: Schakel het selectie vakje in om de functie in te scha kelen. Zie [automatisch schalen van Azure HDInsight-clusters](./hdinsight-autoscale-clusters.md)voor meer informatie.|
|+ Script actie toevoegen|Optioneel: deze optie werkt als u een aangepast script wilt gebruiken voor het aanpassen van een cluster, wanneer het cluster wordt gemaakt. Zie [HDInsight-clusters op basis van Linux aanpassen met behulp van script acties](hdinsight-hadoop-customize-cluster-linux.md)voor meer informatie over script acties.|

Selecteer **controleren + maken > >** om de cluster configuratie te valideren en door te gaan naar het tabblad definitief.

## <a name="review--create"></a>Controleren en maken

![HDInsight-cluster overzicht maken](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png "Aantal cluster knooppunten opgeven")

Controleer de instellingen. Selecteer **Maken** om het cluster te maken.

Meestal duurt het genereren van het cluster ongeveer 20 minuten. Bewaak **meldingen** om het inrichtings proces te controleren.

## <a name="post-creation"></a>Na maken

Nadat het proces is gemaakt, selecteert **u naar resource gaan** vanuit de melding **implementatie geslaagd** . Het cluster venster bevat de volgende informatie:

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

Zie [een HDInsight-cluster verwijderen met behulp van uw browser, Power shell of de Azure cli](./hdinsight-delete-cluster.md).

## <a name="troubleshoot"></a>Problemen oplossen

Zie [Vereisten voor toegangsbeheer](./hdinsight-hadoop-customize-cluster-linux.md#access-control) als u problemen ondervindt met het maken van HDInsight-clusters.

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
