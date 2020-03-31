---
title: Analytics over HDInsight Spark met PySpark, Scala - Team Data Science Process
description: Voorbeelden van het Team Data Science-proces dat het gebruik van PySpark en Scala op een Azure HDInsight Spark doorloopt.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864142"
---
# <a name="hdinsight-spark-data-science-walkthroughs-using-pyspark-and-scala-on-azure"></a>HDInsight Spark data science walkthroughs met PySpark en Scala op Azure

Deze walkthroughs gebruiken PySpark en Scala op een Azure Spark-cluster om voorspellende analyses uit te doen. Ze volgen de stappen die zijn beschreven in het Team Data Science Process. Zie Data Science Process voor een overzicht van het Team Data Science [Process.](overview.md) Zie Inleiding tot Vonk op HDInsight voor een overzicht van Spark [op HDInsight.](../../hdinsight/spark/apache-spark-overview.md)

Aanvullende data science walkthroughs die het Team Data Science Process uitvoeren, worden gegroepeerd door het **platform** dat ze gebruiken. Zie [Walkthroughs die het Team Data Science-proces uitvoeren](walkthroughs.md) voor een itemisering van deze voorbeelden.

## <a name="predict-taxi-tips-using-pyspark-on-azure-spark"></a>Voorspel taxitips met PySpark op Azure Spark

Met behulp van new York taxigegevens voorspelt de walkthrough [Spark gebruiken op Azure HDInsight](spark-overview.md) of er een fooi wordt betaald en het bereik van de verwachte bedragen. In dit voorbeeld wordt het Team Data Science-proces gebruikt in een scenario met behulp van een [Azure HDInsight Spark-cluster](https://azure.microsoft.com/services/hdinsight/) om gegevens van de openbaar beschikbare NYC-taxirit en tariefgegevensset op te slaan, te verkennen en te beheren. Dit overzichtsonderwerp maakt gebruik van een HDInsight Spark-cluster en Jupyter PySpark-notitieblokken. Deze notitieblokken laten u zien hoe u uw gegevens verkennen en vervolgens hoe u modellen maken en consumeren. De geavanceerde gegevens exploratie en modellering notebook laat zien hoe cross-validatie, hyper-parameter vegen, en model evaluatie op te nemen.

### <a name="data-exploration-and-modeling-with-spark"></a>Gegevensverkenning en modellering met Spark 
Verken de gegevensset en maak, beoordeel en evalueer de machine learning-modellen door te werken via de [binaire classificatie- en regressiemodellen maken voor gegevens met het Spark MLlib-toolkitonderwerp.](spark-data-exploration-modeling.md)

### <a name="model-consumption"></a>Modelverbruik
Zie [Spark-built machine learning-modellen voor](spark-model-consumption.md)meer informatie over het scoren van de classificatie- en regressiemodellen die in dit onderwerp zijn gemaakt.

### <a name="cross-validation-and-hyperparameter-sweeping"></a>Cross-validatie en hyperparameter vegen
Zie [Geavanceerde gegevensverkenning en modellering met Spark](spark-advanced-data-exploration-modeling.md) over hoe modellen kunnen worden getraind met behulp van cross-validatie en hyperparametervegen.


## <a name="predict-taxi-tips-using-scala-on-azure-spark"></a>Voorspel taxitips met Scala op Azure Spark

De [walkthrough Scala gebruiken met Spark op Azure](scala-walkthrough.md) voorspelt of er een fooi wordt betaald en of er een bedrag wordt betaald dat naar verwachting zal worden betaald. Het laat zien hoe scala te gebruiken voor onder toezicht staande machine learning-taken met de Spark machine learning-bibliotheek (MLlib) en SparkML-pakketten op een Azure HDInsight Spark-cluster. Het leidt u door de taken die het [Data Science Process](https://docs.microsoft.com/azure/machine-learning/team-data-science-process/)vormen: gegevensopname en -exploratie, visualisatie, functieengineering, modellering en modelverbruik. De modellen gebouwd omvatten logistieke en lineaire regressie, willekeurige bossen, en gradiënt gestimuleerd bomen.


## <a name="next-steps"></a>Volgende stappen

Zie Team Data Science Process overview voor een overzicht van het Team Data Science Process voor een overzicht van het Team [Data Science Process.](overview.md)

Zie De levenscyclus van het Team [Data Science Process](lifecycle.md)voor een discussie over de levenscyclus van het Team Data Science Process. Deze levenscyclus geeft een overzicht van de stappen, van begin tot eind, die projecten meestal volgen wanneer ze worden uitgevoerd. 

