---
title: Apache Hive verbindingen naar Apache Zookeeper-Azure HDInsight
description: Apache Hive weer gave is niet toegankelijk vanwege Apache Zookeeper-problemen in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/30/2019
ms.openlocfilehash: a6ede13ea258048857e06e1c158a297219ff0df6
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/04/2019
ms.locfileid: "73494274"
---
# <a name="scenario-apache-hive-fails-to-establish-a-connection-to-apache-zookeeper-in-azure-hdinsight"></a>Scenario: Apache Hive kan geen verbinding maken met Apache Zookeeper in azure HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van interactieve query onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

De Hive-weer gave is niet toegankelijk en de logboeken in `/var/log/hive` tonen een fout die er ongeveer als volgt uitziet:

```
ERROR [Curator-Framework-0]: curator.ConnectionState (ConnectionState.java:checkTimeouts(200)) - Connection timed out for connection string (zk0-cluster.cloud.wbmi.com:2181,zk1-cluster.cloud.wbmi.com:2181,zk2-cluster.cloud.wbmi.com:2181) and timeout (15000) / elapsed (21852)
```

## <a name="cause"></a>Oorzaak

Het is mogelijk dat de Hive geen verbinding kan maken met Zookeeper, waardoor de Hive-weer gave niet kan worden gestart.

## <a name="resolution"></a>Resolutie

1. Controleer of de Zookeeper-service in orde is.

1. Controleer of de Zookeeper-service een ZNode-vermelding heeft voor Hive server2. De waarde ontbreekt of is onjuist.

    ```
    /usr/hdp/2.6.2.25-1/zookeeper/bin/zkCli.sh -server zk1-wbwdhs
    [zk: zk0-cluster(CONNECTED) 0] ls /hiveserver2-hive2
    ```

1. Als u opnieuw verbinding wilt maken, start u de Zookeeper-knoop punten opnieuw op en start u HiveServer2 opnieuw op.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Zie [Een ondersteuningsaanvraag maken voor Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) voor meer informatie. Toegang tot abonnementsbeheer en factuurbeheer is in uw Microsoft Azure-abonnement inbegrepen, en technische ondersteuning wordt verstrekt via een van de [Azure-ondersteuningsplannen](https://azure.microsoft.com/support/plans/).
