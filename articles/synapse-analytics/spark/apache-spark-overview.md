---
title: Wat is Apache Spark in azure Synapse Analytics?
description: Dit artikel bevat een inleiding tot Apache Spark in azure Synapse Analytics en de verschillende scenario's waarin u Spark kunt gebruiken.
services: synapse-analytics
author: euangMS
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: ''
ms.date: 04/15/2020
ms.author: euang
ms.reviewer: euang
ms.openlocfilehash: b6af42f09351df60beb2aa449f8fcd2ff6a554e8
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/29/2020
ms.locfileid: "82116652"
---
# <a name="what-is-apache-spark-in-azure-synapse-analytics"></a>Wat is Apache Spark in azure Synapse Analytics

Apache Spark is een framework voor parallelle verwerking dat ondersteuning biedt voor in-memory verwerking om de prestaties van toepassingen voor de analyse van big data te verbeteren. Apache Spark in azure Synapse Analytics is een van de implementaties van Apache Spark van micro soft in de Cloud. Met Azure Synapse kunt u eenvoudig een Spark-groep maken en configureren in Azure. Spark-Pools in azure Synapse zijn compatibel met Azure Storage en Azure Data Lake de generatie 2-opslag. U kunt ook Azure Spark-Pools gebruiken om uw gegevens te verwerken die zijn opgeslagen in Azure.

![Spark: een geünificeerd framework](./media/apache-spark-overview/spark-overview.png)

[!INCLUDE [preview](../includes/note-preview.md)]

## <a name="what-is-apache-spark"></a>Wat is Apache Spark

Apache Spark biedt primitieven voor cluster computing in het geheugen. Een Spark-taak kan gegevens laden en in het geheugen cachen en er herhaaldelijk query’s op uitvoeren. In-Memory computing is veel sneller dan op schijven gebaseerde toepassingen. Spark kan ook worden geïntegreerd met meerdere programmeer talen zodat u gedistribueerde gegevens sets zoals lokale verzamelingen kunt bewerken. Het is niet nodig om alles te structureren als toewijzings- en verminderingsbewerkingen.

![Traditioneel MapReduce vs. Spark](./media/apache-spark-overview/map-reduce-vs-spark.png)

Spark-Pools in azure Synapse bieden een volledig beheerde Spark-service. De voor delen van het maken van een Spark-groep in Synapse Analytics worden hier weer gegeven.

