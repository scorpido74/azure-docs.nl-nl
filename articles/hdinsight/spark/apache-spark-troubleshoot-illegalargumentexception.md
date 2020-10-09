---
title: IllegalArgumentException-fout voor Apache Spark-Azure HDInsight
description: IllegalArgumentException voor Apache Spark-activiteit in azure HDInsight voor Azure Data Factory
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: df62dbd8db7d41eb11207c7741aed76cec0ac7a8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75894386"
---
# <a name="scenario-illegalargumentexception-for-apache-spark-activity-in-azure-hdinsight"></a>Scenario: IllegalArgumentException for Apache Spark-activiteit in azure HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van Apache Spark-onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

U ontvangt de volgende uitzonde ring bij het uitvoeren van een Spark-activiteit in een Azure Data Factory-pijp lijn:

```error
Exception in thread "main" java.lang.IllegalArgumentException:
Wrong FS: wasbs://additional@xxx.blob.core.windows.net/spark-examples_2.11-2.1.0.jar, expected: wasbs://wasbsrepro-2017-11-07t00-59-42-722z@xxx.blob.core.windows.net
```

## <a name="cause"></a>Oorzaak

Een Spark-taak mislukt als het jar-bestand zich niet in de standaard-of primaire opslag ruimte van het Spark-cluster bevindt.

Dit is een bekend probleem met het open-source-framework dat wordt bijgehouden in deze bug: [Spark-taak mislukt als FS. defaultFS en Application jar een andere URL zijn](https://issues.apache.org/jira/browse/SPARK-22587).

Dit probleem is opgelost in Spark 2.3.0.

## <a name="resolution"></a>Oplossing

Zorg ervoor dat de toepassing jar is opgeslagen op de standaard/primaire opslag voor het HDInsight-cluster. In het geval van Azure Data Factory moet u ervoor zorgen dat de gekoppelde ADF-service wordt verwezen naar de standaard-HDInsight-container in plaats van een secundaire container.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees voor meer gedetailleerde informatie [hoe u een ondersteunings aanvraag voor Azure maakt](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
