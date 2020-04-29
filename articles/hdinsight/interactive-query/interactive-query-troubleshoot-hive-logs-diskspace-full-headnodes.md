---
title: Apache Hive logboeken schijf ruimte invullen-Azure HDInsight
description: De Apache Hive-logboeken vullen de schijf ruimte op de hoofd knooppunten in azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 03/05/2020
ms.openlocfilehash: d843b942702d335065a5f3798572e34c71b4cd0e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78943973"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Scenario: Apache Hive-logboeken worden de schijf ruimte op de hoofd knooppunten in azure HDInsight gevuld

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen met betrekking tot onvoldoende schijf ruimte op de hoofd knooppunten in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Op een Apache Hive-LLAP-cluster nemen ongewenste Logboeken de volledige schijf ruimte op de hoofd knooppunten in beslag. Als gevolg hiervan kunnen de volgende problemen worden weer gegeven.

1. SSH-toegang mislukt omdat er geen ruimte meer is op het hoofd knooppunt.
2. Ambari geeft *HTTP-fout: de 503-Service is niet beschikbaar*.

In `ambari-agent` de logboeken ziet u het volgende wanneer het probleem optreedt.
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Oorzaak

In geavanceerde configuraties van Hive-log4j wordt de para meter *log4j. appender. rfa. MaxBackupIndex* wegge laten. Het leidt ertoe dat logboek bestanden oneindig worden gegenereerd.

## <a name="resolution"></a>Oplossing

1. Ga naar samen vatting van Hive-onderdeel in de Ambari- `Configs` Portal en klik op het tabblad.

2. Ga naar de `Advanced hive-log4j` sectie in geavanceerde instellingen.

3. Stel `log4j.appender.RFA` para meter in als RollingFileAppender. 

4. Instellen `log4j.appender.RFA.MaxFileSize` en `log4j.appender.RFA.MaxBackupIndex` als volgt.

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
5. Stel `hive.root.logger` als `INFO,RFA` volgt in. De standaard instelling is DEBUG, waardoor logboeken erg groot worden.

```
# Define some default values that can be overridden by system properties
hive.log.threshold=ALL
hive.root.logger=INFO,RFA
hive.log.dir=${java.io.tmpdir}/${user.name}
hive.log.file=hive.log
```

6. Sla de configuraties op en start de vereiste onderdelen opnieuw.

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding [@AzureSupport](https://twitter.com/azuresupport) met-het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees voor meer gedetailleerde informatie [hoe u een ondersteunings aanvraag voor Azure maakt](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request). De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
