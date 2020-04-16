---
title: Wat is Apache Spark in Azure Synapse Analytics?
description: In dit artikel vindt u een inleiding tot Apache Spark in Azure Synapse Analytics en de verschillende scenario's waarin u Spark gebruiken.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: 4155e419bb03613abad808ca09d84bcb6583291f
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81423647"
---
# <a name="what-is-apache-spark-in-azure-synapse-analytics"></a>Wat is Apache Spark in Azure Synapse Analytics

Apache Spark is een framework voor parallelle verwerking dat ondersteuning biedt voor in-memory verwerking om de prestaties van toepassingen voor de analyse van big data te verbeteren. Apache Spark in Azure Synapse Analytics is een van de implementaties van Apache Spark door Microsoft in de cloud. Azure Synapse maakt het eenvoudig om een Spark-pool (preview) in Azure te maken en te configureren. Spark-pools in Azure Synapse zijn compatibel met Azure Storage en Azure Data Lake Generation 2 Storage. U Azure Spark-pools dus gebruiken om uw gegevens die zijn opgeslagen in Azure te verwerken.

![Spark: een geünificeerd framework](./media/apache-spark-overview/spark-overview.png)

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="what-is-apache-spark"></a>Wat is Apache Spark

Apache Spark biedt primitieven voor clustercomputing in het geheugen. Een Spark-taak kan gegevens laden en in het geheugen cachen en er herhaaldelijk query’s op uitvoeren. In-memory computing is veel sneller dan schijfgebaseerde toepassingen. Spark integreert ook met meerdere programmeertalen om gedistribueerde gegevenssets zoals lokale verzamelingen te manipuleren. Het is niet nodig om alles te structureren als toewijzings- en verminderingsbewerkingen.

![Traditionele kaartReduce vs. Spark](./media/apache-spark-overview/map-reduce-vs-spark.png)

Spark-pools in Azure Synapse bieden een volledig beheerde Spark-service. De voordelen van het maken van een Spark-pool in Synapse Analytics worden hier weergegeven.

