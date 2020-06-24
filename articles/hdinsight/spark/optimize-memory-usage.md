---
title: Het geheugen gebruik optimaliseren in Apache Spark-Azure HDInsight
description: Meer informatie over het optimaliseren van geheugen gebruik in Apache Spark in azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 06bb8f2005dae9e36fe55547a31c81c36d0c7068
ms.sourcegitcommit: c4ad4ba9c9aaed81dfab9ca2cc744930abd91298
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 06/12/2020
ms.locfileid: "84737645"
---
# <a name="memory-usage-optimization-for-apache-spark"></a>Optimalisatie van geheugen gebruik voor Apache Spark

In dit artikel wordt beschreven hoe u het geheugen beheer van uw Apache Spark cluster optimaliseert voor de beste prestaties op Azure HDInsight.

## <a name="overview"></a>Overzicht

Spark werkt door gegevens in het geheugen te plaatsen. Het beheren van geheugen bronnen is dus een belang rijk aspect van het optimaliseren van de uitvoering van Spark-taken.  Er zijn verschillende technieken die u kunt Toep assen om het geheugen van uw cluster efficiënt te gebruiken.

* Geniet de voor keur van kleinere gegevens partities en een account voor gegevens grootte, typen en distributie in uw strategie voor partitioneren.
* Overweeg de nieuwere, efficiënter [`Kryo data serialization`](https://github.com/EsotericSoftware/kryo) , in plaats van de standaard-Java-serialisatie.
* Geniet van de voor keur aan GARENs, omdat deze `spark-submit` door batch worden gescheiden.
* Spark-configuratie-instellingen bewaken en afstemmen.

Ter referentie worden de Spark-geheugen structuur en enkele para meters voor de door Voer van de sleutel in de volgende afbeelding weer gegeven.

## <a name="spark-memory-considerations"></a>Overwegingen voor Spark-geheugen

Als u Apache Hadoop GARENs gebruikt, beheert garen het geheugen dat door alle containers op elk Spark-knoop punt wordt gebruikt.  In het volgende diagram ziet u de belangrijkste objecten en hun relaties.

![Vonk geheugen beheer van garen](./media/apache-spark-perf/apache-yarn-spark-memory.png)

Probeer het volgende om berichten over onvoldoende geheugen te adresseren:

* DAG beheer in wille keurige volg orde controleren. Verlaag met rebuising aan de kaart zijde, pre-Partition (of buckets) bron gegevens, Maximaliseer enkele wille keurige volg orde en verminder de hoeveelheid verzonden gegevens.
* `ReduceByKey`De voor keur met de vaste geheugen limiet tot `GroupByKey` , die aggregaties, windowing en andere functies biedt, maar wel een niet-gebonden geheugen limiet heeft.
* `TreeReduce`Voor keur, waarmee u meer werk kunt doen aan de uitvoerders of partities, aan `Reduce` , dat alles op het stuur programma werkt.
* Gebruik DataFrames in plaats van RDD-objecten op lagere niveaus.
* Maak toe die acties inkapselen, zoals ' top N ', verschillende aggregaties of venster bewerkingen.

Zie [OutOfMemoryError-uitzonde ringen voor Apache Spark in azure HDInsight](apache-spark-troubleshoot-outofmemory.md)voor meer probleemoplossings stappen.

## <a name="next-steps"></a>Volgende stappen

* [Gegevens verwerking voor Apache Spark optimaliseren](optimize-cluster-configuration.md)
* [Gegevens opslag voor Apache Spark optimaliseren](optimize-data-storage.md)
* [Cluster configuratie voor Apache Spark optimaliseren](optimize-cluster-configuration.md)
