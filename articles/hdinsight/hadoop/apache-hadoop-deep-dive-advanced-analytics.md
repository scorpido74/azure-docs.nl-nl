---
title: Deep dive - geavanceerde analytics - Azure HDInsight
description: Ontdek hoe geavanceerde analyses algoritmen gebruiken om big data te verwerken in Azure HDInsight.
author: ashishthaps
ms.author: ashishth
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive
ms.date: 01/01/2020
ms.openlocfilehash: 6fc6ea9ca0ce3bf82f027b964db94df50f07f2bd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75646520"
---
# <a name="deep-dive---advanced-analytics"></a>Deep dive - geavanceerde analytics

## <a name="what-is-advanced-analytics-for-hdinsight"></a>Wat is geavanceerde analyse voor HDInsight?

HDInsight biedt de mogelijkheid om waardevol inzicht te verkrijgen uit grote hoeveelheden gestructureerde, ongestructureerde en snel bewegende gegevens. Geavanceerde analyses zijn het gebruik van zeer schaalbare architecturen, statistische en machine learning-modellen en intelligente dashboards om u zinvolle inzichten te bieden. Machine learning, of *voorspellende analyses,* maakt gebruik van algoritmen die relaties in uw gegevens identificeren en leren om voorspellingen te doen en uw beslissingen te begeleiden.

## <a name="advanced-analytics-process"></a>Geavanceerd analyseproces

![Geavanceerde analyseprocesstroom](./media/apache-hadoop-deep-dive-advanced-analytics/hdinsight-analytic-process.png)

Nadat u het bedrijfsprobleem hebt geïdentificeerd en bent begonnen met het verzamelen en verwerken van uw gegevens, moet u een model maken dat de vraag vertegenwoordigt die u wilt voorspellen. Uw model gebruikt een of meer machine learning-algoritmen om het type voorspelling te maken dat het beste past bij uw bedrijfsbehoeften.  Het merendeel van uw gegevens moet worden gebruikt om uw model te trainen, met de rest gebruikt om te testen of te evalueren.

Nadat u uw model hebt gemaakt, geladen, getest en geëvalueerd, is de volgende stap het implementeren van uw model, zodat het begint met het geven van antwoorden op uw vragen. De laatste stap is om de prestaties van uw model te controleren en af te stemmen als dat nodig is.

## <a name="common-types-of-algorithms"></a>Veelvoorkomende typen algoritmen

Geavanceerde analyseoplossingen bieden een reeks machine learning-algoritmen. Hier vindt u een overzicht van de categorieën algoritmen en bijbehorende algemene business use cases.

![Samenvattingen van de categorie Machine Learning](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-use-cases.png)

Samen met het selecteren van de best passende algoritme(s), moet u overwegen of u gegevens moet verstrekken voor training. Machine learning-algoritmen zijn als volgt gecategoriseerd:

* Onder toezicht - algoritme moet worden getraind op een set gelabelde gegevens voordat het resultaten kan opleveren
* Semi-supervised - algoritme kan worden uitgebreid met extra doelen door middel van interactieve query door een trainer, die niet beschikbaar waren tijdens de eerste fase van de training
* Onbewaakt - algoritme vereist geen trainingsgegevens
* Versterking - algoritme maakt gebruik van software agents om ideaal gedrag te bepalen binnen een specifieke context (vaak gebruikt in robotica)

| Categorie algoritmes| Gebruiken | Leertype | Algoritmen |
| --- | --- | --- | -- |
| Classificatie | Personen of dingen classificeren in groepen | Onder supervisie | Beslissingsbomen, Logistieke regressie, neurale netwerken |
| Clustering | Een reeks voorbeelden verdelen in homogene groepen | Zonder toezicht | K-middelen clustering |
| Patroondetectie | Frequente associaties in de gegevens identificeren | Zonder toezicht | Association Rules |
| Regressie | Numerieke resultaten voorspellen | Onder supervisie | Lineaire regressie, neurale netwerken |
| Versterking | Bepaal optimaal gedrag voor robots | Versterking | Monte Carlo Simulaties, DeepMind |

## <a name="machine-learning-on-hdinsight"></a>Machine learning op HDInsight

HDInsight heeft verschillende machine learning-opties voor een geavanceerde analyseworkflow:

* Machine Learning en Apache Spark
* R- en ML-services
* Azure Machine Learning en Apache Hive
* Apache Spark en Deep learning

### <a name="machine-learning-and-apache-spark"></a>Machine Learning en Apache Spark