| Functie | Beschrijving |
| --- | --- |
| Snelheid en efficiëntie |Spark-exemplaren starten in ongeveer 2 minuten voor minder dan 60 knooppunten en ongeveer 5 minuten voor meer dan 60 knooppunten. De instantie wordt standaard 5 minuten na de laatste taak uitgeschakeld, tenzij deze in leven wordt gehouden door een notebookverbinding. |
| Eenvoudig te maken |U binnen enkele minuten een nieuwe Spark-pool maken in Azure Synapse met de Azure-portal, Azure PowerShell of de Synapse Analytics .NET SDK. Zie [Aan de slag met Spark-pools in Synapse Analytics.](apache-spark-notebook-create-spark-use-sql.md) |
| Gebruiksgemak |Synapse Analytics bevat een aangepaste notebook afkomstig van [Nteract.](https://nteract.io/) U kunt deze notebooks gebruiken voor interactieve gegevensverwerking en visualisatie.|
| REST-API’s |Spark in Synapse Analytics bevat [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), een OP REST API gebaseerde Spark-taakserver om taken op afstand in te dienen en te controleren. |
| Ondersteuning voor Azure Data Lake Storage Generation 2| Spark-pools in Azure Synapse kunnen azure Data Lake Storage Generation 2 en BLOB-opslag gebruiken. Zie [Overzicht van Azure Data Lake Storage](../../data-lake-store/data-lake-store-overview.md) voor meer informatie over Data Lake Storage. |
| Integratie met IDE's van derden | Azure Synapse biedt een IDE-plug-in voor [Jetbrains' IntelliJ IDEA](https://www.jetbrains.com/idea/) die handig is om toepassingen te maken en in te dienen bij een Spark-pool. |
| Vooraf geladen Anaconda-bibliotheken |Spark-pools in Azure Synapse worden geleverd met Anaconda-bibliotheken die vooraf zijn geïnstalleerd. [Anaconda](https://docs.continuum.io/anaconda/) voorziet in bijna 200 bibliotheken voor machine learning, data-analyse, visualisatie, enzovoort. |
| Schaalbaarheid | Apache Spark in Azure Synapse-pools kan Automatisch schalen ingeschakeld hebben, zodat pools naar behoefte op en neer worden geschaald. Spark-pools kunnen ook worden afgesloten zonder verlies van gegevens, omdat alle gegevens zijn opgeslagen in Azure Storage of Data Lake Storage. |

Spark-pools in Azure Synapse bevatten standaard de volgende componenten die beschikbaar zijn op de pools.

- [Spark Core](https://spark.apache.org/docs/latest/). Inclusief Spark Core, Spark SQL, GraphX en MLlib.
- [Anaconda](https://docs.continuum.io/anaconda/)
- [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
- [Nteract-notitieblok](https://nteract.io/)

## <a name="spark-pool-architecture"></a>Spark-poolarchitectuur

Het is gemakkelijk om de componenten van Spark te begrijpen door te begrijpen hoe Spark draait op Synapse Analytics.

Spark-toepassingen worden uitgevoerd als onafhankelijke reeksen processen op een pool, gecoördineerd door het SparkContext-object in uw hoofdprogramma (het stuurprogrammaprogramma genoemd).

De SparkContext kan verbinding maken met de clusterbeheerder, die resources toewijst voor verschillende toepassingen. De clustermanager is [Apache Hadoop YARN](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Eenmaal aangesloten, verwerft Spark uitvoerders op knooppunten in de groep, die processen zijn die berekeningen uitvoeren en gegevens opslaan voor uw toepassing. Vervolgens wordt de code van uw toepassing (gedefinieerd in JAR- of Python-bestanden die worden doorgegeven aan SparkContext) verstuurd naar de executor. Ten slotte verzendt SparkContext taken naar de executors om uit te voeren.

De SparkContext voert de hoofdfunctie van de gebruiker uit en voert de verschillende parallelle bewerkingen op de knooppunten uit. Vervolgens verzamelt SparkContext de resultaten van de bewerkingen. De knooppunten lezen en schrijven gegevens van en naar het bestandssysteem. De knooppunten cache ook getransformeerde gegevens in het geheugen als Resilient Distributed Datasets (RDD's).

De SparkContext maakt verbinding met de Spark-pool en is verantwoordelijk voor het converteren van een toepassing naar een gerichte grafiek (DAG). De grafiek bestaat uit afzonderlijke taken die worden uitgevoerd binnen een executorproces op de knooppunten. Elke toepassing krijgt zijn eigen executorprocessen, die voor de duur van de gehele toepassing blijven en taken uitvoeren in meerdere threads.

## <a name="apache-spark-in-synapse-analytics-use-cases"></a>Apache Spark in Use cases van Synapse Analytics

Spark-pools in Synapse Analytics maken de volgende belangrijke scenario's mogelijk:

### <a name="data-engineeringdata-preparation"></a>Data Engineering/Data Preparation

Apache Spark bevat veel taalfuncties ter ondersteuning van de voorbereiding en verwerking van grote hoeveelheden gegevens, zodat deze waardevoller kunnen worden gemaakt en vervolgens kunnen worden verbruikt door andere services binnen Synapse Analytics. Dit is ingeschakeld via meerdere talen (C#, Scala, PySpark, Spark SQL) en meegeleverde bibliotheken voor verwerking en connectiviteit.

### <a name="machine-learning"></a>Machine Learning

Apache Spark wordt geleverd met [MLlib](https://spark.apache.org/mllib/), een machine learning-bibliotheek die is gebouwd bovenop Spark en die u gebruiken vanuit een Spark-pool in Synapse Analytics. Spark-pools in Synapse Analytics bevatten ook Anaconda, een Python-distributie met een verscheidenheid aan pakketten voor data science, waaronder machine learning. In combinatie met ingebouwde ondersteuning voor notebooks hebt u een omgeving voor het maken van machine learning-toepassingen.

## <a name="where-do-i-start"></a>Waar moet ik beginnen

Gebruik de volgende artikelen voor meer informatie over Apache Spark in Synapse Analytics:

- [Snelstart: een Spark-pool maken in Azure Synapse](./apache-spark-notebook-create-spark-use-sql.md)
- [Zelfstudie: Machine learning met Apache Spark](./apache-spark-machine-learning-mllib-notebook.md)
- [Apache Spark officiële documentatie](https://spark.apache.org/docs/latest/)

> [!NOTE]
> Sommige van de officiële Apache Spark-documentatie is gebaseerd op het gebruik van de spark-console, dit is niet beschikbaar op Azure Synapse Spark, gebruik in plaats daarvan de notebook- of IntelliJ-ervaringen

## <a name="next-steps"></a>Volgende stappen

In dit overzicht krijgt u een basiskennis van Apache Spark in Azure Synapse Analytics. Ga naar het volgende artikel voor meer informatie over het maken van een Spark-groep in Azure Synapse Analytics:

- [Een Spark-pool maken in Azure Synapse](./apache-spark-notebook-create-spark-use-sql.md)
