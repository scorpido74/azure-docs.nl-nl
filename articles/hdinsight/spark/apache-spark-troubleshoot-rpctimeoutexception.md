---
title: RpcTimeoutException voor Apache Spark Thrift-Azure HDInsight
description: Er worden 502-fouten weer gegeven bij het verwerken van grote gegevens sets met behulp van Apache Spark Thrift-server
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: b15ac80295a0113eb0c384e1cc3185f3304c39c6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "75894282"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Scenario: RpcTimeoutException voor Apache Spark Thrift-server in azure HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van Apache Spark-onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Spark-toepassing mislukt met `org.apache.spark.rpc.RpcTimeoutException` een uitzonde ring en `Futures timed out`een bericht:, zoals in het volgende voor beeld:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError`en `overhead limit exceeded` fouten kunnen ook worden weer gegeven `sparkthriftdriver.log` in de as in het volgende voor beeld:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Oorzaak

Deze fouten worden veroorzaakt door een gebrek aan geheugen bronnen tijdens het verwerken van gegevens. Als het proces voor het uitvoeren van Java-garbagecollection wordt gestart, kan dit ertoe leiden dat de Spark-toepassing vastloopt. Query's gaan naar een time-out en stoppen met de verwerking. De `Futures timed out` fout wijst op een cluster onder ernstige stress.

## <a name="resolution"></a>Oplossing

Verg root de cluster grootte door meer werk knooppunten toe te voegen of de geheugen capaciteit van de bestaande cluster knooppunten te verhogen. U kunt ook de gegevens pijplijn aanpassen om de hoeveelheid gegevens die tegelijk worden verwerkt, te verminderen.

`spark.network.timeout` Hiermee bepaalt u de time-out voor alle netwerk verbindingen. Als u de time-out van het netwerk verhoogt, kan het langer duren voordat bepaalde kritieke bewerkingen zijn voltooid, maar wordt het probleem niet volledig opgelost.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding [@AzureSupport](https://twitter.com/azuresupport) met-het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees voor meer gedetailleerde informatie [hoe u een ondersteunings aanvraag voor Azure maakt](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
