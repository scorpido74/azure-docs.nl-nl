---
title: Apache Hadoop-clusters maken met behulp van webbrowser Azure HDInsight
description: Meer informatie over het maken van Apache Hadoop-, Apache HBase-, Apache Storm- of Apache Spark-clusters op Linux voor HDInsight met behulp van een webbrowser en de Azure-portal.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 02/19/2020
ms.openlocfilehash: bcc71b7244ba3498b2fcde023d372e67f41d6117
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "77623187"
---
# <a name="create-linux-based-clusters-in-hdinsight-by-using-the-azure-portal"></a>Linux-gebaseerde clusters maken in HDInsight met behulp van de Azure-portal

[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

De Azure-portal is een webgebaseerde beheertool voor services en resources die worden gehost in de Microsoft Azure-cloud. In dit artikel leert u hoe u Azure HDInsight-clusters op basis van Linux maken met behulp van de portal. Aanvullende details zijn beschikbaar bij [Clusters van Create HDInsight.](./hdinsight-hadoop-provision-linux-clusters.md)

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

De Azure-portal legt de meeste clustereigenschappen bloot. Door Azure Resource Manager-sjablonen te gebruiken, u veel details verbergen. Zie [Clusters van Apache Hadoop maken in HDInsight voor](hdinsight-hadoop-create-linux-clusters-arm-templates.md)meer informatie met behulp van Resource Manager-sjablonen .

Als u geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) voordat u begint.

## <a name="create-clusters"></a>Clusters maken

[!INCLUDE [secure-transfer-enabled-storage-account](../../includes/hdinsight-secure-transfer.md)]

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

1. Selecteer in het bovenste menu **+ Een resource maken.**

    ![Een nieuw cluster maken in de Azure-portal](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-create-resource.png "Een nieuw cluster maken in de Azure-portal")

1. Selecteer **Analytics** > **Azure HDInsight** om naar de **clusterpagina HDInsight maken** te gaan.

## <a name="basics"></a>Basisbeginselen

![HDInsight maakt clusterbasisprincipes](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-basics.png "Een nieuw cluster maken in de Azure-portal")

Geef op het tabblad **Basisbeginselen** de volgende informatie op:

|Eigenschap |Beschrijving |
|---|---|
|Abonnement|Selecteer in de vervolgkeuzelijst het Azure-abonnement dat voor het cluster wordt gebruikt.|
|Resourcegroep|Selecteer in de vervolgkeuzelijst uw bestaande resourcegroep of selecteer **Nieuw maken**.|
|Clusternaam|Voer een wereldwijd unieke naam in.|
|Regio|Selecteer in de vervolgkeuzelijst een gebied waar het cluster wordt gemaakt.|
|Clustertype|Selecteer **Clustertype selecteren** om een lijst te openen. Selecteer in de lijst het gewenste clustertype. HDInsight clusters zijn er in verschillende soorten. Ze komen overeen met de werkbelasting of technologie waarop het cluster is afgestemd. Er is geen ondersteunde methode om een cluster te maken dat meerdere typen combineert.|
|Versie|Selecteer in de vervolgkeuzelijst een **versie**. Gebruik de standaardversie als u niet weet wat u moet kiezen. Zie [HDInsight-clusterversies](hdinsight-component-versioning.md) voor meer informatie.|
|Gebruikersnaam voor clusteraanmeldgegevens|Geef de gebruikersnaam op, standaard is **beheerder**.|
|Wachtwoord voor clusteraanmeldgegevens|Geef het wachtwoord op.|
|Wachtwoord voor het inloggen van het cluster bevestigen|Voer het wachtwoord opnieuw in|
|SSH-gebruikersnaam (Secure Shell)|Geef de gebruikersnaam op, standaard is **sshuser**|
|Wachtwoord voor clusteraanmelding voor SSH gebruiken|Als u hetzelfde SSH-wachtwoord wilt als het beheerderswachtwoord dat u eerder hebt opgegeven, schakelt u het **selectievakje Wachtwoord voor clusterinlogen voor SSH** gebruiken in. Zo niet, geef dan een **wachtwoord** of **een openbare sleutel** om de SSH-gebruiker te verifiëren. Een publieke sleutel is de aanpak die wij aanbevelen. Kies **Onderaan selecteren** om de configuratie van de referenties op te slaan.  Zie [Verbinding maken met HDInsight (Apache Hadoop) voor](hdinsight-hadoop-linux-use-ssh-unix.md)meer informatie met SSH .|

Selecteer **Volgende: Opslag >>** om door te gaan naar het volgende tabblad.

## <a name="storage"></a>Storage

![HDInsight maakt clusteropslag](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-storage.png "Een nieuw cluster maken in de Azure-portal - opslag")

### <a name="primary-storage"></a>Primaire opslag

