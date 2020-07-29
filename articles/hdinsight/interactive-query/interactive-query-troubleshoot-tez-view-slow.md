---
title: De Ambari TEZ-weer gave van Apache wordt langzaam in azure HDInsight geladen
description: De Apache Ambari TEZ-weer gave kan langzaam worden geladen of helemaal niet worden geladen in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: f4b1ffbc1e5b8147279d1e0320bd5f55aec90ea2
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 07/02/2020
ms.locfileid: "75895097"
---
# <a name="scenario-apache-ambari-tez-view-loads-slowly-in-azure-hdinsight"></a>Scenario: de Ambari-weer gave van Apache TEZ wordt langzaam in azure HDInsight geladen

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van interactieve query onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

De Apache Ambari TEZ-weer gave kan langzaam worden geladen of helemaal niet worden geladen. Wanneer u de weer gave Ambari TEZ laadt, ziet u mogelijk processen op hoofd knooppunten niet meer reageert.

## <a name="cause"></a>Oorzaak

Het openen van garen-Api's van coonderdelen kan soms slechte prestaties hebben voor clusters die zijn gemaakt vóór okt 2017 als het cluster een groot aantal Hive-taken uitvoert.

## <a name="resolution"></a>Oplossing

Dit is een probleem dat is opgelost in OCT 2017. Als u het cluster opnieuw maakt, wordt het niet opnieuw uitgevoerd in dit probleem.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees voor meer gedetailleerde informatie [hoe u een ondersteunings aanvraag voor Azure maakt](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
