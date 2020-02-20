---
title: Schijf ruimte in azure HDInsight beheren
description: Probleemoplossings stappen en mogelijke oplossingen voor problemen bij interactie met Azure HDInsight-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/17/2020
ms.openlocfilehash: 577bed7ce342be14a50077a3ffd841cd901b5b31
ms.sourcegitcommit: 64def2a06d4004343ec3396e7c600af6af5b12bb
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/19/2020
ms.locfileid: "77473010"
---
# <a name="manage-disk-space-in-azure-hdinsight"></a>Schijf ruimte in azure HDInsight beheren

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="hive-log-configurations"></a>Hive-logboek configuraties

1. Ga in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net`, waarbij `CLUSTERNAME` de naam van uw cluster is.

1. Navigeer naar **hive** > **configs** > **Geavanceerde** > **Geavanceerde Hive-log4j**. Controleer de volgende instellingen:

    * `hive.root.logger=DEBUG,RFA`. Dit is de standaard waarde. Wijzig het [logboek niveau](https://logging.apache.org/log4j/2.x/log4j-api/apidocs/org/apache/logging/log4j/Level.html) in `INFO` om minder logboek vermeldingen af te drukken.

    * `log4jhive.log.maxfilesize=1024MB`. Dit is de standaard waarde. Wijzig desgewenst.

    * `log4jhive.log.maxbackupindex=10`. Dit is de standaard waarde. Wijzig desgewenst. Als de para meter is wegge laten, zullen de gegenereerde logboek bestanden eindeloos zijn.

## <a name="yarn-log-configurations"></a>Logboek configuraties voor garen

Bekijk de volgende configuraties:

* Apache Ambari

    1. Ga in een webbrowser naar `https://CLUSTERNAME.azurehdinsight.net`, waarbij `CLUSTERNAME` de naam van uw cluster is.

    1. Navigeer naar **Hive** - > **configuratie** > **Advanced** > **Resource Manager**. Zorg ervoor dat **logboek aggregatie inschakelen** is ingeschakeld. Als deze functie is uitgeschakeld, worden de logboeken lokaal door naam knooppunten bewaard en worden ze niet geaggregeerd in externe opslag wanneer de toepassing wordt voltooid of beëindigd.

* Zorg ervoor dat de clustergrootte geschikt is voor de werkbelasting. De werk belasting is mogelijk onlangs gewijzigd of het cluster is verg root of verkleind. [Schaal](../hdinsight-scaling-best-practices.md) het cluster omhoog zodat dit overeenkomt met een hogere werk belasting.

* `/mnt/resource` kan worden gevuld met zwevende bestanden (zoals in het geval van opnieuw opstarten van de Resource Manager). `/mnt/resource/hadoop/yarn/log` en `/mnt/resource/hadoop/yarn/local`, indien nodig, hand matig opschonen.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring. Verbinding maken met de Azure-community met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees [hoe u een ondersteunings aanvraag voor Azure kunt maken](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request)voor meer informatie. De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