Selecteer in de vervolgkeuzelijst voor **primaire opslagtype**het standaardopslagtype. De volgende velden die moeten worden voltooid, variëren afhankelijk van uw selectie. Voor **Azure Storage:**

1. Kies **voor selectiemethode** **Selecteren in lijst**of **Toegangssleutel gebruiken**.
    * Selecteer uw **primaire opslagaccount** in de vervolgkeuzelijst selecteren **voor Selecteren uit lijst**of selecteer Nieuw **maken**.
    * Voer de naam van uw **opslagaccount**in voor **het gebruik van toegangssleutel**. Geef vervolgens de **toegangssleutel op.**

1. Accepteer **voor Container**de standaardwaarde of voer een nieuwe in.

### <a name="additional-azure-storage"></a>Extra Azure-opslag

Optioneel: selecteer **Azure Storage toevoegen** voor extra clusteropslag. Het gebruik van een extra opslagaccount in een andere regio dan het HDInsight-cluster wordt niet ondersteund.

### <a name="metastore-settings"></a>Metastore-instellingen

Optioneel: Geef een bestaande SQL-database op om Apache Hive, Apache Oozie en/of Apache Ambari-metagegevens buiten het cluster op te slaan. De Azure SQL-database die wordt gebruikt voor de metastore moet connectiviteit met andere Azure-services mogelijk maken, waaronder Azure HDInsight. Wanneer u een metastore maakt, geeft u geen naam aan een database met streepjes of koppeltekens. Deze tekens kunnen ervoor zorgen dat het proces voor het maken van het clusterproces mislukt.

Selecteer **Volgende: Beveiliging + netwerk >>** om door te gaan naar het volgende tabblad.

## <a name="security--networking"></a>Beveiliging + netwerken

![HDInsight maakt clusterbeveiligingsnetwerken](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-security-networking.png "HDInsight maakt clusterbeveiligingsnetwerken")

Geef op het tabblad **Beveiliging + netwerken** de volgende informatie op:

