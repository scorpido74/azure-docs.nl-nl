---
title: Spark-taken optimaliseren voor prestaties-Azure HDInsight
description: Algemene strategieën weer geven voor de beste prestaties van Apache Spark clusters in azure HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 05/21/2020
ms.openlocfilehash: f92a351087670ce0b37921a496eabfa883a3b1fc
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83780111"
---
# <a name="optimize-apache-spark-jobs-in-hdinsight"></a>Apache Spark-taken in HDInsight optimaliseren

Dit artikel bevat een overzicht van strategieën voor het optimaliseren van Apache Spark taken in azure HDInsight.

## <a name="overview"></a>Overzicht

De prestaties van uw Apache Spark-taken zijn afhankelijk van meerdere factoren. Deze prestatie factoren zijn onder andere: hoe uw gegevens worden opgeslagen, hoe het cluster is geconfigureerd en welke bewerkingen worden gebruikt bij het verwerken van de gegevens.

Veelvoorkomende uitdagingen die u mogelijk hebt, zijn onder meer geheugen beperkingen vanwege onjuist gemaate uitvoeringen, langlopende bewerkingen en taken die leiden tot Cartesische bewerkingen.

Er zijn ook verschillende strategieën die u kunnen helpen bij het oplossen van deze uitdagingen, zoals caching, en het toestaan van gegevens scheefheid.

In elk van de volgende artikelen vindt u veelvoorkomende uitdagingen en oplossingen voor een ander aspect van Spark-optimalisatie.

* [Gegevens opslag optimaliseren](optimize-data-storage.md)
* [Gegevens verwerking optimaliseren](optimize-data-processing.md)
* [Geheugen gebruik optimaliseren](optimize-memory-usage.md)
* [Cluster configuratie optimaliseren](optimize-cluster-configuration.md)

## <a name="next-steps"></a>Volgende stappen

* [Fouten opsporen in Apache Spark-taken die worden uitgevoerd in Azure HDInsight](apache-spark-job-debugging.md)
* [Resources voor een Apache Spark cluster in HDInsight beheren](apache-spark-resource-manager.md)
* [Apache Spark-instellingen configureren](apache-spark-settings.md)