| Functie | Beschrijving |
| --- | --- |
| Snelheid en efficiëntie |Spark-instanties beginnen ongeveer 2 minuten voor minder dan 60 knoop punten en ongeveer 5 minuten voor meer dan 60 knoop punten. Het exemplaar wordt standaard 5 minuten na de laatste taak afgesloten, tenzij het actief wordt gehouden door een notebook-verbinding. |
| Eenvoudig te maken |U kunt binnen enkele minuten een nieuwe Spark-groep in azure Synapse maken met behulp van de Azure Portal, Azure PowerShell of de Synapse Analytics .NET SDK. Zie [aan de slag met Spark-Pools in Synapse Analytics](../quickstart-create-apache-spark-pool.md). |
| Gebruiksgemak |Synapse Analytics bevat een aangepast notebook dat is afgeleid van [Nteract](https://nteract.io/). U kunt deze notebooks gebruiken voor interactieve gegevensverwerking en visualisatie.|
| REST-API’s |Spark in Synapse Analytics bevat [Apache livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server), een op rest API gebaseerde Spark-taak server voor het extern indienen en bewaken van taken. |
| Ondersteuning voor Azure Data Lake Storage-generatie 2| Spark-Pools in azure Synapse kunnen Azure Data Lake Storage Generation 2 en BLOB-opslag gebruiken. Zie [Overzicht van Azure Data Lake Storage](../../data-lake-store/data-lake-store-overview.md) voor meer informatie over Data Lake Storage. |
| Integratie met IDE's van derden | Azure Synapse biedt een IDE-invoeg toepassing voor [JetBrains ' IntelliJ-idee](https://www.jetbrains.com/idea/) dat nuttig is om toepassingen te maken en in te dienen bij een Spark-pool. |
| Vooraf geladen Anaconda-bibliotheken |Spark-Pools in azure Synapse worden geleverd met Anaconda-bibliotheken die vooraf zijn geïnstalleerd. [Anaconda](https://docs.continuum.io/anaconda/) voorziet in bijna 200 bibliotheken voor machine learning, data-analyse, visualisatie, enzovoort. |
| Schaalbaarheid | Voor Apache Spark in azure Synapse-groepen kan automatisch schalen zijn ingeschakeld, zodat Pools naar behoefte omhoog en omlaag worden geschaald. Mousserende groepen kunnen ook zonder verlies van gegevens worden afgesloten, omdat alle gegevens worden opgeslagen in Azure Storage of Data Lake Storage. |

Spark-Pools in azure Synapse bevatten de volgende onderdelen die standaard beschikbaar zijn in de groepen.

- [Spark Core](https://spark.apache.org/docs/latest/). Omvat Spark-kern, Spark SQL, Graphx en MLlib.
- [Anaconda](https://docs.continuum.io/anaconda/)
- [Apache Livy](https://github.com/cloudera/hue/tree/master/apps/spark/java#welcome-to-livy-the-rest-spark-server)
- [Nteract-notebook](https://nteract.io/)

## <a name="spark-pool-architecture"></a>Architectuur van Spark-groep

Het is eenvoudig om inzicht te krijgen in de onderdelen van Spark door te begrijpen hoe Spark wordt uitgevoerd op Synapse Analytics.

Spark-toepassingen worden uitgevoerd als onafhankelijke sets processen in een groep, gecoördineerd door het SparkContext-object in het hoofd programma (het stuur programma genoemd).

De SparkContext kan verbinding maken met cluster beheer, waarmee bronnen worden toegewezen aan verschillende toepassingen. Cluster beheer is [Apache HADOOP garens](https://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html). Zodra de verbinding tot stand is gebracht, worden er door Spark uitvoerende modules opgehaald op knoop punten in de pool. Dit zijn processen waarmee berekeningen worden uitgevoerd en gegevens voor uw toepassing worden opgeslagen. Vervolgens wordt de code van uw toepassing (gedefinieerd in JAR- of Python-bestanden die worden doorgegeven aan SparkContext) verstuurd naar de executor. Ten slotte verzendt SparkContext taken naar de executors om uit te voeren.

De SparkContext voert de hoofd functie van de gebruiker uit en voert de verschillende parallelle bewerkingen uit op de knoop punten. Vervolgens verzamelt SparkContext de resultaten van de bewerkingen. De knoop punten lezen en schrijven gegevens van en naar het bestands systeem. De knoop punten slaan ook getransformeerde gegevens in het geheugen in de cache op als robuuste gedistribueerde data sets (Rdd's).

De SparkContext maakt verbinding met de Spark-groep en is verantwoordelijk voor het converteren van een toepassing naar een gestuurde Graph (DAG). De grafiek bestaat uit afzonderlijke taken die worden uitgevoerd binnen een bezorgings proces op de knoop punten. Elke toepassing krijgt zijn eigen executorprocessen, die voor de duur van de gehele toepassing blijven en taken uitvoeren in meerdere threads.

## <a name="apache-spark-in-synapse-analytics-use-cases"></a>Apache Spark in Synapse Analytics-use cases

Spark-Pools in Synapse Analytics bieden de volgende belang rijke scenario's:

### <a name="data-engineeringdata-preparation"></a>Gegevens techniek/gegevens voorbereiding

Apache Spark bevat veel taal functies voor het ondersteunen van de voor bereiding en verwerking van grote hoeveel heden gegevens, zodat deze waardevoler kunnen zijn en vervolgens kan worden gebruikt door andere services in Synapse Analytics. Dit wordt ingeschakeld via meerdere talen (C#, scala, PySpark, Spark SQL) en de geleverde bibliotheken voor verwerking en connectiviteit.

### <a name="machine-learning"></a>Machine Learning

Apache Spark wordt geleverd met [MLlib](https://spark.apache.org/mllib/), een machine learning bibliotheek die boven op Spark is gebouwd en die u vanuit een Spark-groep in Synapse Analytics kunt gebruiken. Spark-Pools in Synapse Analytics bevatten ook Anaconda, een python-distributie met diverse pakketten voor data technologie, waaronder machine learning. In combi natie met ingebouwde ondersteuning voor notebooks hebt u een omgeving voor het maken van machine learning-toepassingen.

## <a name="where-do-i-start"></a>Waar kan ik beginnen

Gebruik de volgende artikelen voor meer informatie over Apache Spark in Synapse Analytics:

- [Quick Start: een Spark-groep maken in azure Synapse](../quickstart-create-apache-spark-pool.md)
- [Snelstartgids: een Apache Spark notitie blok maken](../quickstart-apache-spark-notebook.md)
- [Zelf studie: machine learning met Apache Spark](./apache-spark-machine-learning-mllib-notebook.md)
- [Apache Spark officiële documentatie](https://spark.apache.org/docs/latest/)

> [!NOTE]
> Sommige van de officiële Apache Spark documentatie is gebaseerd op het gebruik van de Spark-console, maar dit is niet beschikbaar in azure Synapse Spark, gebruik in plaats daarvan de ervaring notebook of IntelliJ

## <a name="next-steps"></a>Volgende stappen

In dit overzicht krijgt u een basis inzicht in Apache Spark in azure Synapse Analytics. Ga naar het volgende artikel voor meer informatie over het maken van een Spark-groep in azure Synapse Analytics:

- [Een Spark-groep maken in azure Synapse](../quickstart-create-apache-spark-pool.md)
