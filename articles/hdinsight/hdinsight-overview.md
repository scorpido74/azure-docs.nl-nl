---
title: Wat is Azure HDInsight?
description: Een inleiding tot HDInsight en de Apache Hadoop-en Apache Spark technologie stack en-onderdelen, inclusief Kafka, Hive, Storm en HBase voor big data analyse.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: overview
ms.custom: hdinsightactive,hdiseo17may2017,mvc,seodec18
ms.date: 02/27/2020
ms.openlocfilehash: 31e8506067133304144614ff58974ee21e9680be
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "79241218"
---
# <a name="what-is-azure-hdinsight"></a>Wat is Azure HDInsight?

Azure HDInsight is een beheerde, open-source analyse service in de Cloud voor ondernemingen. U kunt populaire opensourcekaders zoals Hadoop, Apache Spark, Apache Hive, LLAP, Apache Kafka, Apache Storm, R, enzovoort gebruiken.

## <a name="what-is-hdinsight-and-the-hadoop-technology-stack"></a>Wat is HDInsight en de Hadoop-technologiestack?

Azure HDInsight is een Cloud distributie van Hadoop-onderdelen. Azure HDInsight maakt het eenvoudig, snel en kosteneffectief om enorme hoeveelheden gegevens te verwerken. U kunt de populairste opensourcekaders gebruiken, zoals Hadoop, Spark, Hive, LLAP, Kafka, Storm, R enz. Met deze kaders kunt u ook een breed scala aan scenario's inschakelen, zoals extraheren, transformeren en laden (ETL), gegevensopslag, machine learning en IoT.

