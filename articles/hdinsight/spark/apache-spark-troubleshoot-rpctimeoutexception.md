---
title: RpcTimeoutException voor Apache Spark Thrift-Azure HDInsight
description: Er worden 502-fouten weer gegeven bij het verwerken van grote gegevens sets met behulp van Apache Spark Thrift-server
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: c9e71c745d62432af3c0fe035d28009e3e5be761
ms.sourcegitcommit: 3486e2d4eb02d06475f26fbdc321e8f5090a7fac
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/31/2019
ms.locfileid: "73241028"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Scenario: RpcTimeoutException voor Apache Spark Thrift-server in azure HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van Apache Spark-onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Spark-toepassing mislukt met een `org.apache.spark.rpc.RpcTimeoutException`-uitzonde ring en een bericht: `Futures timed out`, zoals in het volgende voor beeld:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError`-en `overhead limit exceeded` fouten kunnen ook worden weer gegeven in de `sparkthriftdriver.log`, zoals in het volgende voor beeld:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Oorzaak

Deze fouten worden veroorzaakt door een gebrek aan geheugen bronnen tijdens het verwerken van gegevens. Als het proces voor het uitvoeren van Java-garbagecollection wordt gestart, kan dit ertoe leiden dat de Spark-toepassing vastloopt. Query's gaan naar een time-out en stoppen met de verwerking. De `Futures timed out` fout wijst op een cluster onder ernstige stress.

## <a name="resolution"></a>Resolutie

Verg root de cluster grootte door meer werk knooppunten toe te voegen of de geheugen capaciteit van de bestaande cluster knooppunten te verhogen. U kunt ook de gegevens pijplijn aanpassen om de hoeveelheid gegevens die tegelijk worden verwerkt, te verminderen.

De `spark.network.timeout` regelt de time-out voor alle netwerk verbindingen. Als u de time-out van het netwerk verhoogt, kan het langer duren voordat bepaalde kritieke bewerkingen zijn voltooid, maar wordt het probleem niet volledig opgelost.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees voor meer gedetailleerde informatie [hoe u een ondersteunings aanvraag voor Azure maakt](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
