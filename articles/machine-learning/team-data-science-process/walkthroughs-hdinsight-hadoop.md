---
title: Analytics op Azure HDInsight Hadoop met Hive - Team Data Science Process
description: Voorbeelden van het Team Data Science-proces dat door het gebruik van Hive op Azure HDInsight Hadoop loopt om voorspellende analyses uit te doen.
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
ms.openlocfilehash: f72ea6ed5f0eec076d181ef56c99c4f1308a7741
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75864159"
---
# <a name="hdinsight-hadoop-data-science-walkthroughs-using-hive-on-azure"></a>HDInsight Hadoop data science walkthroughs met Hive op Azure 

Deze walkthroughs gebruiken Hive met een HDInsight Hadoop-cluster om voorspellende analyses uit te doen. Ze volgen de stappen die zijn beschreven in het Team Data Science Process. Zie Data Science Process voor een overzicht van het Team Data Science [Process.](overview.md) Zie [Inleiding tot Azure HDInsight, de Hadoop-technologiestack en Hadoop-clusters](../../hdinsight/hadoop/apache-hadoop-introduction.md)voor een inleiding tot Azure HDInsight.

Aanvullende data science walkthroughs die het Team Data Science Process uitvoeren, worden gegroepeerd door het **platform** dat ze gebruiken. Zie [Walkthroughs die het Team Data Science-proces uitvoeren](walkthroughs.md) voor een itemisering van deze voorbeelden.


## <a name="predict-taxi-tips-using-hive-with-hdinsight-hadoop"></a>Voorspel taxitips met Hive met HDInsight Hadoop

De [Use HDInsight Hadoop clusters](hive-walkthrough.md) walkthrough maakt gebruik van gegevens van New York taxi's om te voorspellen: 

- Of een fooi wordt betaald 
- De verdeling van de tipbedragen

Het scenario wordt geïmplementeerd met Hive met een [Azure HDInsight Hadoop-cluster.](https://azure.microsoft.com/services/hdinsight/) Je leert hoe je gegevens van een openbaar toegankelijke NYC-taxirit en tariefgegevensset opslaan, verkennen en functies. U gebruikt Azure Machine Learning ook om de modellen te bouwen en te implementeren.

## <a name="predict-advertisement-clicks-using-hive-with-hdinsight-hadoop"></a>Voorspel advertentieklikken met Hive met HDInsight Hadoop

De [walkthrough Azure HDInsight Hadoop Clusters op een 1 TB-gegevensset](hive-criteo-walkthrough.md) maakt gebruik van een openbaar beschikbare [Criteo-klikgegevensset](https://labs.criteo.com/downloads/download-terabyte-click-logs/) om te voorspellen of een tip wordt betaald en de verwachte bedragen. Het scenario wordt geïmplementeerd met Hive met een [Azure HDInsight Hadoop-cluster](https://azure.microsoft.com/services/hdinsight/) om voorbeeldgegevens op te slaan, te verkennen, te implementeren en omlaag te brengen. Het maakt gebruik van Azure Machine Learning om een binair classificatiemodel te bouwen, te trainen en te scoren en te voorspellen of een gebruiker op een advertentie klikt. De walkthrough eindigt laten zien hoe je een van deze modellen te publiceren als een webservice.


## <a name="next-steps"></a>Volgende stappen

Zie [Team Data Science Process overzicht](overview.md)voor een bespreking van de belangrijkste onderdelen van het Team Data Science Process.

Zie De levenscyclus van het [Team Data Science Process](lifecycle.md)voor een discussie over de levenscyclus van het Team Data Science Process die u gebruiken om uw data science-projecten te structureren. De levenscyclus geeft een overzicht van de stappen, van begin tot eind, die projecten meestal volgen wanneer ze worden uitgevoerd. 

