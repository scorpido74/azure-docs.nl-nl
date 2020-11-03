---
title: Slechte prestaties in Apache Hive LLAP query's in azure HDInsight
description: Query's in Apache Hive LLAP worden langzamer uitgevoerd dan verwacht in azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: ee7ce401f889dd9c06b14860f4fc9674c5350b52
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93288878"
---
# <a name="scenario-poor-performance-in-apache-hive-llap-queries-in-azure-hdinsight"></a>Scenario: slechte prestaties in Apache Hive LLAP query's in azure HDInsight

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

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]