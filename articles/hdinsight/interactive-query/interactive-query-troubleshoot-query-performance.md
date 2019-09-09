---
title: Slechte prestaties in Apache Hive LLAP query's in azure HDInsight
description: Query's in Apache Hive LLAP worden langzamer uitgevoerd dan verwacht in azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/30/2019
ms.openlocfilehash: d05febbe6ba9e30ef46f5e961af155d37c76c205
ms.sourcegitcommit: fa4852cca8644b14ce935674861363613cf4bfdf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/09/2019
ms.locfileid: "70811360"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>Scenario: Slechte prestaties in Apache Hive LLAP query's in azure HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van interactieve query onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

De standaard cluster configuraties zijn niet voldoende afgestemd op uw werk belasting. Query's in Hive LLAP worden langzamer uitgevoerd dan verwacht.

## <a name="cause"></a>Oorzaak

Dit kan worden veroorzaakt door verschillende redenen.

## <a name="resolution"></a>Oplossing

LLAP is geoptimaliseerd voor query's waarbij samen voegingen en aggregaties betrokken zijn. Query's zoals de volgende niet goed pres teren in een cluster met een interactieve Hive:

```
select * from table where column = "columnvalue"
```

Stel de volgende configuraties in om de prestaties van de Point-query in Hive LLAP te verbeteren:

```
hive.llap.io.enabled=false; (disable LLAP IO)
hive.optimize.index.filter=false; (disable ORC row index)
hive.exec.orc.split.strategy=BI; (to avoid recombining splits)
```

U kunt ook het gebruik van de LLAP-cache verhogen om de prestaties te verbeteren met de volgende configuratie wijziging:

```
hive.fetch.task.conversion=none
```

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding [@AzureSupport](https://twitter.com/azuresupport) met-het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees voor meer gedetailleerde informatie [hoe u een ondersteunings aanvraag voor Azure maakt](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
