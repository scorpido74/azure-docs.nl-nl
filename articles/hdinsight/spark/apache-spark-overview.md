---
title: Wat is Apache Spark - Azure HDInsight
description: In dit artikel maakt u kennis met Spark in HDInsight en de verschillende scenario's waarin u een Spark-cluster kunt gebruiken in HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.custom: hdinsightactive,mvc,seoapr2020
ms.topic: overview
ms.date: 04/17/2020
ms.openlocfilehash: e933d48a60a01bdbf00205a389778b9a3befeede
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82188222"
---
# <a name="what-is-apache-spark-in-azure-hdinsight"></a>Wat is Apache Spark in Azure HDInsight?

Apache Spark is een framework voor parallelle verwerking dat ondersteuning biedt voor in-memory verwerking om de prestaties van toepassingen voor de analyse van big data te verbeteren. Apache Spark in Azure HDInsight is Microsoft's implementatie van Apache Spark in de cloud. Met HDInsight kunt u eenvoudiger Spark-clusters maken en configureren in Azure. Spark-clusters in HDInsight zijn compatibel met Azure Storage en Azure Data Lake Storage. Dit betekent dat u HDInsight Spark-clusters kunt gebruiken om gegevens te verwerken die zijn opgeslagen in Azure. Zie [Apache Hadoop-onderdelen en versies in Azure HDInsight](../hdinsight-component-versioning.md) voor informatie over de onderdelen en versies.

![Spark: een geünificeerd framework](./media/apache-spark-overview/hdinsight-spark-overview.png)

## <a name="what-is-apache-spark"></a>Wat is Apache Spark?

Spark biedt primitieve typen voor in-memory clustercomputing. Een Spark-taak kan gegevens laden en in het geheugen cachen en er herhaaldelijk query’s op uitvoeren. Verwerkingen in het geheugen (in-memory) zijn veel sneller dan toepassingen die op schijf worden uitgevoerd, zoals Hadoop, dat gegevens deelt via HDFS (Hadoop Distributed File System). Apache Spark kan ook worden geïntegreerd met de programmeertaal Scala waarmee u gedistribueerde gegevenssets zoals lokale verzamelingen kunt bewerken. Het is niet nodig om alles te structureren als toewijzings- en verminderingsbewerkingen.

![Traditioneel MapReduce vs. Spark](./media/apache-spark-overview/map-reduce-vs-spark1.png)

Spark-clusters in HDInsight bieden een volledig beheerde Spark-service. Dit zijn de voordelen van het maken van een Spark-cluster in HDInsight.

