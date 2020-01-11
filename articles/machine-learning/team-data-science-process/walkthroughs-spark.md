---
title: Analyses in HDInsight Spark met PySpark, Scala - Team Data Science Process
description: Voor beelden van het team data Science process dat het gebruik van PySpark en scala op een Azure HDInsight Spark door lopen.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 91aac279a264d64ace5988d147c4caf8c52e9656
ms.sourcegitcommit: 12a26f6682bfd1e264268b5d866547358728cd9a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/10/2020
ms.locfileid: "75864142"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>HDInsight Spark datatechnologische rondleidingen met PySpark en Scala op Azure

Deze scenario's gebruik PySpark en Scala op een Azure Spark-cluster voor voorspellende analyses. Ze stappen de die worden beschreven in het Team Data Science Process. Zie voor een overzicht van het Team Data Science Process [Data Science Process](overview.md). Zie voor een overzicht van Spark in HDInsight [Inleiding tot Spark in HDInsight](../../hdinsight/spark/apache-spark-overview.md).

Aanvullende gegevens datatechnologische rondleidingen die het Team Data Science Process worden uitgevoerd zijn gegroepeerd op de **platform** die ze gebruiken. Zie [scenario's voor het uitvoeren van het Team Data Science Process](walkthroughs.md) voor een specificatie van deze voorbeelden.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Met behulp van de PySpark op Azure Spark taxiritjes voorspellen

Met behulp van de nieuwe taxi-gegevens van de Utrecht [gebruiken in het scenario voor het gebruik van Spark in azure HDInsight](spark-overview.md) of een tip wordt betaald en het bereik van de verwachte bedragen. In dit voor beeld wordt het team data Science-proces in een scenario gebruikt met behulp van een [Azure HDInsight Spark-cluster](https://azure.microsoft.com/services/hdinsight/) om engineergegevens op te slaan, te verkennen en te functie van de openbaar beschik bare NYCe taxi en ritbedrag-gegevensset. In dit overzichts onderwerp wordt gebruikgemaakt van een HDInsight Spark-cluster en Jupyter PySpark-notebooks. Deze laptops ziet u hoe u om uw gegevens te verkennen en vervolgens op over het maken en gebruiken van modellen. De geavanceerde gegevens verkennen en modelleren notebook laat zien hoe het opnemen van kruisvalidatie, hyper-parameter verstrekkende, en model van de evaluatie.

### <a name="data-exploration-and-modeling-with-spark"></a>Met Spark gegevens verkennen en modelleren 
De gegevensset te verkennen en maken, te beoordelen en evalueren van de machine learning-modellen door het uitvoeren van de [binaire classificatie- en regressiemodellen modellen voor gegevens maken in de werkset Spark MLlib](spark-data-exploration-modeling.md) onderwerp.

### <a name="model-consumption"></a>Model-verbruik
Zie voor meer informatie over de classificatie- en regressiemodellen modellen die zijn gemaakt in dit onderwerp te beoordelen, [Score en evalueren met Spark gebouwde machine learning-modellen](spark-model-consumption.md).

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Kruisvalidatie en hyperparameter sweeping
Zie [geavanceerde met Spark gegevens verkennen en modelleren](spark-advanced-data-exploration-modeling.md) op hoe de modellen kunnen worden getraind met behulp van kruisvalidatie en hyper-parameter sweeping.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Met behulp van Scala op Azure Spark taxiritjes voorspellen

Met het scenario [scala gebruiken met Spark in azure wordt voor](scala-walkthrough.md) speld of een tip wordt betaald en het bereik van de bedragen die naar verwachting worden betaald. Deze ziet u hoe u Scala voor beheerde machine learning-taken met de Spark machine learning-bibliotheek (MLlib) en SparkML pakketten op een Azure HDInsight Spark-cluster. Dit helpt u bij de taken die deel uitmaken van de [Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/): gegevensopname en verkennen, visualisatie, feature-engineering, modellen en model verbruik. De gebouwde modellen zijn logistieke en lineaire regressie, willekeurige forests en kleurovergang boosted structuren.


## <a name="next-steps"></a>Volgende stappen

Zie [overzicht van team data Science process](overview.md)voor een overzicht van het team data Science process.

Zie voor een bespreking van de team data Science proces levenscyclus de [team data Science process Lifecycle](lifecycle.md). Deze levens cyclus bevat een overzicht van de stappen, van begin tot eind, die projecten meestal volgen wanneer ze worden uitgevoerd. 

