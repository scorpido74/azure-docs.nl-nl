---
title: Schijfruimte beheren in Azure HDInsight
description: Problemen oplossen met stappen en mogelijke oplossingen voor problemen bij interactie met Azure HDInsight-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 577bed7ce342be14a50077a3ffd841cd901b5b31
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77473010"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>Schijfruimte beheren in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij interactie met Azure HDInsight-clusters.

## <a name="hive-log-configurations"></a>Hive log configuraties

1. Navigeer vanuit een webbrowser `https://CLUSTERNAME.azurehdinsight.net`naar `CLUSTERNAME` , waar is de naam van uw cluster.

1. Navigeer naar **Hive** > **Configs** > **Advanced** > **Advanced hive-log4j**. Bekijk de volgende instellingen:

    * `hive.root.logger=DEBUG,RFA`. Dit is de standaardwaarde, wijzig `INFO` het [logboekniveau](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html) om minder logboeken items af te drukken.

    * `log4jhive.log.maxfilesize=1024MB`. Dit is de standaardwaarde, wijzigen zoals gewenst.

    * `log4jhive.log.maxbackupindex=10`. Dit is de standaardwaarde, wijzigen zoals gewenst. Als de parameter is weggelaten, zijn de gegenereerde logboekbestanden eindeloos.

## <a name="yarn-log-configurations"></a>Garenlogboekconfiguraties

Bekijk de volgende configuraties:

* Apache Ambari

    1. Navigeer vanuit een webbrowser `https://CLUSTERNAME.azurehdinsight.net`naar `CLUSTERNAME` , waar is de naam van uw cluster.

    1. Navigeer naar **Hive** > **Configs** > **Advanced** > Resource**Manager**. Zorg ervoor **dat Logboekaggregatie inschakelen** is ingeschakeld. Als deze is uitgeschakeld, houden naamknooppunten de logboeken lokaal bij en worden ze niet samengevoegd in het externe archief bij het voltooien of beëindigen van de toepassing.

* Zorg ervoor dat de clustergrootte geschikt is voor de werkbelasting. De werkbelasting is mogelijk onlangs gewijzigd of het cluster is mogelijk aangepast. [Schaal](../hdinsight-scaling-best-practices.md) het cluster op om een hogere werkbelasting aan te passen.

* `/mnt/resource`kan worden gevuld met verweesde bestanden (zoals in het geval van resource manager herstart). Indien nodig handmatig `/mnt/resource/hadoop/yarn/log` reinigen `/mnt/resource/hadoop/yarn/local`en .

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
