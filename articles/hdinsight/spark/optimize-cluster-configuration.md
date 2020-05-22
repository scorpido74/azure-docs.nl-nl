---
title: Apache Spark cluster configuratie optimaliseren-Azure HDInsight
description: Meer informatie over het configureren van uw Apache Spark-cluster om de door Voer in azure HDInsight te maximaliseren.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/20/2020
ms.openlocfilehash: 4227f80b9ac153aee72c518bf6f93efdce7234d2
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83791083"
---
# <a name="cluster-configuration-optimization"></a>Optimalisatie van cluster configuratie

In dit artikel wordt beschreven hoe u de configuratie van uw Apache Spark-cluster optimaliseert voor de beste prestaties op Azure HDInsight.

## <a name="overview"></a>Overzicht

Afhankelijk van de werk belasting van het Spark-cluster kunt u bepalen dat een niet-standaard Spark-configuratie zou leiden tot een meer geoptimaliseerde Spark-taak uitvoering.  Voer Bench Mark-tests uit met voorbeeld werkbelastingen om niet-standaard cluster configuraties te valideren.

Hier volgen enkele algemene para meters die u kunt aanpassen:

|Parameter |Beschrijving |
|---|---|
|--num-uitvoerende modules|Hiermee stelt u het juiste aantal uitvoerender in.|
|--uitvoerder-kernen|Hiermee stelt u het aantal kernen in voor elke uitvoerder. Normaal gesp roken moet u middelste uitvoerings modules hebben, aangezien andere processen een deel van het beschik bare geheugen verbruiken.|
|--uitvoerbaar geheugen|Hiermee stelt u de grootte van het geheugen voor elke uitvoerder, waarmee de heapgrootte op GARENs wordt beheerd. Zorg dat er voldoende geheugen beschikbaar is voor de overhead van de uitvoering.|

## <a name="select-the-correct-executor-size"></a>Selecteer de juiste grootte voor de uitvoerder

Houd bij het bepalen van de configuratie van de uitvoerder rekening met de overhead van de Java garbage collection (GC).

* Factoren om de uitvoerings grootte te verkleinen:
    1. Verminder de grootte van de heap onder 32 GB om de GC-overhead < 10% te houden.
    2. Verminder het aantal kernen om de GC-overhead < 10% te houden.

* Factoren voor het verg Roten van de uitvoerder grootte:
    1. Verminder de communicatie overhead tussen uitvoerender.
    2. Verminder het aantal openstaande verbindingen tussen uitvoerende (N2) op grotere clusters (>100-uitvoerders).
    3. Verg root de Heap-grootte zodat deze geschikt is voor geheugenintensieve taken.
    4. Optioneel: de overhead per uitvoerder geheugen verlagen.
    5. Optioneel: gebruik en gelijktijdigheid verhogen door de CPU te verlengen.

Als algemene regel wordt bij het selecteren van de grootte van de uitvoerder:

1. Begin met 30 GB per uitvoerder en distribueer beschik bare machine kernen.
2. Verhoog het aantal uitvoer kernen voor grotere clusters (> 100-uitvoerendeers).
3. Wijzig grootte op basis van de test uitvoeringen en op de voor gaande factoren zoals GC-overhead.

Bij het uitvoeren van gelijktijdige query's moet u rekening houden met het volgende:

1. Begin met 30 GB per uitvoerder en alle machine kernen.
2. Maak meerdere parallelle Spark-toepassingen door de CPU (circa 30% latentie verbetering) te vervolledigen.
3. Verdeel query's over parallelle toepassingen.
4. Wijzig grootte op basis van de test uitvoeringen en op de voor gaande factoren zoals GC-overhead.

Zie [Apache Spark Settings-Spark-uitvoerende modules](apache-spark-settings.md#configuring-spark-executors)voor meer informatie over het gebruik van Ambari voor het configureren van uitvoerender.

Bewaak de query prestaties voor uitbijters of andere prestatie problemen door te kijken naar de tijdlijn weergave. Ook SQL-grafiek, taak statistieken, enzovoort. Zie [Debug Apache Spark jobs die worden uitgevoerd op Azure HDInsight](apache-spark-job-debugging.md)voor informatie over het opsporen van fouten in Spark-taken met garens en de Spark-geschiedenis server. Voor tips over het gebruik van een garen tijdlijn Server raadpleegt u [toegang Apache Hadoop toepassings logboeken van garens](../hdinsight-hadoop-access-yarn-app-logs-linux.md).

Soms zijn een of enkele van de uitvoerende partijen trager dan de andere, en duren taken veel langer om uit te voeren. Deze traagheid gebeurt vaak op grotere clusters (> 30 knoop punten). In dit geval moet u het werk delen in een groter aantal taken zodat de planner de taak kan compenseren voor trage taken. Stel bijvoorbeeld ten minste twee taken uit als het aantal uitvoer kernen in de toepassing. U kunt ook speculatieve uitvoering van taken inschakelen met `conf: spark.speculation = true` .

## <a name="next-steps"></a>Volgende stappen

* [Gegevens verwerking voor Apache Spark optimaliseren](optimize-cluster-configuration.md)
* [Gegevens opslag voor Apache Spark optimaliseren](optimize-data-storage.md)
* [Geheugen gebruik optimaliseren voor Apache Spark](optimize-memory-usage.md)