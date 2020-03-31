---
title: RequestBodyTooLarge error van Apache Spark app - Azure HDInsight
description: NativeAzureFileSystem ... RequestBodyTooLarge verschijnt in log voor Apache Spark streaming app in Azure HDInsight
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 07/29/2019
ms.openlocfilehash: 777d06670238a7625d190c92f78a55cd4794d226
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "75894403"
---
# <a name="nativeazurefilesystemrequestbodytoolarge-appear-in-apache-spark-streaming-app-log-in-hdinsight"></a>"NativeAzureFileSystem... RequestBodyTooLarge" verschijnen in Apache Spark streaming app log in HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen beschreven bij het gebruik van Apache Spark-componenten in Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

De fout: `NativeAzureFileSystem ... RequestBodyTooLarge` wordt weergegeven in het stuurprogrammalogboek voor een Apache Spark-streaming-app.

## <a name="cause"></a>Oorzaak

Uw Spark-gebeurtenislogboekbestand raakt waarschijnlijk de limiet voor de bestandslengte voor WASB.

In Spark 2.3 genereert elke Spark-app één Spark-gebeurtenislogboekbestand. Het spark-gebeurtenislogboekbestand voor een Spark-streaming-app blijft groeien terwijl de app wordt uitgevoerd. Vandaag een bestand op WASB heeft een 50000 blok limiet, en de standaard blok grootte is 4 MB. Dus in standaard configuratie de maximale bestandsgrootte is 195 GB. Azure Storage heeft de maximale blokgrootte echter verhoogd tot 100 MB, waardoor de limiet voor één bestand effectief is opgelopen tot 4,75 TB. Zie [Schaalbaarheids- en prestatiedoelen voor Blob-opslag voor](../../storage/blobs/scalability-targets.md)meer informatie.

## <a name="resolution"></a>Oplossing

Er zijn drie oplossingen beschikbaar voor deze fout:

* Verhoog de blokgrootte tot maximaal 100 MB. Wijzig in Ambari-gebruikersinterface de `fs.azure.write.request.size` eigenschap HDFS-configuratie (of maak deze in `Custom core-site` sectie). Stel de eigenschap in op een grotere waarde, bijvoorbeeld: 33554432. Sla de bijgewerkte configuratie op en start de betreffende onderdelen opnieuw op.

* Stop en dien de vacature voor vonkstreaming regelmatig opnieuw in.

* HDFS gebruiken om Spark-gebeurtenislogboeken op te slaan. Het gebruik van HDFS voor opslag kan leiden tot verlies van Spark-gebeurtenisgegevens tijdens clusterschaling of Azure-upgrades.

    1. Wijzigingen aanbrengen `spark.eventlog.dir` `spark.history.fs.logDirectory` in en via de Ambari-gebruikersinterface:

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

    1. Start alle betrokken services opnieuw via de Ambari UI.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie raadpleegt u [Hoe u een Azure-ondersteuningsaanvraag maakt.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
