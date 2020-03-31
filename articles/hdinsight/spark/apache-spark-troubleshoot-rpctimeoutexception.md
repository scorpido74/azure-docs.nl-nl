---
title: RpcTimeoutException voor Apache Spark zuinigheid - Azure HDInsight
description: U ziet 502 fouten bij het verwerken van grote gegevenssets met apache spark-spaarserver
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: b15ac80295a0113eb0c384e1cc3185f3304c39c6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894282"
---
# <a name="scenario-rpctimeoutexception-for-apache-spark-thrift-server-in-azure-hdinsight"></a>Scenario: RpcTimeoutException for Apache Spark thrift server in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij het gebruik van Apache Spark-componenten in Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Spark-toepassing mislukt `org.apache.spark.rpc.RpcTimeoutException` met een `Futures timed out`uitzondering en een bericht: , zoals in het volgende voorbeeld:

```
org.apache.spark.rpc.RpcTimeoutException: Futures timed out after [120 seconds]. This timeout is controlled by spark.rpc.askTimeout
 at org.apache.spark.rpc.RpcTimeout.org$apache$spark$rpc$RpcTimeout$$createRpcTimeoutException(RpcTimeout.scala:48)
```

`OutOfMemoryError`en `overhead limit exceeded` fouten kunnen ook `sparkthriftdriver.log` worden weergegeven in de zoals in het volgende voorbeeld:

```
WARN  [rpc-server-3-4] server.TransportChannelHandler: Exception in connection from /10.0.0.17:53218
java.lang.OutOfMemoryError: GC overhead limit exceeded
```

## <a name="cause"></a>Oorzaak

Deze fouten worden veroorzaakt door een gebrek aan geheugenbronnen tijdens de gegevensverwerking. Als het proces voor het ophalen van het e-huis op Java wordt gestart, kan dit ertoe leiden dat de Spark-toepassing wordt opgehangen. Query's beginnen een time-out te krijgen en de verwerking te stoppen. De `Futures timed out` fout duidt op een cluster onder zware stress.

## <a name="resolution"></a>Oplossing

Vergroot de clustergrootte door meer werkknooppunten toe te voegen of de geheugencapaciteit van de bestaande clusterknooppunten te vergroten. U ook de gegevenspijplijn aanpassen om de hoeveelheid gegevens die in één keer worden verwerkt te verminderen.

Hiermee `spark.network.timeout` regelt u de time-out voor alle netwerkverbindingen. Als u de time-out van het netwerk verhoogt, kan het mogelijk zijn dat sommige kritieke bewerkingen worden voltooid, maar dit lost het probleem niet volledig op.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie raadpleegt u [Hoe u een Azure-ondersteuningsaanvraag maakt.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
