---
title: Toepassingen van derden installeren op Azure HDInsight
description: Meer informatie over het installeren van Apache Hadoop-toepassingen van derden op Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: 22d27d50a7d2c304e5d1a04a9a5eaa03d2f0bfa6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "79366339"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Apache Hadoop-toepassingen van derden installeren op Azure HDInsight

Meer informatie over het installeren van een [Apache Hadoop-toepassing](https://hadoop.apache.org/) van derden op Azure HDInsight. Zie voor instructies voor de installatie van uw eigen toepassing [Aangepaste HDInsight-toepassingen installeren](hdinsight-apps-install-custom-applications.md).

Een HDInsight-toepassing is een toepassing die gebruikers kunnen installeren op een HDInsight-cluster. Deze toepassingen kunnen zijn ontwikkeld door Microsoft, door onafhankelijke softwareleveranciers (ISV) of door u zelf.  

In de volgende lijst worden de gepubliceerde aanvragen weergegeven:

|Toepassing |Clustertype(s) | Beschrijving |
|---|---|---|
|[AtScale Intelligence Platform](https://azuremarketplace.microsoft.com/marketplace/apps/atscaleinc.atscale) |Hadoop |AtScale verandert uw HDInsight-cluster in een scale-out OLAP-server, zodat u miljarden rijen gegevens interactief opvragen met behulp van de BI-tools die u al kent, bezit en lief hebt - van Microsoft Excel, Power BI, Tableau Software tot QlikView. |
|[CDAP voor HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/cask.cdap-for-hdinsight) |HBase |CDAP is het eerste uniforme integratieplatform voor big data dat de time-to-value voor Hadoop versnelt en IT in staat stelt om selfservicegegevens te leveren. Open source en uitbreidbaar, CDAP verwijdert belemmeringen voor innovatie. Vereisten: 4 regioknooppunten, min D3 v2. |
|[Datameer](https://azuremarketplace.microsoft.com/marketplace/apps/datameer.datameer) |Hadoop |Datameer's self-service schaalbare platform voor het voorbereiden, verkennen en beheren van uw gegevens voor analytics versnelt het omzetten van complexe multisource-gegevens in waardevolle bedrijfsklare informatie, waardoor snellere, slimmere inzichten op bedrijfsschaal worden geleverd. |
|[Dataiku DSS op HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Vonk |Dataiku DSS in een enterprise data science-platform waarmee datawetenschappers en data-analisten samenwerken om nieuwe dataproducten en -services efficiënter te ontwerpen en uit te voeren, waardoor ruwe gegevens worden omgezet in impactvolle voorspellingen. |
|[WANdisco Fusion HDI-app](https://azuremarketplace.microsoft.com/marketplace/apps/wandisco.fusion-hdi-app) |Hadoop, Spark, HBase, Storm, Kafka |Het consistent houden van gegevens in een gedistribueerde omgeving is een enorme uitdaging voor dataoperations. WANdisco Fusion, een softwareplatform van bedrijfsniveau, lost dit probleem op door ongestructureerde gegevensconsistentie in elke omgeving mogelijk te maken. |
|[H2O-bruisend water voor HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O Sparkling Water ondersteunt de volgende gedistribueerde algoritmen: GLM, Naïve Bayes, Distributed Random Forest, Gradient Boosting Machine, Deep Neural Networks, Deep learning, K-means, PCA, Generalized Low Rank Models, Anomaly Detection, Autoencoders. |
|[Striim voor real-time data-integratie naar HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/striim.hdinsightintegration) |Hadoop, HBase, Storm, Spark, Kafka |Striim (uitgesproken als "stream") is een end-to-end streaming data-integratie + intelligence platform, waardoor continue opname, verwerking en analyse van ongelijksoortige gegevensstromen mogelijk is. |
|[Jumbune Enterprise-Accelerating BigData Analytics](https://azuremarketplace.microsoft.com/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Vonk |Op hoog niveau staat Jumbune ondernemingen bij door 1. Versnellen Tez, MapReduce & Spark engine based Hive, Java, Scala workload performance. 2. Proactieve Hadoop Cluster Monitoring, 3. Het opstellen van datakwaliteitsbeheer op gedistribueerd bestandssysteem. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/kyligence.kyligence) |Hadoop, HBase, Spark |Kyligence Enterprise wordt aangedreven door Apache Kylin en maakt BI mogelijk op Big Data. Als enterprise OLAP-engine op Hadoop stelt Kyligence Enterprise bedrijfsanalist in staat om BI op Hadoop te architecten met industriestandaard datawarehouse en BI-methodologie. |
|[Starburst Presto voor Azure HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/starburst.starburst-presto) |Hadoop |Presto is een snelle en schaalbare gedistribueerde SQL-queryengine. Presto is ontworpen voor de scheiding van opslag en rekenkracht en is perfect voor het opvragen van gegevens in Azure Data Lake Storage, Azure Blob Storage, SQL- en NoSQL-databases en andere gegevensbronnen. |
|[StreamSets Data Collector voor HDInsight Cloud](https://azuremarketplace.microsoft.com/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop, HBase, Spark, Kafka |StreamSets Data Collector is een lichtgewicht, krachtige engine die gegevens in realtime streamt. Gebruik Data Collector om gegevens in uw gegevensstromen te routeren en te verwerken. Het wordt geleverd met een 30 dagen trial licentie. |
|[Trifacta Wrangler Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/trifacta.tr01) |Hadoop, Spark, HBase |Trifacta Wrangler Enterprise voor HDInsight ondersteunt enterprise-wide data wrangling voor elke schaal van gegevens. De kosten van het uitvoeren van Trifacta op Azure is een combinatie van Trifacta-abonnementskosten plus de Azure-infrastructuurkosten voor de virtuele machines. |
|[Unifi-gegevensplatform](https://unifisoftware.com/platform/) |Hadoop, HBase, Storm, Spark |Het Unifi Data Platform is een naadloos geïntegreerde suite van self-service data tools die zijn ontworpen om de zakelijke gebruiker in staat te stellen data-uitdagingen aan te pakken die incrementele inkomsten genereren, kosten verlagen of operationele complexiteit. |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |Unravel Data-app voor HDInsight Spark-cluster. |
|[Waterline AI-gestuurde gegevenscatalogus](https://azuremarketplace.microsoft.com/marketplace/apps/waterline_data.waterline_data) |Spark |Waterline catalogiseert, organiseert en regelt gegevens met Behulp van AI om gegevens automatisch te taggen met zakelijke voorwaarden. De bedrijfsgeletterde catalogus van Waterline is een essentieel succesonderdeel voor selfservice-analyses, compliance- en governance- en IT-beheerinitiatieven. |

De instructies in dit artikel zijn bestemd voor gebruik met Azure Portal. U de sjabloon Azure Resource Manager ook exporteren vanuit de portal of een kopie van de resourcemanagersjabloon van leveranciers verkrijgen en Azure PowerShell en Azure Classic CLI gebruiken om de sjabloon te implementeren.  Zie [Apache Hadoop-clusters maken op HDInsight met behulp van Resource Manager-sjablonen](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Vereisten
Als u HDInsight-toepassingen wilt installeren op een bestaand HDInsight-cluster, hebt u een HDInsight-cluster nodig. Zie [Clusters maken](hadoop/apache-hadoop-linux-tutorial-get-started.md) voor informatie over het maken van een cluster. U kunt ook HDInsight-toepassingen installeren wanneer u een HDInsight-cluster maakt.

## <a name="install-applications-to-existing-clusters"></a>Toepassingen installeren in bestaande clusters
De volgende procedure beschrijft hoe u HDInsight-toepassingen in een bestaand HDInsight-cluster installeert.

**Een HDInsight-toepassing installeren**

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Navigeer in het linkermenu naar alle**analytics-clusters van** **Analytics.** > **Analytics** > 
3. Selecteer een HDInsight-cluster in de lijst.  Als u deze niet hebt, maakt u die eerst.  Zie [Clusters maken](hadoop/apache-hadoop-linux-tutorial-get-started.md).
4. Selecteer **Toepassingen**onder de categorie **Instellingen** . U een lijst met geïnstalleerde toepassingen in het hoofdvenster bekijken. 
   
    ![Menu van HDInsight-toepassingenportal](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Selecteer **+Toevoegen in** het menu. U een lijst met beschikbare toepassingen bekijken.  Als **+Add** grijs is, betekent dit dat er geen toepassingen zijn voor deze versie van het HDInsight-cluster.
   
    ![HDInsight-toepassingen, beschikbare toepassingen](./media/hdinsight-apps-install-applications/hdinsight-apps-list1.png)
6. Selecteer een van de beschikbare toepassingen en volg vervolgens de instructies om de wettelijke voorwaarden te accepteren.

U de installatiestatus zien van de portalmeldingen (selecteer het belpictogram boven aan de portal). Nadat de toepassing is geïnstalleerd, wordt de toepassing weergegeven in de lijst Geïnstalleerde apps.

## <a name="install-applications-during-cluster-creation"></a>Toepassingen installeren tijdens het maken van het cluster

U hebt ook de optie HDInsight-toepassingen te installeren wanneer u een cluster maakt. Tijdens het proces worden HDInsight-toepassingen geïnstalleerd nadat het cluster is gemaakt en actief is. Als u toepassingen wilt installeren tijdens het maken van het cluster met de Azure-portal, selecteert u op het tabblad **Configuratie + prijzen** de optie + Toepassing **toevoegen**.

![Configuratietoepassingen voor Azure-portalcluster](./media/hdinsight-apps-install-applications/azure-portal-cluster-configuration-applications.png)

## <a name="list-installed-hdinsight-apps-and-properties"></a>Lijst van geïnstalleerde HDInsight-apps en -eigenschappen
De portal toont een lijst van de geïnstalleerde HDInsight-toepassingen voor een cluster en de eigenschappen van elke geïnstalleerde toepassing.

**HdInsight-toepassings- en weergave-eigenschappen weergeven**

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Navigeer in het linkermenu naar alle**analytics-clusters van** **Analytics.** > **Analytics** > 
3. Selecteer een HDInsight-cluster in de lijst.
4. Selecteer **Toepassingen**onder de categorie **Instellingen** . U een lijst met geïnstalleerde toepassingen in het hoofdvenster bekijken. 
   
    ![HDInsight-toepassingen, geïnstalleerde apps](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Selecteer een van de geïnstalleerde toepassingen om de eigenschap weer te geven. De eigenschap lijsten:

    |Eigenschap | Beschrijving |
    |---|---|
    |Naam van app |Toepassingsnaam. |
    |Status |Toepassingsstatus. |
    |Webpagina |De URL van de webtoepassing die u hebt geïmplementeerd op het randknooppunt. De referenties zijn dezelfde als de HTTP-gebruikersreferenties die u hebt geconfigureerd voor het cluster. |
    |SSH-eindpunt |U SSH gebruiken om verbinding te maken met het randknooppunt. De SSH-referenties zijn dezelfde als de SSH-gebruikersreferenties die u hebt geconfigureerd voor het cluster. Zie [SSH-sleutels gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) voor informatie. |
    |Beschrijving | Beschrijving van de aanvraag. |

6. Als u een toepassing wilt verwijderen, klikt u met de rechtermuisknop op de toepassing en klikt u vervolgens op **Verwijderen** in het contextmenu.

## <a name="connect-to-the-edge-node"></a>Verbinding maken met het edge-knooppunt
U kunt verbinding maken met het edge-knooppunt door middel van HTTP en SSH. Informatie over het eindpunt kunt u vinden in de [portal](#list-installed-hdinsight-apps-and-properties). Zie [SSH-sleutels gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) voor informatie.

De HTTP-eindpuntreferenties zijn de HTTP-gebruikersreferenties die u hebt geconfigureerd voor het HDInsight-cluster. De SSH-eindpuntreferenties zijn de SSH-referenties die u hebt geconfigureerd voor het HDInsight-cluster.

## <a name="troubleshoot"></a>Problemen oplossen
Zie [Problemen met de installatie oplossen](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Volgende stappen
* [Installeer aangepaste HDInsight-toepassingen:](hdinsight-apps-install-custom-applications.md)leer hoe u een niet-gepubliceerde HDInsight-toepassing implementeert op HDInsight.
* [HDInsight-toepassingen publiceren](hdinsight-apps-publish-applications.md): informatie over het publiceren van aangepaste HDInsight-toepassingen in Azure Marketplace.
* [MSDN: een HDInsight-toepassing installeren](https://msdn.microsoft.com/library/mt706515.aspx): informatie over het definiëren van HDInsight-toepassingen.
* [Op Linux gebaseerde HDInsight-clusters aanpassen met behulp van een scriptactie](hdinsight-hadoop-customize-cluster-linux.md): informatie over het gebruik van een scriptactie om extra toepassingen te installeren.
* [Maak Apache Hadoop-clusters op basis van Linux in HDInsight met behulp van Resource Manager-sjablonen:](hdinsight-hadoop-create-linux-clusters-arm-templates.md)lees hoe u Resource Manager-sjablonen aanroept om HDInsight-clusters te maken.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Lege edge-knooppunten gebruiken in HDInsight): meer informatie over het gebruik van een leeg edge-knooppunt om toegang te krijgen tot het HDInsight-cluster, HDInsight toepassingen te testen en HDInsight-toepassingen te hosten.

