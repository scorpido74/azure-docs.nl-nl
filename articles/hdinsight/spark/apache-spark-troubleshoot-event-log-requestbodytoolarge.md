---
title: RequestBodyTooLarge-fout van Apache Spark app-Azure HDInsight
description: NativeAzureFileSystem ... RequestBodyTooLarge wordt weer gegeven in het logboek voor Apache Spark streaming-app in azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 114f710c9d0e85ecde4ab163401c714c5e28a708
ms.sourcegitcommit: aee08b05a4e72b192a6e62a8fb581a7b08b9c02a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/09/2020
ms.locfileid: "75771595"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativeAzureFileSystem... RequestBodyTooLarge ' worden weer gegeven in Apache Spark streaming-app-logboek in HDInsight

In dit artikel worden probleemoplossings stappen en mogelijke oplossingen voor problemen beschreven bij het gebruik van Apache Spark-onderdelen in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

De fout: `NativeAzureFileSystem ... RequestBodyTooLarge` wordt weer gegeven in het stuur programma-logboek voor een Apache Spark streaming-app.

## <a name="cause"></a>Oorzaak

Het Spark-gebeurtenis logboek bestand heeft waarschijnlijk de maximale bestands lengte voor WASB.

In Spark 2,3 genereert elke Spark-app één Spark-gebeurtenis logboek bestand. Het Spark-gebeurtenis logboek bestand voor een Spark-streaming-app blijft groeien terwijl de app wordt uitgevoerd. Een bestand op WASB heeft nu een limiet van 50000 MB en de standaard blok grootte is 4 Mega bytes. In de standaard configuratie is de maximale bestands grootte 195 GB. Azure Storage heeft de maximale blok grootte echter verhoogd tot 100 MB, waardoor de limiet voor de afzonderlijke bestanden effectief wordt ingesteld op 4,75 TB. Zie [schaalbaarheids-en prestatie doelen voor Blob Storage](../../storage/blobs/scalability-targets.md)voor meer informatie.

## <a name="resolution"></a>Resolutie

Er zijn drie oplossingen beschikbaar voor deze fout:

* Verg root de blok grootte tot 100 MB. In de Ambari-gebruikers interface wijzigt u de eigenschap HDFS Configuration `fs.azure.write.request.size` (of maakt u deze in `Custom core-site` sectie). Stel de eigenschap in op een hogere waarde, bijvoorbeeld: 33554432. Sla de bijgewerkte configuratie op en start de betrokken onderdelen opnieuw op.

* Stop de taak Spark-streaming regel matig en verzend deze opnieuw.

* Gebruik HDFS om Spark-gebeurtenis logboeken op te slaan. Het gebruik van HDFS voor opslag kan leiden tot verlies van Spark-gebeurtenis gegevens tijdens het schalen van clusters of Azure-upgrades.

    1. Wijzigingen aanbrengen in `spark.eventlog.dir` en `spark.history.fs.logDirectory` via de Ambari-gebruikers interface:

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

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees voor meer gedetailleerde informatie [hoe u een ondersteunings aanvraag voor Azure maakt](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
