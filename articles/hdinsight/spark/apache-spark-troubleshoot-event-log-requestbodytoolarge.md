---
title: RequestBodyTooLarge in Logboeken voor Apache Spark streaming-app in azure HDInsight
description: NativeAzureFileSystem ... RequestBodyTooLarge wordt weer gegeven in het logboek voor Apache Spark streaming-app in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.date: 07/29/2019
ms.openlocfilehash: 92e9e41d5829e10bc0130d209249301d78f1b5d3
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 09/06/2019
ms.locfileid: "70736335"
---
# <a name="scenario-nativeazurefilesystem--requestbodytoolarge-appears-in-log-for-apache-spark-streaming-app-in-azure-hdinsight"></a>Scenario: "NativeAzureFileSystem ... RequestBodyTooLarge ' wordt weer gegeven in log for Apache Spark streaming-app in azure HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van Apache Spark-onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

De fout: `NativeAzureFileSystem ... RequestBodyTooLarge` wordt weer gegeven in het stuur programma-logboek voor een Apache Spark streaming-app.

## <a name="cause"></a>Oorzaak

Het Spark-gebeurtenis logboek bestand heeft waarschijnlijk de maximale bestands lengte voor WASB.

In Spark 2,3 genereert elke Spark-app één Spark-gebeurtenis logboek bestand. Het Spark-gebeurtenis logboek bestand voor een Spark-streaming-app blijft groeien terwijl de app wordt uitgevoerd. Een bestand op WASB heeft nu een limiet van 50000 MB en de standaard blok grootte is 4 Mega bytes. In de standaard configuratie is de maximale bestands grootte 195 GB. Azure Storage heeft de maximale blok grootte echter verhoogd tot 100 MB, waardoor de limiet voor één bestand in feite tot 4,75 TB wordt teruggebracht. Zie [Azure Storage schaal baarheid en prestatie doelen](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)voor meer informatie.

## <a name="resolution"></a>Oplossing

Er zijn drie oplossingen beschikbaar voor deze fout:

* Verg root de blok grootte tot 100 MB. Wijzig in de gebruikers interface van Ambari de `fs.azure.write.request.size` eigenschap HDFS Configuration (of `Custom core-site` maak deze in de sectie). Stel de eigenschap in op een hogere waarde, bijvoorbeeld: 33554432. Sla de bijgewerkte configuratie op en start de betrokken onderdelen opnieuw op.

* Stop de taak Spark-streaming regel matig en verzend deze opnieuw.

* Gebruik HDFS om Spark-gebeurtenis logboeken op te slaan. Het gebruik van HDFS voor opslag kan leiden tot verlies van Spark-gebeurtenis gegevens tijdens het schalen van clusters of Azure-upgrades.

    1. Wijzigingen aanbrengen `spark.eventlog.dir` in `spark.history.fs.logDirectory` en via de Ambari-gebruikers interface:

        ```
        spark.eventlog.dir = hdfs://mycluster/hdp/spark2-events
        spark.history.fs.logDirectory = "hdfs://mycluster/hdp/spark2-events"
        ```

    1. Mappen maken op HDFS:

        ```
        hadoop fs -mkdir -p hdfs://mycluster/hdp/spark2-events
        hadoop fs -chown -R spark:hadoop hdfs://mycluster/hdp
        hadoop fs -chmod -R 777 hdfs://mycluster/hdp/spark2-events
        hadoop fs -chmod -R o+t hdfs://mycluster/hdp/spark2-events
        ```

    1. Start alle betrokken services opnieuw via de Ambari-gebruikers interface.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding [@AzureSupport](https://twitter.com/azuresupport) met-het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees voor meer gedetailleerde informatie [hoe u een ondersteunings aanvraag voor Azure maakt](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
