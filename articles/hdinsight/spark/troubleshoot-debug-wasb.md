---
title: Wasb-bestandsbewerkingen debuggen in Azure HDInsight
description: Beschrijft stappen voor het oplossen van problemen en mogelijke oplossingen voor problemen bij interactie met Azure HDInsight-clusters.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: troubleshooting
ms.date: 02/18/2020
ms.openlocfilehash: f1707c7f8d6324678c8bf5a470bbded1e58c719e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2020
ms.locfileid: "77470714"
---
# <a name="debug-wasb-file-operations-in-azure-hdinsight"></a>Wasb-bestandsbewerkingen debuggen in Azure HDInsight

Er zijn momenten waarop u wilt weten welke bewerkingen het WASB-stuurprogramma is gestart met Azure Storage. Voor de clientzijde produceert het WASB-stuurprogramma logboeken voor elke bestandssysteembewerking op **DEBUG-niveau.** WASB-stuurprogramma gebruikt log4j om het logboekniveau te beheren en het standaardis **INFO-niveau.** Zie Analyselogboekregistratie azure [storage-analyses](../../storage/common/storage-analytics-logging.md)voor Azure Storage-analyselogboeken.

Een geproduceerd logboek lijkt op:

```log
18/05/13 04:15:55 DEBUG NativeAzureFileSystem: Moving wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/_temporary/0/_temporary/attempt_20180513041552_0000_m_000000_0/part-00000 to wasb://xxx@yyy.blob.core.windows.net/user/livy/ulysses.txt/part-00000
```

## <a name="turn-on-wasb-debug-log-for-file-operations"></a>WASB-foutopsporingslogboek inschakelen voor bestandsbewerkingen

1. Navigeer vanuit een webbrowser `https://CLUSTERNAME.azurehdinsight.net/#/main/services/SPARK2/configs`naar `CLUSTERNAME` , waar is de naam van uw Spark-cluster.

1. Navigeer naar **geavanceerde spark2-log4j-eigenschappen**.

    1. Wijzigen `log4j.appender.console.Threshold=INFO` `log4j.appender.console.Threshold=DEBUG`in .

    1. Toevoegen `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`.

1. Navigeer naar **Geavanceerde eigenschappen livy2-log4j.**

    Toevoegen `log4j.logger.org.apache.hadoop.fs.azure.NativeAzureFileSystem=DEBUG`.

1. Wijzigingen opslaan.

## <a name="additional-logging"></a>Aanvullende logboekregistratie

De bovenstaande logboeken moeten inzicht bieden op hoog niveau in de bewerkingen van het bestandssysteem. Als de bovenstaande logboeken nog steeds geen nuttige informatie opleveren of `fs.azure.storage.client.logging=true` als `core-site`u blob-opslagapi-aanroepen wilt onderzoeken, voegt u toe aan de . Deze instelling maakt de java sdk-logboeken voor wasb-opslagstuurprogramma mogelijk en drukt elke oproep af op blob-opslagserver. Verwijder de instelling na onderzoeken omdat deze de schijf snel kan opvullen en het proces kan vertragen.

Als de backend op Azure Data Lake is gebaseerd, gebruikt u de volgende log4j-instelling voor de component (bijvoorbeeld spark/tez/hdfs):

```
log4j.logger.com.microsoft.azure.datalake.store=ALL,adlsFile
log4j.additivity.com.microsoft.azure.datalake.store=true
log4j.appender.adlsFile=org.apache.log4j.FileAppender
log4j.appender.adlsFile.File=/var/log/adl/adl.log
log4j.appender.adlsFile.layout=org.apache.log4j.PatternLayout
log4j.appender.adlsFile.layout.ConversionPattern=%p\t%d{ISO8601}\t%r\t%c\t[%t]\t%m%n
```

Kijk voor de `/var/log/adl/adl.log` logs in voor de logs.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring. De Azure-community verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie, bekijk [Hoe maak je een Azure-ondersteuningsaanvraag](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