| Functie | Beschrijving |
| --- | --- |
| Eenvoudig te maken |U kunt binnen enkele minuten een nieuw Spark-cluster in HDInsight maken met Azure Portal, Azure PowerShell of de .NET-SDK voor HDInsight. Zie [aan de slag met Apache Spark cluster in HDInsight](apache-spark-jupyter-spark-sql-use-portal.md). |
| Gebruiksgemak |Spark-cluster in HDInsight bevat Jupyter- en Apache Zeppelin-notebooks. U kunt deze notebooks gebruiken voor interactieve gegevensverwerking en visualisatie. Zie [Apache Zeppelin-notebooks gebruiken met Apache Spark](apache-spark-zeppelin-notebook.md) en [gegevens laden en query's uitvoeren op een Apache Spark cluster](apache-spark-load-data-run-query.md).|
| REST-API’s |Apache Spark-clusters in HDInsight omvatten [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), een op REST API gebaseerde Apache Spark-taakserver om op afstand taken te verzenden en te controleren. Zie [Apache Spark REST API gebruiken voor het verzenden van externe taken naar een HDInsight Spark-cluster](apache-spark-livy-rest-interface.md).|
| Ondersteuning voor Azure Data Lake Storage | Spark-clusters in HDInsight kunnen Azure Data Lake Storage gebruiken als de primaire opslag of als extra opslag. Zie [Overzicht van Azure Data Lake Storage](../../data-lake-store/data-lake-store-overview.md) voor meer informatie over Data Lake Storage. |
| Integratie met Azure-services |Spark-clusters in HDInsight worden geleverd met een connector voor Azure Event Hubs. U kunt streaming-toepassingen maken met behulp van de Event Hubs. Inclusief Apache Kafka, dat al beschikbaar is als onderdeel van Spark. |
| Ondersteuning voor ML Server | Ondersteuning voor ML Server in HDInsight wordt geleverd als het clustertype **ML Services**. U kunt een ML Services-cluster instellen om gedistribueerde R-berekeningen uit te voeren met de snelheden van een Spark-cluster. Zie [Wat is ml-Services in azure HDInsight](../r-server/r-server-overview.md)voor meer informatie. |
| Integratie met IDE's van derden | HDInsight biedt verschillende IDE-invoegtoepassingen die handig zijn voor het maken en verzenden van toepassingen naar een HDInsight Spark-cluster. Zie [Azure-Toolkit voor IntelliJ-idee gebruiken](apache-spark-intellij-tool-plugin.md)voor meer informatie, [gebruik Spark & Hive tools voor VSCode](../hdinsight-for-vscode.md)en [gebruik Azure-Toolkit voor eclipse](apache-spark-eclipse-tool-plugin.md).|
| Gelijktijdige query's |Spark-clusters in HDInsight ondersteunen gelijktijdige query's. Hierdoor kunnen meerdere query's van één gebruiker of meerdere query's van verschillende gebruikers en toepassingen dezelfde clusterresources delen. |
| Opslaan in cache in SSD's |U kunt gegevens in het geheugen of in aan de clusterknooppunten gekoppelde SSD's cachen. Cachen in het geheugen biedt de beste queryprestaties, maar kan duur zijn. Cachen op SSD's is een goed alternatief voor het verbeteren van de prestaties van query's zonder dat er een cluster hoeft te worden gemaakt dat zo groot is dat de volledige gegevensset in het geheugen past. Zie de [prestaties van Apache Spark workloads verbeteren met behulp van de Azure HDINSIGHT io-cache](apache-spark-improve-performance-iocache.md). |
| Integratie met BI-tools |Spark-clusters in HDInsight bieden connectors voor BI-tools zoals Power BI voor gegevensanalyse. |
| Vooraf geladen Anaconda-bibliotheken |Spark-clusters in HDInsight worden geleverd met Anaconda-bibliotheken die vooraf zijn geïnstalleerd. [Anaconda](https://docs.continuum.io/anaconda/) biedt 200-bibliotheken voor machine learning, gegevens analyse, visualisatie, enzovoort. |
| Adaptief | Met HDInsight kunt u het aantal cluster knooppunten dynamisch wijzigen met de functie voor automatisch schalen. Zie [automatisch Azure HDInsight-clusters schalen](../hdinsight-autoscale-clusters.md). Bovendien kunnen Spark-clusters zonder gegevensverlies worden verwijderd, omdat alle gegevens zijn opgeslagen in Azure Storage of Data Lake Storage. |
| SLA |Spark-clusters in HDInsight worden geleverd met 24/7 ondersteuning en een SLA met een beschikbaarheid van 99,9%. |

Apache Spark-clusters in HDInsight omvatten de volgende onderdelen die standaard beschikbaar zijn in de clusters.

* [Spark Core](https://spark.apache.org/docs/latest/). Omvat Spark Core, Spark SQL, Spark-streaming-API's, GraphX en MLlib.
* [Anaconda](https://docs.continuum.io/anaconda/)
* [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
* [Jupyter-notebook](https://jupyter.org)
* [Apache Zeppelin-notebook](http://zeppelin-project.org/)

HDInsight Spark clusters een [ODBC-stuur programma](https://go.microsoft.com/fwlink/?LinkId=616229) voor connectiviteit vanuit bi-hulpprogram Ma's zoals micro soft power bi.

## <a name="spark-cluster-architecture"></a>Architectuur van Spark-cluster

![De architectuur van HDInsight Spark](./media/apache-spark-overview/hdi-spark-architecture.png)

Het is eenvoudig om inzicht te krijgen in de onderdelen van Spark door te begrijpen hoe Spark wordt uitgevoerd op HDInsight-clusters.

Spark-toepassingen worden uitgevoerd als onafhankelijke sets processen op een cluster. Gecoördineerd door het SparkContext-object in het hoofd programma (het stuur programma genoemd).

De SparkContext kan verbinding maken met verschillende typen cluster beheerders, die bronnen in verschillende toepassingen bieden. Deze cluster beheerders zijn onder andere Apache Mesos, [Apache HADOOP garens](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html)of Spark cluster manager. In HDInsight wordt Spark uitgevoerd met behulp van de YARN-clustermanager. Als er verbinding is, verkrijgt Spark executors op werkknooppunten in het cluster. Dit zijn processen die berekeningen uitvoeren en gegevens opslaan voor uw toepassing. Vervolgens wordt de code van uw toepassing (gedefinieerd in JAR- of Python-bestanden die worden doorgegeven aan SparkContext) verstuurd naar de executor. Ten slotte verzendt SparkContext taken naar de executors om uit te voeren.

SparkContext voert de hoofdfunctie van de gebruiker uit en voert de verschillende parallelle bewerkingen uit op de werkknooppunten. Vervolgens verzamelt SparkContext de resultaten van de bewerkingen. De werkrolknooppunten lezen en schrijven gegevens van en naar het HDFS (Hadoop Distributed File System). De werkrolknooppunten plaatsen getransformeerde gegevens ook in het geheugen als RDD’s (Resilient Distributed Datasets).

De SparkContext maakt verbinding met de Spark-Master en is verantwoordelijk voor het converteren van een toepassing naar een gestuurde grafiek (DAG) van afzonderlijke taken. Taken die worden uitgevoerd in een uitvoerings proces op de worker-knoop punten. Elke toepassing krijgt zijn eigen procedures voor de uitvoering. Voor de duur van de hele toepassing blijven en taken uitvoeren in meerdere threads.

## <a name="spark-in-hdinsight-use-cases"></a>Gebruiksscenario's voor Spark in HDInsight

Spark-clusters in HDInsight maken de volgende belangrijke scenario's mogelijk:

### <a name="interactive-data-analysis-and-bi"></a>Interactieve gegevensanalyse en BI

Met Apache Spark in HDInsight worden gegevens opgeslagen in Azure Storage of Azure Data Lake Storage. Zakelijke deskundigen en besluit vormers kunnen rapporten over die gegevens analyseren en bouwen. En gebruik micro soft Power BI om interactieve rapporten te bouwen op basis van de geanalyseerde gegevens. Analisten kunnen beginnen met ongestructureerde/semi-gestructureerde gegevens in clusteropslag, een schema voor de gegevens definiëren met behulp van notebooks, en vervolgens gegevensmodellen bouwen met behulp van Microsoft Power BI. Spark-clusters in HDInsight bieden ook ondersteuning voor een aantal BI-hulpprogram ma's van derden. Zoals tableau, waardoor het eenvoudiger is voor gegevens analisten, bedrijfs experts en belang rijke besluitvormings makers.

* [Zelfstudie: Spark-gegevens visualiseren met behulp van Power BI](apache-spark-use-bi-tools.md)

### <a name="spark-machine-learning"></a>Machine Learning in Spark

Apache Spark wordt geleverd met [MLlib](https://spark.apache.org/mllib/). MLlib is een machine learning bibliotheek die is gebouwd op Spark en die u kunt gebruiken vanuit een Spark-cluster in HDInsight. Spark-cluster in HDInsight omvat ook Anaconda, een python-distributie met verschillende soorten pakketten voor machine learning. En met ingebouwde ondersteuning voor Jupyter-en Zeppelin-notebooks hebt u een omgeving voor het maken van machine learning-toepassingen.

* [Zelf studie: voors pellen met behulp van HVAC-gegevens](apache-spark-ipython-notebook-machine-learning.md)  
* [Zelf studie: voedsel inspectie resultaten voors pellen](apache-spark-machine-learning-mllib-ipython.md)

### <a name="spark-streaming-and-real-time-data-analysis"></a>Streaming en realtime gegevensanalyse in Spark

Spark-clusters in HDInsight bieden uitgebreide ondersteuning voor het bouwen van realtime analyseoplossingen. Spark heeft al connectors om gegevens op te nemen uit verschillende bronnen, zoals Kafka, Flume, Twitter, ZeroMQ of TCP-sockets. Spark in HDInsight biedt ondersteuning voor de eerste klasse voor het opnemen van gegevens uit Azure Event Hubs. Event Hubs is de meest gebruikte wachtrijservice in Azure. Dankzij de volledige ondersteuning voor Event Hubs worden Spark-clusters in HDInsight een ideaal platform voor het bouwen van realtime analyse pijplijn.

* [Overzicht van Apache Spark streaming](apache-spark-streaming-overview.md)
* [Overzicht van Apache Spark Structured streaming](apache-spark-structured-streaming-overview.md)

## <a name="where-do-i-start"></a>Waar moet ik beginnen?

Raadpleeg de volgende artikelen voor meer informatie over Apache Spark in HDInsight:

* [Snelstartgids: een Apache Spark-cluster in HDInsight maken en interactieve query's uitvoeren met Jupyter](./apache-spark-jupyter-spark-sql-use-portal.md)
* [Zelf studie: een Apache Spark-taak uitvoeren met Jupyter](./apache-spark-load-data-run-query.md)
* [Zelf studie: gegevens analyseren met BI-hulpprogram ma's](./apache-spark-use-bi-tools.md)
* [Zelf studie: machine learning met Apache Spark](./apache-spark-ipython-notebook-machine-learning.md)
* [Zelf studie: een scala maven-toepassing maken met behulp van IntelliJ](./apache-spark-create-standalone-application.md)

## <a name="next-steps"></a>Volgende stappen

In dit overzicht hebt u algemene informatie gekregen over het gebruik van Apache Spark in Azure HDInsight. Meer informatie over het maken van een HDInsight Spark-cluster en het uitvoeren van een aantal Spark SQL-query's:

* [Een Apache Spark-cluster maken in Azure HDInsight](./apache-spark-jupyter-spark-sql-use-portal.md)