|Eigenschap |Beschrijving |
|---|---|
|Bedrijfsbeveiligingspakket|Optioneel: schakel het selectievakje in om **enterprise security package**te gebruiken. Zie [Een HDInsight-cluster configureren met Enterprise Security Package met Azure Active Directory Domain Services](./domain-joined/apache-domain-joined-configure-using-azure-adds.md)voor meer informatie.|
|TLS|Optioneel: selecteer een TLS-versie in de vervolgkeuzelijst. Zie [Transport Layer Security](./hdinsight-plan-virtual-network-deployment.md#transport-layer-security)voor meer informatie.|
|Virtueel netwerk|Optioneel: selecteer een bestaand virtueel netwerk en subnet in de vervolgkeuzelijst. Zie Een [virtuele netwerkimplementatie voor Azure HDInsight-clusters plannen voor](hdinsight-plan-virtual-network-deployment.md)meer informatie. Het artikel bevat specifieke configuratievereisten voor het virtuele netwerk.|
|Instellingen voor schijfversleuteling|Optioneel: schakel het selectievakje in om versleuteling te gebruiken. Zie [Door de klant beheerde sleutelschijfversleuteling](./disk-encryption.md)voor meer informatie.|
|Kafka REST-proxy|Deze instelling is alleen beschikbaar voor clustertype Kafka. Zie [Een REST-proxy gebruiken](./kafka/rest-proxy.md)voor meer informatie.|
|Identiteit|Optioneel: selecteer een bestaande door de gebruiker toegewezen serviceidentiteit in de vervolgkeuzelijst. Zie [Beheerde identiteiten in Azure HDInsight](./hdinsight-managed-identities.md)voor meer informatie.|

Selecteer **Volgende: Configuratie + prijzen >>** om door te gaan naar het volgende tabblad.

## <a name="configuration--pricing"></a>Configuratie + prijzen

![HDInsight maakt clusterconfiguratie](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-configuration.png "Tabblad Configuratie en prijzen")

Geef op het tabblad **Configuratie + prijzen** de volgende informatie op:

|Eigenschap |Beschrijving |
|---|---|
|+ Toepassing toevoegen|Optioneel: selecteer de gewenste toepassingen. Microsoft, onafhankelijke softwareleveranciers (ISV's), of u deze toepassingen ontwikkelen. Zie [Toepassingen installeren tijdens het maken van het cluster voor](hdinsight-apps-install-applications.md#install-applications-during-cluster-creation)meer informatie .|
|Grootte van knooppunt|Optioneel: selecteer een knooppunt van verschillende grootte.|
|Aantal knooppunten|Optioneel: voer het aantal knooppunten voor het opgegeven knooppunttype in. Als u van plan bent op meer dan 32 werkknooppunten, selecteert u een hoofdknooppuntgrootte met ten minste acht cores en 14 GB RAM. Plan de knooppunten bij het maken van het cluster of door het cluster na het maken te schalen.|
|Automatische schaal inschakelen|Optioneel: schakel het selectievakje in om de functie in te schakelen. Zie [Azure HDInsight-clusters automatisch schalen](./hdinsight-autoscale-clusters.md)voor meer informatie.|
|+ Scriptactie toevoegen|Optioneel: deze optie werkt als u een aangepast script wilt gebruiken om een cluster aan te passen, terwijl het cluster wordt gemaakt. Zie [Op Linux gebaseerde HDInsight-clusters aanpassen met scriptacties](hdinsight-hadoop-customize-cluster-linux.md)voor meer informatie over scriptacties.|

Selecteer **Controleren + >>maken** om de clusterconfiguratie te valideren en door te gaan naar het laatste tabblad.

## <a name="review--create"></a>Controleren + maken

![HDInsight maakt clusteroverzicht](./media/hdinsight-hadoop-create-linux-clusters-portal/azure-portal-cluster-review-create-hadoop.png "Aantal clusterknooppunten opgeven")

Bekijk de instellingen. Selecteer **Maken** om het cluster te maken.

Meestal duurt het genereren van het cluster ongeveer 20 minuten. **Controleer meldingen** om het inrichtingsproces te controleren.

## <a name="post-creation"></a>Na creatie

Nadat het creatieproces is voltooid, selecteert u **Ga naar Resource** in de melding Implementatie **geslaagd.** Het clustervenster bevat de volgende informatie.

![Overzicht van HDI Azure-portalcluster](./media/hdinsight-hadoop-create-linux-clusters-portal/hdinsight-create-cluster-completed.png "Clustereigenschappen")

Sommige pictogrammen in het venster worden als volgt uitgelegd:

|Eigenschap | Beschrijving |
|---|---|
|Overzicht|Biedt alle essentiële informatie over het cluster. Voorbeelden hiervan zijn de naam, de brongroep waartoe het behoort, de locatie, het besturingssysteem en de URL voor het clusterdashboard.|
|Clusterdashboards|Leidt u naar de Ambari-portal die is gekoppeld aan het cluster.|
|SSH + Cluster login|Biedt informatie die nodig is om toegang te krijgen tot het cluster met behulp van SSH.|
|Verwijderen|Hiermee verwijdert u het HDInsight-cluster.|

## <a name="customize-clusters"></a>Clusters aanpassen

* [HDInsight-clusters aanpassen met Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md)
* [Linux-gebaseerde HDInsight-clusters aanpassen met behulp van scriptacties](hdinsight-hadoop-customize-cluster-linux.md)

## <a name="delete-the-cluster"></a>Het cluster verwijderen

Zie [Een HDInsight-cluster verwijderen met uw browser, PowerShell of Azure CLI](./hdinsight-delete-cluster.md).

## <a name="troubleshoot"></a>Problemen oplossen

Zie [Vereisten voor toegangsbeheer](./hdinsight-hadoop-customize-cluster-linux.md#access-control) als u problemen ondervindt met het maken van HDInsight-clusters.

## <a name="next-steps"></a>Volgende stappen

U hebt een HDInsight-cluster gemaakt. Lees nu hoe u met uw cluster werken.

### <a name="apache-hadoop-clusters"></a>Apache Hadoop clusters

* [Apache Hive gebruiken met HDInsight](hadoop/hdinsight-use-hive.md)
* [MapReduce gebruiken met HDInsight](hadoop/hdinsight-use-mapreduce.md)

### <a name="apache-hbase-clusters"></a>Apache HBase clusters

* [Aan de slag met Apache HBase op HDInsight](hbase/apache-hbase-tutorial-get-started-linux.md)
* [Java-toepassingen ontwikkelen voor Apache HBase op HDInsight](hbase/apache-hbase-build-java-maven-linux.md)

### <a name="apache-storm-clusters"></a>Apache Storm clusters

* [Ontwikkel Java-topologieën voor Apache Storm op HDInsight](storm/apache-storm-develop-java-topology.md)
* [Python-componenten gebruiken in Apache Storm op HDInsight](storm/apache-storm-develop-python-topology.md)
* [Topologieën implementeren en monitoren met Apache Storm op HDInsight](storm/apache-storm-deploy-monitor-topology-linux.md)

### <a name="apache-spark-clusters"></a>Apache Spark clusters

* [Een zelfstandige toepassing maken met Scala](spark/apache-spark-create-standalone-application.md)
* [Taken op afstand uitvoeren op een Apache Spark-cluster met Apache Livy](spark/apache-spark-livy-rest-interface.md)
* [Apache Spark met BI: interactieve gegevensanalyse uitvoeren met Spark in HDInsight met BI-tools](spark/apache-spark-use-bi-tools.md)
* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight om de resultaten van voedselinspectie te voorspellen](spark/apache-spark-machine-learning-mllib-ipython.md)