Raadpleeg [Components and versions available with HDInsight](./hdinsight-component-versioning.md) (Onderdelen en versies die beschikbaar zijn in HDInsight) om na te gaan welke onderdelen van de Hadoop-technologiestack in HDInsight beschikbaar zijn. Zie de [pagina met Azure-functies voor HDInsight](https://azure.microsoft.com/services/hdinsight/) voor meer informatie over Hadoop in HDInsight.

## <a name="what-is-big-data"></a>Wat is big data?

Big data wordt verzameld in steeds sneller groeiende volumes, met een steeds hogere snelheid en in een groter aantal indelingen dan ooit tevoren. Bij big data kan het gaan om historische gegevens (opgeslagen gegevens), maar ook om realtime gegevens (gestreamd vanuit de bron). Zie [Scenario's voor het gebruik van HDInsight](#scenarios-for-using-hdinsight) voor meer informatie over de meest voorkomende gebruiksvoorbeelden voor big data.

## <a name="why-should-i-use-azure-hdinsight"></a>Waarom moet ik Azure HDInsight gebruiken?

In deze sectie worden de mogelijkheden van Azure HDInsight toegelicht.

|Mogelijkheid  |Beschrijving  |
|---------|---------|
|Cloudeigen     |     Met Azure HDInsight kunt u geoptimaliseerde clusters maken voor [Hadoop](./hadoop/apache-hadoop-linux-tutorial-get-started.md), [Apache Spark](./spark/apache-spark-jupyter-spark-sql.md), [interactieve query’s (LLAP)](./interactive-query/apache-interactive-query-get-started.md), [Kafka](./kafka/apache-kafka-get-started.md), [ Storm](./storm/apache-storm-tutorial-get-started-linux.md), [HBase](./hbase/apache-hbase-tutorial-get-started-linux.md) en [ML Services](./r-server/r-server-overview.md) in Azure. HDInsight Biedt tevens een end-to-end SLA voor al uw productieworkloads.  |
|Voordelig en schaalbaar     | Met HDInsight kunt u werk belastingen omhoog of omlaag [schalen](./hdinsight-administer-use-portal-linux.md#scale-clusters) .U kunt de kosten verlagen door [clusters op aanvraag](./hdinsight-hadoop-create-linux-clusters-adf.md) te maken en alleen te betalen voor wat u gebruikt. U kunt ook gegevenspijplijnen maken om uw taken operationeel maken. Rekenwerk en opslag zijn niet langer gekoppeld, voor betere prestaties en flexibiliteit. |
|Beveiligd en compatibel    | Met HDInsight kunt u uw zakelijke gegevensassets beschermen met [Azure Virtual Network](./hdinsight-plan-virtual-network-deployment.md), [versleuteling](./hdinsight-hadoop-create-linux-clusters-with-secure-transfer-storage.md) en integratie met [Azure Active Directory](./domain-joined/hdinsight-security-overview.md). HDInsight voldoet aan de meeste populaire [nalevingsstandaarden](https://azure.microsoft.com/overview/trusted-cloud) van de industrie en de overheid.        |
|Bewaking    | Azure HDInsight kan worden geïntegreerd met [Azure Monitor-logboeken](./hdinsight-hadoop-oms-log-analytics-tutorial.md) en biedt zo één enkele interface waarmee u al uw clusters kunt bewaken.        |
|Wereldwijde beschikbaarheid | HDInsight is beschikbaar in meer [regio's](https://azure.microsoft.com/regions/services/) dan enige andere Big data analyse-aanbieding. Azure HDInsight is ook beschikbaar in Azure Government, China en Duitsland, waarmee u kunt voldoen aan de behoeften van uw bedrijf in belangrijke soevereine gebieden. |  
|Productiviteit     |  Met Azure HDInsight kunt u geavanceerde hulpprogramma's voor Hadoop en Spark gebruiken in de ontwikkelomgeving van uw keuze. De beschikbare ontwikkelomgevingen zijn [Visual Studio](./hadoop/apache-hadoop-visual-studio-tools-get-started.md), [VSCode](./hdinsight-for-vscode.md), [Eclipse](./spark/apache-spark-eclipse-tool-plugin.md) en [IntelliJ](./spark/apache-spark-intellij-tool-plugin.md) voor ondersteuning van Scala, Python, R, Java en .NET. Gegevensanalisten kunnen ook samenwerken met behulp van populaire notebooks zoals [Jupyter](./spark/apache-spark-jupyter-notebook-kernels.md) en [Zeppelin](./spark/apache-spark-zeppelin-notebook.md).    |
|Uitbreidbaarheid     |  U kunt de HDInsight-clusters uitbreiden met geïnstalleerde onderdelen (Hue, Presto enz.) met behulp van [scriptacties](./hdinsight-hadoop-customize-cluster-linux.md), door [edge-knooppunten toe te voegen](./hdinsight-apps-use-edge-node.md) of via [integratie met andere toepassingen die zijn gecertificeerd voor big data](./hdinsight-apps-install-applications.md). Met HDInsight kunt u naadloos integreren met de meest populaire big data-oplossingen met [éénkliksimplementatie](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/).|

## <a name="scenarios-for-using-hdinsight"></a>Scenario's voor het gebruik van HDInsight

Azure HDInsight kan worden gebruikt voor tal van scenario's in big data-verwerking. Hierbij kan het gaan om historische gegevens (gegevens die al zijn verzameld en opgeslagen), maar ook om realtime gegevens (gegevens die rechtstreeks vanuit de bron worden gestreamd). De scenario's voor het verwerken van deze gegevens kunnen worden ingedeeld in de volgende categorieën:

### <a name="batch-processing-etl"></a>Batchverwerking (ETL)

Extraheren, transformeren en laden (ETL) is een proces waarbij ongestructureerde of gestructureerde gegevens worden geëxtraheerd uit heterogene gegevensbronnen. Deze gegevens worden vervolgens omgezet in een gestructureerde indeling en in een gegevensarchief geladen. U kunt de getransformeerde gegevens gebruiken voor data science of datawarehousing.

### <a name="data-warehousing"></a>Datawarehousing

Met HDInsight kunt u interactieve query's op petabyte-schaal uitvoeren voor gestructureerde of ongestructureerde gegevens in elke indeling. U kunt ook modellen bouwen die hen verbinden met BI-hulpprogramma’s. Lees dit [verhaal van een klant](https://customers.microsoft.com/story/milliman) voor meer informatie.

![HDInsight-architectuur: datawarehousing](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-data-warehouse.png "Gegevens opslag architectuur HDInsight")

### <a name="internet-of-things-iot"></a>Internet der dingen (IoT)

U kunt HDInsight gebruiken voor het verwerken van streaminggegevens die in real-time worden ontvangen vanaf verschillende soorten apparaten. Voor meer informatie leest u [deze Azure-blogpost](https://azure.microsoft.com/blog/announcing-public-preview-of-apache-kafka-on-hdinsight-with-azure-managed-disks/) waarin de openbare preview van Apache Kafka in HDInsight met Azure Managed Disks wordt aangekondigd.

![HDInsight-architectuur: Internet of Things](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-iot.png "HDInsight IoT-architectuur")

### <a name="data-science"></a>Data Science

Met HDInsight kunt u toepassingen ontwikkelen die belangrijke inzichten uit gegevens halen. U kunt hierbij ook Azure Machine Learning gebruiken om toekomstige ontwikkelingen voor uw bedrijf te voorspellen. Lees dit [verhaal van een klant](https://customers.microsoft.com/story/pros) voor meer informatie.

![HDInsight-architectuur: data science](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-data-science.png "HDInsight data Science-architectuur")

### <a name="hybrid"></a>Hybride

Met HDInsight kunt u uw bestaande on-premises big data-infrastructuur uitbreiden naar Azure om gebruik te maken van de mogelijkheden voor geavanceerde analyses van de cloud.

![HDInsight-architectuur: hybride](./hadoop/media/apache-hadoop-introduction/hdinsight-architecture-hybrid.png "Hybride HDInsight-architectuur")

## <a name="cluster-types-in-hdinsight"></a>Clustertypen in HDInsight

HDInsight omvat specifieke clustertypen en opties voor clusteraanpassing, zoals de mogelijkheid om onderdelen, hulpprogramma's en talen toe te voegen. HDInsight biedt de volgende clustertypen:

|Clustertype | Beschrijving |
|---|---|
|[Apache Hadoop](./hadoop/apache-hadoop-introduction.md)|Een kader dat gebruikmaakt van HDFS, YARN voor resourcebeheer en een eenvoudig MapReduce-programmeermodel om batchgegevens parallel te verwerken en te analyseren.|
|[Apache Spark](./spark/apache-spark-overview.md)|Een opensourcekader voor parallelle verwerking dat ondersteuning biedt voor in-memory verwerking om de prestaties van toepassingen voor de analyse van big data te verbeteren. Zie [Wat is Apache Spark in HDInsight?](./spark/apache-spark-overview.md).|
|[Apache HBase](./hbase/apache-hbase-overview.md)|Een NoSQL-database gebouwd op Hadoop. Deze biedt willekeurige toegang en sterke consistentie voor grote hoeveelheden (mogelijk miljarden rijen bij miljoenen kolommen) ongestructureerde en semi-gestructureerde gegevens. Zie [Wat is HBase in HDInsight?](./hbase/apache-hbase-overview.md)|
|[ML Services](./r-server/r-server-overview.md)|Een server voor de hosting van websites en het beheren van parallelle, gedistribueerde R-processen. Het biedt gegevenswetenschappers, statistici en R-programmeurs on-demand toegang tot schaalbare, gedistribueerde analysemethoden in HDInsight. Zie [Overzicht van ML Services in HDInsight](./r-server/r-server-overview.md).|
|[Apache Storm](./storm/apache-storm-overview.md)|Een gedistribueerd, realtime berekeningssysteem voor het snel verwerken van grote gegevensstromen. Storm wordt aangeboden als beheerd cluster in HDInsight. Zie [Realtime-sensorgegevens analyseren met Storm en Hadoop](./storm/apache-storm-overview.md).|
|[Apache-interactieve query](./interactive-query/apache-interactive-query-get-started.md)|Caching in geheugen voor interactieve en snellere Hive-query's. Zie [Interactive Query gebruiken in HDInsight](./interactive-query/apache-interactive-query-get-started.md).|
|[Apache Kafka](./kafka/apache-kafka-introduction.md)|Een opensourceplatform dat wordt gebruikt voor het bouwen van pijplijnen en toepassingen voor het streamen van gegevens. Kafka biedt ook berichtenwachtrijfunctionaliteit waarmee u gegevensstromen kunt publiceren en zich kunt abonneren op gegevensstromen. Zie [Inleiding tot Apache Kafka in HDInsight](./kafka/apache-kafka-introduction.md).|

## <a name="open-source-components-in-hdinsight"></a>Opensource-onderdelen in HDInsight

Met Azure HDInsight kunt u clusters maken met opensourcekaders zoals Hadoop, Spark, Hive, LLAP, Kafka, Storm, HBase en R. Deze clusters worden standaard geleverd met andere opensource-onderdelen die zijn opgenomen in het cluster, zoals [Apache Ambari](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md), [Avro](https://avro.apache.org/docs/current/spec.html), [Apache Hive](https://hive.apache.org), [HCatalog](https://cwiki.apache.org/confluence/display/Hive/HCatalog/), [Apache Mahout](https://mahout.apache.org/), [Apache Hadoop MapReduce](https://hadoop.apache.org/docs/current/hadoop-mapreduce-client/hadoop-mapreduce-client-core/MapReduceTutorial.html), [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html), [Apache Phoenix](https://phoenix.apache.org/), [Apache Pig](https://pig.apache.org/), [Apache Sqoop](https://sqoop.apache.org/), [Apache Tez](https://tez.apache.org/), [Apache Oozie](https://oozie.apache.org/) en [Apache ZooKeeper](https://zookeeper.apache.org/).  

## <a name="programming-languages-in-hdinsight"></a>Programmeertalen in HDInsight

HDInsight-clusters, waaronder Spark, HBase, Kafka, Hadoop en andere, bieden ondersteuning voor een groot aantal programmeertalen. Sommige programmeertalen worden niet standaard geïnstalleerd. Gebruik voor bibliotheken, modules of pakketten die niet standaard zijn geïnstalleerd, [een script actie om het onderdeel te installeren](./hdinsight-hadoop-script-actions-linux.md).

|Programmeertaal  |Informatie  |
|---------|---------|
|Standaardondersteuning voor programmeertalen     | Standaard bieden HDInsight-clusters ondersteuning voor:<ul><li>Java</li><li>Python</li><li>.NET</li><li>Aan de slag</li></ul>  |
|JVM-talen (Java Virtual Machine)     | Op een virtuele Java-machine (JVM) kunnen naast Java ook vele andere talen worden uitgevoerd. Voor sommige van deze talen moet u mogelijk echter aanvullende onderdelen in het cluster installeren. De volgende JVM-talen worden in HDInsight-clusters ondersteund: <ul><li>Clojure</li><li>Jython (Python voor Java)</li><li>Scala</li></ul>     |
|Hadoop-specifieke talen     | HDInsight-clusters bieden ondersteuning voor de volgende talen die specifiek zijn voor de Hadoop-technologiestack: <ul><li>Pig Latin voor Pig-taken</li><li>HiveQL voor Hive-taken en SparkSQL</li></ul>        |

## <a name="development-tools-for-hdinsight"></a>Ontwikkelingsprogramma's voor HDInsight

U kunt HDInsight-ontwikkelingsprogramma's zoals IntelliJ, Eclipse, Visual Studio Code en Visual Studio gebruiken om HDInsight-gegevensquery's en taken te schrijven en te verzenden met naadloze integratie in Azure.

* [Azure Toolkit voor IntelliJ](./spark/apache-spark-intellij-tool-plugin.md)

* [Azure Toolkit voor eclips](./spark/apache-spark-eclipse-tool-plugin.md)

* [Azure HDInsight Tools voor IntelliJ](./hdinsight-for-vscode.md)

* [Azure Data Lake-tools voor Visual Studio](./hadoop/apache-hadoop-visual-studio-tools-get-started.md)

## <a name="business-intelligence-on-hdinsight"></a>Business Intelligence in HDInsight

Voor het ophalen, analyseren en rapporteren van met HDInsight geïntegreerde gegevens kunt u gebruikmaken van gangbare hulpprogramma's voor Business Intelligence (BI). Hiervoor is de invoegtoepassing Power Query of het ODBC-stuurprogramma Microsoft Hive vereist.

* [Apache Spark BI met gebruik van hulpmiddelen voor gegevensvisualisatie met Azure HDInsight](./spark/apache-spark-use-bi-tools.md)

* [Apache Hive-gegevens visualiseren met Microsoft Power BI in Azure HDInsight](./hadoop/apache-hadoop-connect-hive-power-bi.md)

* [Interactive Query Hive-gegevens visualiseren met Power BI in Azure HDInsight](./interactive-query/apache-hadoop-connect-hive-power-bi-directquery.md)

* [Excel koppelen aan Apache Hadoop met behulp van Power Query](./hadoop/apache-hadoop-connect-excel-power-query.md) (vereist Windows)

* [Excel koppelen aan Apache Hadoop met behulp van het Hive ODBC-stuurprogramma van Microsoft](./hadoop/apache-hadoop-connect-excel-hive-odbc-driver.md) (vereist Windows)

## <a name="next-steps"></a>Volgende stappen

* [Apache Hadoop-cluster maken in HDInsight](./hadoop/apache-hadoop-linux-create-cluster-get-started-portal.md)
