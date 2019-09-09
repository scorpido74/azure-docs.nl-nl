---
title: Installeren van toepassingen van derden op Azure HDInsight
description: Meer informatie over het installeren van Apache Hadoop toepassingen van derden op Azure HDInsight.
author: hrasheed-msft
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: conceptual
ms.date: 06/17/2019
ms.author: hrasheed
ms.openlocfilehash: 8ed854a5775dfc20e258c14de51fe2066fa6caad
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70810271"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Apache Hadoop-toepassingen van derden installeren op Azure HDInsight

Informatie over het installeren van een derde partij [Apache Hadoop](https://hadoop.apache.org/) toepassing voor Azure HDInsight. Zie voor instructies voor de installatie van uw eigen toepassing [Aangepaste HDInsight-toepassingen installeren](hdinsight-apps-install-custom-applications.md).

Een HDInsight-toepassing is een toepassing die gebruikers op een HDInsight-cluster installeren kunnen. Deze toepassingen kunnen zijn ontwikkeld door Microsoft, door onafhankelijke softwareleveranciers (ISV) of door u zelf.  

De volgende lijst bevat de gepubliceerde toepassingen:

|Toepassing |Cluster type (n) | Description |
|---|---|---|
|[AtScale intelligence-platform](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/atscale.atscale) |Hadoop |Met AtScale wordt uw HDInsight-cluster omgezet in een scale-out OLAP-server, zodat u miljarden rijen gegevens interactief kunt opvragen met behulp van de BI-hulpprogram ma's die u al kent, eigenaar en liefde – van micro soft Excel, Power BI, tableau software naar QlikView. |
|[CDAP voor HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/cask.cdap-for-hdinsight) |HBase |CDAP is het eerste geïntegreerde integratie platform voor big data dat de time-to-waarde voor Hadoop versnelt en de IT-service in staat stelt om selfservice gegevens te leveren. Open source en uitbreid bare, CDAP verwijdert belemmeringen voor innovatie. Vereisten: 4 regio knooppunten, min D3 v2. |
|[Data meer](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/datameer.datameer) |Hadoop |Het selfservice schaal bare platform voor het voorbereiden, verkennen en beheren van uw gegevens voor analyse versnelt het omschakelen van complexe gegevens van meerdere bronnen tot waardevolle informatie over het bedrijf, waardoor u sneller, slimmere inzichten kunt leveren op bedrijfs niveau. |
|[Dataiku DSS op HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Spark |Dataiku DSS in een Enter prise data Science-platform waarmee gegevens wetenschappers en gegevens analisten kunnen samen werken om nieuwe gegevens producten en-services efficiënter te ontwerpen en uit te voeren, waardoor onbewerkte gegevens worden omgezet in impactieve voor spellingen. |
|[WANdisco Fusion HDI-app](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/wandisco.fusion-hdi-app) |Hadoop, Spark, HBase, Storm, Kafka |Het bijhouden van gegevens consistentie in een gedistribueerde omgeving is een enorme uitdaging voor gegevens bewerkingen. WANdisco Fusion, een software platform op bedrijfs niveau, lost dit probleem op door ongestructureerde gegevens consistentie in alle omgevingen in te scha kelen. |
|[H2O SparklingWater voor HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/h2o-ai.h2o-sparklingwater) |Spark |H2O-mousserend water ondersteunt de volgende gedistribueerde algoritmen: GLM, Naïve Bayes, gedistribueerde wille keurig forest, Gradient Boosting machine, diep Neural netwerken, diep gaande, K-houdt, PCA, gegeneraliseerde lage-positie modellen, afwijkings detectie, automatische code ring. |
|[Realtimeplatform voor realtime gegevens integratie naar HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/striim.hdinsightintegration) |Hadoop,HBase,Storm,Spark,Kafka |Realtimeplatform (uitgesp roken: ' stream ') is een end-to-end streaming Data Integration + intelligence-platform, waarmee u doorlopende opname, verwerking en analyse van verschillende gegevens stromen kunt maken. |
|[Jumbune Enter prise-Versnel BigData Analytics](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Spark |Op hoog niveau helpt Jumbune ondernemingen met, 1. Versnellen van TEZ, MapReduce & op basis van Spark-engine, Java, scala workload-prestaties. 2. Proactieve Hadoop-cluster bewaking, 3. Het kwaliteits beheer van gegevens in een gedistribueerd bestands systeem vaststellen. |
|[Kyligence Enter prise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/kyligence.kyligence) |Hadoop,HBase,Spark |Kyligence Enter prise, dat is aangedreven door Apache Kylin, maakt BI op Big data mogelijk. Als een Enter prise OLAP-engine op Hadoop kan Kyligence Enter prise de bedrijfs analist op Hadoop bouwen op basis van de industrie standaard data warehouse en BI-methodologie. |
|[Sterren explosie Presto voor Azure HDInsight](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/starburst.starburst-presto) |Hadoop |Presto is een snelle en schaal bare gedistribueerde SQL-query-engine. Ontworpen voor de scheiding van opslagruimte en rekenkracht, is Presto ideaal voor het opvragen van gegevens in Azure Data Lake Storage, Azure Blob Storage, SQL en NoSQL-databases en andere gegevensbronnen. |
|[StreamSets gegevens verzamelaar voor HDInsight-Cloud](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop,HBase,Spark,Kafka |StreamSets Data Collector is een licht gewicht krachtige engine waarmee gegevens in realtime worden gestreamd. Gebruik gegevens verzamelaar om gegevens in uw gegevens stromen te routeren en te verwerken. Het wordt geleverd met een proef licentie van 30 dagen. |
|[Trifacta Wrangler Enterprise](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/trifacta.tr01) |Hadoop, Spark,HBase |Trifacta Wrangler Enter prise voor HDInsight biedt ondersteuning voor zakelijke gegevens wrangling voor elke schaal van gegevens. De kosten voor het uitvoeren van Trifacta op Azure zijn een combi natie van kosten voor het Trifacta-abonnement plus de kosten van de Azure-infra structuur voor de virtuele machines. |
|[Unifi-gegevens platform](https://unifisoftware.com/platform/) |Hadoop,HBase,Storm,Spark |Het Unifi-gegevens platform is een naadloos geïntegreerde suite van self-service data tools, ontworpen om de zakelijke gebruiker in staat te stellen gegevens uitdagingen aan te pakken waarmee incrementele omzet wordt gestimuleerd, de kosten en de operationele complexiteit worden verminderd. |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |Spark |Unravel data-app voor HDInsight Spark-cluster. |
|[AI-Data Catalog met water breedte](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/waterline_data.waterline_data) |Spark |De water Raad catalogi, organiseert en bestuurt gegevens met behulp van AI om gegevens automatisch te labelen met bedrijfs voorwaarden. Waterlijn van zakelijke overweg catalog is een essentieel, succes-onderdeel voor selfservice-analyses, naleving en bestuur en initiatieven voor IT-beheer. |

De instructies in dit artikel zijn bestemd voor gebruik met Azure Portal. U kunt ook de Azure Resource Manager-sjabloon exporteren vanuit de portal of een kopie van de Resource Manager-sjabloon van leveranciers verkrijgen, en Azure PowerShell en de klassieke Azure-CLI gebruiken om de sjabloon te implementeren.  Zie [Apache Hadoop-clusters maken in HDInsight met behulp van Resource Manager-sjablonen](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Vereisten
Als u HDInsight-toepassingen wilt installeren op een bestaand HDInsight-cluster, hebt u een HDInsight-cluster nodig. Zie [Clusters maken](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster) voor informatie over het maken van een cluster. U kunt ook HDInsight-toepassingen installeren wanneer u een HDInsight-cluster maakt.

## <a name="install-applications-to-existing-clusters"></a>Toepassingen installeren in bestaande clusters
De volgende procedure beschrijft hoe u HDInsight-toepassingen in een bestaand HDInsight-cluster installeert.

**Een HDInsight-toepassing installeren**

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Ga in het menu links naar **alle services** > **Analytics** > **HDInsight-clusters**.
3. Selecteer een HDInsight-cluster in de lijst.  Als u deze niet hebt, maakt u die eerst.  Zie [Clusters maken](hadoop/apache-hadoop-linux-tutorial-get-started.md#create-cluster).
4. Selecteer in de categorie **instellingen** de optie **toepassingen**. In het hoofd venster ziet u een lijst met geïnstalleerde toepassingen. 
   
    ![Menu van HDInsight-toepassingenportal](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Selecteer **+ toevoegen** in het menu. U kunt een lijst met beschik bare toepassingen weer geven.  Als **+ add** grijs wordt weer gegeven, betekent dit dat er geen toepassingen zijn voor deze versie van het HDInsight-cluster.
   
    ![HDInsight-toepassingen, beschikbare toepassingen](./media/hdinsight-apps-install-applications/hdinsight-apps-list.png)
6. Selecteer een van de beschik bare toepassingen en volg de instructies om de juridische voor waarden te accepteren.

U kunt de installatie status bekijken via de meldingen van de portal (Selecteer het klok pictogram boven aan de portal). Nadat de toepassing is geïnstalleerd, wordt de toepassing wordt weergegeven in de lijst met geïnstalleerde Apps.

## <a name="install-applications-during-cluster-creation"></a>Toepassingen installeren tijdens het maken van het cluster
U hebt ook de optie HDInsight-toepassingen te installeren wanneer u een cluster maakt. Tijdens het proces worden HDInsight-toepassingen geïnstalleerd nadat het cluster is gemaakt en actief is. Als u toepassingen wilt installeren tijdens het maken van het cluster met behulp van de Azure Portal, gebruikt u de optie **aangepast** in plaats van de standaard optie voor **snel maken** .

## <a name="list-installed-hdinsight-apps-and-properties"></a>Lijst van geïnstalleerde HDInsight-apps en -eigenschappen
De portal toont een lijst van de geïnstalleerde HDInsight-toepassingen voor een cluster en de eigenschappen van elke geïnstalleerde toepassing.

**HDInsight-toepassingen en weergave van de eigenschappen weergeven**

1. Meld u aan bij [Azure Portal](https://portal.azure.com).
2. Ga in het menu links naar **alle services** > **Analytics** > **HDInsight-clusters**.
3. Selecteer een HDInsight-cluster in de lijst.
4. Selecteer in de categorie **instellingen** de optie **toepassingen**. In het hoofd venster ziet u een lijst met geïnstalleerde toepassingen. 
   
    ![HDInsight-toepassingen, geïnstalleerde apps](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Selecteer een van de geïnstalleerde toepassingen om de eigenschap weer te geven. De lijsten met zoekeigenschappen:

    |Eigenschap | Description |
    |---|---|
    |App-naam |Toepassings naam. |
    |Status |Toepassings status. |
    |Webpagina |De URL van de webtoepassing die u hebt geïmplementeerd op het Edge-knoop punt. De referenties zijn dezelfde als de HTTP-gebruikersreferenties die u hebt geconfigureerd voor het cluster. |
    |SSH-eindpunt |U kunt SSH gebruiken om verbinding te maken met het Edge-knoop punt. De SSH-referenties zijn dezelfde als de SSH-gebruikersreferenties die u hebt geconfigureerd voor het cluster. Zie [SSH-sleutels gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) voor informatie. |
    |Description | Beschrijving van toepassing. |

6. Als u wilt verwijderen van een toepassing, met de rechtermuisknop op de toepassing en klik vervolgens op **verwijderen** in het contextmenu.

## <a name="connect-to-the-edge-node"></a>Verbinding maken met het edge-knooppunt
U kunt verbinding maken met het edge-knooppunt door middel van HTTP en SSH. Informatie over het eindpunt kunt u vinden in de [portal](#list-installed-hdinsight-apps-and-properties). Zie [SSH-sleutels gebruiken met HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md) voor informatie.

De HTTP-eindpuntreferenties zijn de HTTP-gebruikersreferenties die u hebt geconfigureerd voor het HDInsight-cluster. De SSH-eindpuntreferenties zijn de SSH-referenties die u hebt geconfigureerd voor het HDInsight-cluster.

## <a name="troubleshoot"></a>Problemen oplossen
Zie [Problemen met de installatie oplossen](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation).

## <a name="next-steps"></a>Volgende stappen
* [Aangepaste HDInsight-toepassingen installeren](hdinsight-apps-install-custom-applications.md): informatie over het implementeren van een niet-gepubliceerde HDInsight-toepassing op HDInsight.
* [HDInsight-toepassingen publiceren](hdinsight-apps-publish-applications.md): Meer informatie over het publiceren van uw aangepaste HDInsight-toepassingen naar Azure Marketplace.
* [NETWORK Een HDInsight-toepassing](https://msdn.microsoft.com/library/mt706515.aspx)installeren: Meer informatie over het definiëren van HDInsight-toepassingen.
* [Op Linux gebaseerde HDInsight-clusters aanpassen met behulp van een scriptactie](hdinsight-hadoop-customize-cluster-linux.md): informatie over het gebruik van een scriptactie om extra toepassingen te installeren.
* [Apache Hadoop op basis van Linux-clusters in HDInsight met behulp van Resource Manager-sjablonen maken](hdinsight-hadoop-create-linux-clusters-arm-templates.md): informatie over het aanroepen van Resource Manager-sjablonen om HDInsight-clusters te maken.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Lege edge-knooppunten gebruiken in HDInsight): meer informatie over het gebruik van een leeg edge-knooppunt om toegang te krijgen tot het HDInsight-cluster, HDInsight toepassingen te testen en HDInsight-toepassingen te hosten.

