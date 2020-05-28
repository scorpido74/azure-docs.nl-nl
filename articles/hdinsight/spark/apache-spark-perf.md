---
title: Spark-taken optimaliseren voor prestaties-Azure HDInsight
description: Algemene strategieën weer geven voor de beste prestaties van Apache Spark clusters in azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: 0b152f7882c7d7a3bab762253da0febc0257ceae
ms.sourcegitcommit: 6a9f01bbef4b442d474747773b2ae6ce7c428c1f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/27/2020
ms.locfileid: "84117965"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Apache Spark-taken in HDInsight optimaliseren

Dit artikel bevat een overzicht van strategieën voor het optimaliseren van Apache Spark taken in azure HDInsight.

## <a name="overview"></a>Overzicht

De prestaties van uw Apache Spark-taken zijn afhankelijk van meerdere factoren. Deze prestatie factoren zijn onder andere: hoe uw gegevens worden opgeslagen, hoe het cluster is geconfigureerd en welke bewerkingen worden gebruikt bij het verwerken van de gegevens.

Veelvoorkomende problemen zijn onder andere: geheugen beperkingen vanwege onjuist gemaate uitvoeringen, langlopende bewerkingen en taken die leiden tot Cartesische bewerkingen.

Er zijn ook veel optimalisaties die u kunnen helpen bij het oplossen van deze uitdagingen, zoals caching, en het toestaan van gegevens scheefheid.

In elk van de volgende artikelen vindt u informatie over verschillende aspecten van Spark-optimalisatie.

* [Gegevens opslag voor Apache Spark optimaliseren](optimize-data-storage.md)
* [Gegevens verwerking voor Apache Spark optimaliseren](optimize-data-processing.md)
* [Geheugen gebruik optimaliseren voor Apache Spark](optimize-memory-usage.md)
* [HDInsight-cluster configuratie optimaliseren voor Apache Spark](optimize-cluster-configuration.md)

## <a name="next-steps"></a>Volgende stappen

* [Fouten opsporen in Apache Spark-taken die worden uitgevoerd in Azure HDInsight](apache-spark-job-debugging.md)
* [Resources voor een Apache Spark cluster in HDInsight beheren](apache-spark-resource-manager.md)
* [Apache Spark-instellingen configureren](apache-spark-settings.md)
