---
title: Apache Hive Logs vullen schijfruimte - Azure HDInsight
description: De Apache Hive-logboeken vullen de schijfruimte op de hoofdknooppunten in Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 03/05/2020
ms.openlocfilehash: d843b942702d335065a5f3798572e34c71b4cd0e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/28/2020
ms.locfileid: "78943973"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Scenario: Apache Hive-logboeken vullen de schijfruimte op de hoofdknooppunten in Azure HDInsight

In dit artikel worden stappen voor het oplossen van problemen en mogelijke oplossingen beschreven voor problemen die verband houden met onvoldoende schijfruimte op de hoofdknooppunten in Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Op een Apache Hive/LLAP-cluster nemen ongewenste logboeken de volledige schijfruimte op de hoofdknooppunten in beslag. Hierdoor konden volgende problemen worden gezien.

1. SSH-toegang mislukt omdat er geen ruimte op het hoofdknooppunt is.
2. Ambari geeft *HTTP-FOUT: 503-service niet beschikbaar*.

De `ambari-agent` logboeken zouden het volgende laten zien wanneer het probleem zich voordoet.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Oorzaak

In geavanceerde Hive-log4j-configuraties wordt de parameter *log4j.appender.RFA.MaxBackupIndex* weggelaten. Het veroorzaakt eindeloze generatie van logbestanden.

## <a name="resolution"></a>Oplossing

1. Navigeer naar het overzicht van de component `Configs` Hive op de Ambari-portal en klik op het tabblad.

2. Ga naar `Advanced hive-log4j` de sectie binnen Geavanceerde instellingen.

3. Stel `log4j.appender.RFA` de parameter in als RollingFileAppender. 

4. Instellen `log4j.appender.RFA.MaxFileSize` `log4j.appender.RFA.MaxBackupIndex` en als volgt.

```
log4jhive.log.maxfilesize=1024MB
log4jhive.log.maxbackupindex=10

log4j.appender.RFA=org.apache.log4j.RollingFileAppender
log4j.appender.RFA.File=${hive.log.dir}/${hive.log.file}
log4j.appender.RFA.MaxFileSize=${log4jhive.log.maxfilesize}
log4j.appender.RFA.MaxBackupIndex=${log4jhive.log.maxbackupindex}
log4j.appender.RFA.layout=org.apache.log4j.PatternLayout
log4j.appender.RFA.layout.ConversionPattern=%d{ISO8601} %-5p [%t] %c{2}: %m%n
```
5. Stel `hive.root.logger` `INFO,RFA` als volgt in. De standaardinstelling is DEBUG, waardoor logboeken erg groot worden.

```
# Define some default values that can be overridden by system properties
hive.log.threshold=ALL
hive.root.logger=INFO,RFA
hive.log.dir=${java.io.tmpdir}/${user.name}
hive.log.file=hive.log
```

6. Sla de configs op en start de benodigde componenten opnieuw op.

## <a name="next-steps"></a>Volgende stappen

Als je je probleem niet hebt gezien of niet in staat bent om je probleem op te lossen, ga je naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via [Azure Community Support.](https://azure.microsoft.com/support/community/)

* Maak [@AzureSupport](https://twitter.com/azuresupport) verbinding met - het officiële Microsoft Azure-account voor het verbeteren van de klantervaring door de Azure-community te verbinden met de juiste bronnen: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, u een ondersteuningsaanvraag indienen via de [Azure-portal.](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/) Selecteer **Ondersteuning** op de menubalk of open de **Help + ondersteuningshub.** Voor meer gedetailleerde informatie raadpleegt u [Hoe u een Azure-ondersteuningsaanvraag maakt.](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request) Toegang tot abonnementsbeheer en factureringsondersteuning is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geboden via een van de [Azure Support-abonnementen](https://azure.microsoft.com/support/plans/).
