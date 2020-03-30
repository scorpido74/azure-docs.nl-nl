---
title: Overzicht van machine learning - Azure HDInsight
description: Overzicht van big data machine learning-opties voor clusters in Azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 12/06/2019
ms.openlocfilehash: 6c7f50e4ba3803b5cb929b75f5ad1127f7f063d9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75931671"
---
# <a name="machine-learning-on-hdinsight"></a>Machine learning op HDInsight

HDInsight maakt machine learning met big data mogelijk en biedt de mogelijkheid om waardevolle inzichten te verkrijgen uit grote hoeveelheden (petabytes, of zelfs exabytes) van gestructureerde, ongestructureerde en snel bewegende gegevens. Er zijn verschillende machine learning-opties in HDInsight: SparkML en Apache Spark MLlib, R, Apache Hive en de Microsoft Cognitive Toolkit.

## <a name="sparkml-and-mllib"></a>SparkML en MLlib

[HDInsight Spark](spark/apache-spark-overview.md) is een door Azure gehost aanbod van [Apache Spark](https://spark.apache.org/), een uniform, open source, parallel dataprocessingframework dat in-memory processing ondersteunt om big data analytics te stimuleren. De Spark processing engine is gebouwd voor snelheid, gebruiksgemak en geavanceerde analyses. Spark's in-memory gedistribueerde rekenmogelijkheden maken het een goede keuze voor de iteratieve algoritmen die worden gebruikt in machine learning en grafiekberekeningen. Er zijn twee schaalbare machine learning-bibliotheken die algoritmische modelleringsmogelijkheden naar deze gedistribueerde omgeving brengen: MLlib en SparkML. MLlib bevat de originele API die bovenop RDD's is gebouwd. SparkML is een nieuwer pakket dat een API op een hoger niveau biedt die is gebouwd bovenop DataFrames voor het bouwen van ML-pijplijnen. SparkML ondersteunt nog niet alle functies van MLlib, maar vervangt MLlib als spark's standaard machine learning-bibliotheek.

De Microsoft Machine Learning-bibliotheek voor Apache Spark is [MMLSpark.](https://github.com/Azure/mmlspark) Deze bibliotheek is ontworpen om gegevenswetenschappers productiever te maken op Spark, de frequentie van experimenten te verhogen en geavanceerde machine learning-technieken, waaronder deep learning, te gebruiken op zeer grote datasets. MMLSpark biedt een laag bovenop de API's op laag niveau van SparkML bij het bouwen van schaalbare ML-modellen, zoals indexeringstekenreeksen, het afzetten van gegevens in een lay-out die wordt verwacht door machine learning-algoritmen en het samenstellen van functievectoren. De MMLSpark-bibliotheek vereenvoudigt deze en andere veelvoorkomende taken voor het bouwen van modellen in PySpark.

## <a name="r"></a>R

[R](https://www.r-project.org/) is momenteel de meest populaire statistische programmeertaal in de wereld. Het is een open-source data visualisatie tool met een gemeenschap van meer dan 2,5 miljoen gebruikers en groeiende. Met zijn bloeiende gebruikersbasis, en meer dan 8.000 bijgedragen pakketten, R is een waarschijnlijke keuze voor veel bedrijven die machine learning nodig hebben. U een HDInsight-cluster maken met ML Services die klaar zijn voor gebruik met enorme gegevenssets en modellen. Deze mogelijkheid biedt gegevenswetenschappers en statistici een vertrouwde R-interface die on-demand kan schalen via HDInsight, zonder de overhead van clustersetup en -onderhoud.

![Training voor voorspelling met R-server](./media/hdinsight-machine-learning-overview/training-for-prediction.png)

Het randknooppunt van een cluster biedt een handige plek om verbinding te maken met het cluster en uw R-scripts uit te voeren.  U ook R-scripts uitvoeren op de knooppunten van het cluster met behulp van scaler's Hadoop Map Reduce of Spark compute-contexten.

Met ML Services op HDInsight met Spark u de training over de knooppunten van een cluster parallellopen met behulp van een Spark-compute-context. U R-scripts direct op het randknooppunt uitvoeren, waarbij alle beschikbare kernen parallel worden gebruikt, indien nodig. U uw code afwisselend vanaf het randknooppunt uitvoeren om de verwerking te starten die wordt verdeeld over alle knooppunten in het cluster. ML Services op HDInsight met Spark maakt het ook mogelijk om functies van open-source R-pakketten te paralleleren, indien gewenst.

## <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning en Apache Hive

Azure Machine Learning biedt tools voor het modelleren van voorspellende analyses en een volledig beheerde service die u gebruiken om uw voorspellende modellen te implementeren als kant-en-klare webservices. Azure Machine Learning is een complete oplossing voor voorspellende analyses in de cloud die u gebruiken om voorspellende modellen te maken, te testen, te operationaliseren en te beheren. Kies uit een grote algoritmebibliotheek, gebruik een webstudio voor het bouwen van modellen en implementeer eenvoudig uw model als webservice.

![Overzicht van Microsoft Azure machine learning](./media/hdinsight-machine-learning-overview/azure-machine-learning.png)

Maak functies voor gegevens in een HDInsight Hadoop-cluster met [Hive-query's.](../machine-learning/team-data-science-process/create-features-hive.md) *Feature engineering* probeert de voorspellende kracht van leeralgoritmen te vergroten door functies te maken van ruwe gegevens die het leerproces vergemakkelijken. U HiveQL-query's uitvoeren vanuit Azure Machine Learning Studio (klassiek) en toegang krijgen tot gegevens die in Hive worden verwerkt en zijn opgeslagen in blob-opslag, met behulp van de [module Gegevens importeren](../machine-learning/studio/import-data.md).

## <a name="microsoft-cognitive-toolkit"></a>Microsoft Cognitive Toolkit

[Deep learning](https://www.microsoft.com/en-us/research/group/dltc/) is een tak van machine learning die gebruik maakt van neurale netwerken, geïnspireerd door de biologische processen van het menselijk brein. Veel onderzoekers zien deep learning als een veelbelovende aanpak voor het verbeteren van kunstmatige intelligentie. Voorbeelden van deep learning zijn gesproken taalvertalers, beeldherkenningssystemen en machineredeneren.

Om zijn eigen werk op het gebied van deep learning te bevorderen, ontwikkelde Microsoft de gratis, gebruiksvriendelijke, open-source [Microsoft Cognitive Toolkit.](https://www.microsoft.com/en-us/cognitive-toolkit/) Deze toolkit wordt gebruikt door een breed scala aan Microsoft-producten, door bedrijven over de hele wereld met de noodzaak om deep learning op schaal te implementeren, en door studenten die geïnteresseerd zijn in de nieuwste algoritmen en technieken.

## <a name="see-also"></a>Zie ook

### <a name="scenarios"></a>Scenario's

* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight voor het analyseren van de temperatuur van gebouwen met behulp van HVAC-gegevens](spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Gebruik Spark in HDInsight om de resultaten van voedselinspectie te voorspellen](spark/apache-spark-machine-learning-mllib-ipython.md)
* [Filmaanbevelingen genereren met Apache Mahout](hadoop/apache-hadoop-mahout-linux-mac.md)
* [Apache Hive en Azure Machine Learning](../machine-learning/team-data-science-process/create-features-hive.md)
* [Apache Hive en Azure Machine Learning end-to-end](../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Machine learning met Apache Spark op HDInsight](../machine-learning/team-data-science-process/spark-overview.md)

### <a name="deep-learning-resources"></a>Deep learning-bronnen

* [Microsoft Cognitive Toolkit deep learning-model gebruiken met Azure HDInsight Spark-cluster](spark/apache-spark-microsoft-cognitive-toolkit.md)
* [Deep Learning en AI frameworks op de Data Science Virtual Machine (DSVM)](../machine-learning/data-science-virtual-machine/dsvm-tools-deep-learning-frameworks.md)
