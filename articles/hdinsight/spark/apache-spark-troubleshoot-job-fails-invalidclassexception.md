---
title: InvalidClassException-fout van Apache Spark-Azure HDInsight
description: Apache Spark taak mislukt met InvalidClassException, niet-overeenkomende klasse versies in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 124d5586180258589c5db17454b8fbf1e465fc24
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/15/2019
ms.locfileid: "74106484"
---
# <a name="apache-spark-job-fails-with-invalidclassexception-class-version-mismatch-in-azure-hdinsight"></a>Apache Spark taak mislukt met InvalidClassException, niet-overeenkomende klasse versies in azure HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van Apache Spark-onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

U probeert een Apache Spark-taak te maken in een Spark 2. x-cluster. Dit mislukt met een fout die vergelijkbaar is met de volgende:

```
18/09/18 09:32:26 WARN TaskSetManager: Lost task 0.0 in stage 1.0 (TID 1, wn7-dev-co.2zyfbddadfih0xdq0cdja4g.ax.internal.cloudapp.net, executor 4): java.io.InvalidClassException:
org.apache.commons.lang3.time.FastDateFormat; local class incompatible: stream classdesc serialVersionUID = 2, local class serialVersionUID = 1
        at java.io.ObjectStreamClass.initNonProxy(ObjectStreamClass.java:699)
        at java.io.ObjectInputStream.readNonProxyDesc(ObjectInputStream.java:1885)
        at java.io.ObjectInputStream.readClassDesc(ObjectInputStream.java:1751)
        at java.io.ObjectInputStream.readOrdinaryObject(ObjectInputStream.java:2042)
        at java.io.ObjectInputStream.readObject0(ObjectInputStream.java:1573)
```

## <a name="cause"></a>Oorzaak

Deze fout kan worden veroorzaakt door een extra jar toe te voegen aan de `spark.yarn.jars` config, met name een geschakeerd jar met een andere versie van het `commons-lang3`-pakket en introduceert een niet-overeenkomende klasse. Spark 2.1/2/3 maakt standaard gebruik van versie 3,5 van `commons-lang3`.

> [!TIP]
> Als u een bibliotheek wilt arceren, moet u de inhoud ervan in uw eigen jar plaatsen, waardoor het pakket wordt gewijzigd. Dit verschilt van het verpakken van de bibliotheek, die de bibliotheek in uw eigen jar plaatst zonder opnieuw te verpakken.

## <a name="resolution"></a>Oplossing

Verwijder het JAR of compileer het aangepaste JAR opnieuw (AzureLogAppender) en gebruik [maven-Shade-invoeg toepassing](https://maven.apache.org/plugins/maven-shade-plugin/examples/class-relocation.html) om klassen te verplaatsen.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Zie [Een ondersteuningsaanvraag maken voor Azure](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request) voor meer informatie. Toegang tot abonnementsbeheer en factuurbeheer is in uw Microsoft Azure-abonnement inbegrepen, en technische ondersteuning wordt verstrekt via een van de [Azure-ondersteuningsplannen](https://azure.microsoft.com/support/plans/).