[HDInsight Spark](../spark/apache-spark-overview.md) is een door Azure gehost aanbod van [Apache Spark](https://spark.apache.org/), een uniform, open source, parallel gegevensverwerkingsframework dat in-memory processing gebruikt om Big Data analytics te stimuleren. De Spark processing engine is gebouwd voor snelheid, gebruiksgemak en geavanceerde analyses. Spark's in-memory gedistribueerde rekenmogelijkheden maken het een goede keuze voor de iteratieve algoritmen die worden gebruikt in machine learning en grafiekberekeningen.

Er zijn drie schaalbare machine learning-bibliotheken die algoritmische modelleringsmogelijkheden naar deze gedistribueerde omgeving brengen:

* [**MLlib**](https://spark.apache.org/docs/latest/ml-guide.html) - MLlib bevat de originele API die bovenop Spark RDD's is gebouwd.
* [**SparkML**](https://spark.apache.org/docs/1.2.2/ml-guide.html) - SparkML is een nieuwer pakket dat een API op een hoger niveau biedt die is gebouwd bovenop Spark DataFrames voor het bouwen van ML-pijplijnen.
* [**MMLSpark**](https://github.com/Azure/mmlspark) - De Microsoft Machine Learning-bibliotheek voor Apache Spark (MMLSpark) is ontworpen om gegevenswetenschappers productiever te maken op Spark, om de snelheid van experimenten te verhogen en om geavanceerde machine learning-technieken, waaronder deep learning, te gebruiken op zeer grote datasets. De MMLSpark-bibliotheek vereenvoudigt veelvoorkomende modelleringstaken voor het bouwen van modellen in PySpark.

### <a name="r-and-ml-services"></a>R- en ML-services

Als onderdeel van HDInsight u een HDInsight-cluster maken met [ML Services](../r-server/r-server-overview.md) die klaar zijn voor gebruik met enorme gegevenssets en modellen. Deze nieuwe mogelijkheid biedt gegevenswetenschappers en statistici een vertrouwde R-interface die on-demand kan schalen via HDInsight, zonder de overhead van clustersetup en -onderhoud.

### <a name="azure-machine-learning-and-apache-hive"></a>Azure Machine Learning en Apache Hive

[Azure Machine Learning Studio (klassiek)](https://studio.azureml.net/) biedt tools voor het modelleren van voorspellende analyses en een volledig beheerde service die u gebruiken om uw voorspellende modellen te implementeren als kant-en-klare webservices. Azure Machine Learning biedt tools voor het maken van complete predictive analytics-oplossingen in de cloud om voorspellende modellen snel te maken, te testen, te operationaliseren en te beheren. Kies uit een grote algoritmebibliotheek, gebruik een webstudio voor het bouwen van modellen en implementeer eenvoudig uw model als webservice.

### <a name="apache-spark-and-deep-learning"></a>Apache Spark en Deep learning

[Deep learning](https://www.microsoft.com/research/group/dltc/) is een tak van machine learning die gebruik maakt van *diepe neurale netwerken* (DNNs), geïnspireerd door de biologische processen van het menselijk brein. Veel onderzoekers zien deep learning als een veelbelovende aanpak voor kunstmatige intelligentie. Enkele voorbeelden van deep learning zijn gesproken taalvertalers, beeldherkenningssystemen en machineredeneren. Om zijn eigen werk op het gebied van deep learning te bevorderen, heeft Microsoft de gratis, gebruiksvriendelijke, open-source [Microsoft Cognitive Toolkit](https://www.microsoft.com/en-us/cognitive-toolkit/)ontwikkeld. De toolkit wordt op grote schaal gebruikt door een breed scala aan Microsoft-producten, door bedrijven over de hele wereld met de noodzaak om deep learning op schaal in te zetten, en door studenten die geïnteresseerd zijn in de nieuwste algoritmen en technieken.

## <a name="scenario---score-images-to-identify-patterns-in-urban-development"></a>Scenario - Score beelden om patronen in stedelijke ontwikkeling te identificeren

Laten we eenvoorbeeld bekijken van een geavanceerde machine learning-pijplijn voor geavanceerde analyses met HDInsight.

In dit scenario ziet u hoe DNN's die zijn geproduceerd in een deep learning-framework, Microsoft's Cognitive Toolkit (CNTK), kunnen worden geoperationaliseerd voor het scoren van grote afbeeldingsverzamelingen die zijn opgeslagen in een Azure Blob Storage-account met PySpark op een HDInsight Spark-cluster. Deze benadering wordt toegepast op een gemeenschappelijke DNN use case, luchtfoto classificatie, en kan worden gebruikt om recente patronen in stedelijke ontwikkeling te identificeren.  U gebruikt een vooraf getraind afbeeldingsclassificatiemodel. Het model is vooraf getraind op de [CIFAR-10 dataset](https://www.cs.toronto.edu/~kriz/cifar.html) en is toegepast op 10.000 ingehouden afbeeldingen.

Er zijn drie belangrijke taken in dit geavanceerde analysescenario:

1. Maak een Azure HDInsight Hadoop-cluster met een Apache Spark 2.1.0-distributie.
2. Voer een aangepast script uit om Microsoft Cognitive Toolkit te installeren op alle knooppunten van een Azure HDInsight Spark-cluster.
3. Upload een vooraf gebouwd Jupyter-notitieblok naar uw HDInsight Spark-cluster om een getraind Microsoft Cognitive Toolkit deep learning-model toe te passen op bestanden in een Azure Blob-opslagaccount met behulp van de Spark Python API (PySpark).

In dit voorbeeld wordt gebruik gemaakt van de CIFAR-10-afbeeldingsset die is samengesteld en gedistribueerd door Alex Krizhevsky, Vinod Nair en Geoffrey Hinton. De CIFAR-10 dataset bevat 60.000 32×32 kleurenafbeeldingen die behoren tot 10 wederzijds exclusieve klassen:

![Voorbeeldafbeeldingen van Machine Learning](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-images.png)

Zie Alex Krizhevsky's Learning Multiple [Layers of Features van Tiny Images](https://www.cs.toronto.edu/~kriz/learning-features-2009-TR.pdf)voor meer informatie over de dataset.

De dataset werd verdeeld in een trainingsset van 50.000 afbeeldingen en een testset van 10.000 afbeeldingen. De eerste set werd gebruikt om een twintig-laags-diepe convolutional residuele netwerk (ResNet) model met behulp van Microsoft Cognitive Toolkit trainen door het volgen van [deze tutorial](https://github.com/Microsoft/CNTK/tree/master/Examples/Image/Classification/ResNet) van de Cognitive Toolkit GitHub repository. De overige 10.000 afbeeldingen werden gebruikt voor het testen van de nauwkeurigheid van het model. Dit is waar distributed computing in het spel komt: de taak van pre-processing en het scoren van de beelden is zeer parallelbaar. Met het opgeslagen getrainde model in de hand gebruikten we:

* PySpark om de afbeeldingen en het getrainde model te distribueren naar de werknemersknooppunten van het cluster.
* Python om de afbeeldingen op elk knooppunt van het HDInsight Spark-cluster vooraf te verwerken.
* Cognitieve toolkit om het model te laden en de vooraf verwerkte afbeeldingen op elk knooppunt te scoren.
* Jupyter-laptops om het PySpark-script uit te voeren, de resultaten samen te voegen en [Matplotlib](https://matplotlib.org/) te gebruiken om de modelprestaties te visualiseren.

De volledige voorbewerking/score van de 10.000 afbeeldingen duurt minder dan een minuut op een cluster met 4 werkknooppunten. Het model voorspelt nauwkeurig de labels van ~ 9.100 (91%) Afbeeldingen. Een verwarringsmatrix illustreert de meest voorkomende classificatiefouten. De matrix laat bijvoorbeeld zien dat het verkeerd etiketteren van honden als katten en vice versa vaker voorkomt dan bij andere labelparen.

![Grafiek machine learning-resultaten](./media/apache-hadoop-deep-dive-advanced-analytics/machine-learning-results.png)

### <a name="try-it-out"></a>Probeer het uit!

Volg [deze zelfstudie](../spark/apache-spark-microsoft-cognitive-toolkit.md) om deze oplossing end-to-end te implementeren: stel een HDInsight Spark-cluster in, installeer cognitieve toolkit en voer de Jupyter-notebook uit die 10.000 CIFAR-afbeeldingen scoort.

## <a name="next-steps"></a>Volgende stappen

Apache Hive en Azure Machine Learning

* [Apache Hive en Azure Machine Learning end-to-end](../../machine-learning/team-data-science-process/hive-walkthrough.md)
* [Een Azure HDInsight Hadoop-cluster gebruiken op een gegevensset van 1 TB](../../machine-learning/team-data-science-process/hive-criteo-walkthrough.md)

Apache Spark en MLLib

* [Machine learning met Apache Spark op HDInsight](../../machine-learning/team-data-science-process/spark-overview.md)
* [Apache Spark met Machine Learning: Gebruik Apache Spark in HDInsight voor het analyseren van de temperatuur van gebouwen met behulp van HVAC-gegevens](../spark/apache-spark-ipython-notebook-machine-learning.md)
* [Apache Spark met Machine Learning: Gebruik Apache Spark in HDInsight om de resultaten van voedselinspectie te voorspellen](../spark/apache-spark-machine-learning-mllib-ipython.md)

Deep Learning, Cognitieve Toolkit, en anderen

* [Data Science Azure Virtual Machine](../../machine-learning/data-science-virtual-machine/overview.md)
* [Maak kennis met H2O.ai op Azure HDInsight](https://azure.microsoft.com/blog/introducing-h2o-ai-with-on-azure-hdinsight-to-bring-the-most-robust-ai-platform-for-enterprises/)
