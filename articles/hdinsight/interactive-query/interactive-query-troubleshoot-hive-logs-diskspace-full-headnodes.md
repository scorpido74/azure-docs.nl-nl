---
title: 'Problemen oplossen: Apache Hive logboeken schijf ruimte opvullen-Azure HDInsight'
description: In dit artikel worden de stappen beschreven voor het oplossen van problemen wanneer Apache Hive Logboeken de schijf ruimte op de hoofd knooppunten in azure HDInsight invult.
ms.service: hdinsight
ms.topic: troubleshooting
author: nisgoel
ms.author: nisgoel
ms.reviewer: jasonh
ms.date: 10/05/2020
ms.openlocfilehash: 64bf5714f5eb99df9929a47fef414a827ec680af
ms.sourcegitcommit: 4b76c284eb3d2b81b103430371a10abb912a83f4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/01/2020
ms.locfileid: "93145630"
---
# <a name="scenario-apache-hive-logs-are-filling-up-the-disk-space-on-the-head-nodes-in-azure-hdinsight"></a>Scenario: Apache Hive-logboeken worden de schijf ruimte op de hoofd knooppunten in azure HDInsight gevuld

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen met betrekking tot onvoldoende schijf ruimte op de hoofd knooppunten in azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Op een Apache Hive-LLAP-cluster nemen ongewenste Logboeken de volledige schijf ruimte op de hoofd knooppunten in beslag. Dit probleem kan de volgende problemen veroorzaken:

- SSH-toegang mislukt omdat er geen ruimte meer is op het hoofd knooppunt.
- Ambari genereert een *HTTP-fout: de 503-Service is niet beschikbaar* .
- HiveServer2 Interactive kan niet opnieuw worden opgestart.

De `ambari-agent` Logboeken bevatten de volgende vermeldingen wanneer het probleem optreedt:
```
ambari_agent - Controller.py - [54697] - Controller - ERROR - Error:[Errno 28] No space left on device
```
```
ambari_agent - HostCheckReportFileHandler.py - [54697] - ambari_agent.HostCheckReportFileHandler - ERROR - Can't write host check file at /var/lib/ambari-agent/data/hostcheck.result
```

## <a name="cause"></a>Oorzaak

In geavanceerde configuraties van Hive-log4j is het huidige standaard verwijderings schema het verwijderen van bestanden die ouder zijn dan 30 dagen, op basis van de datum van de laatste wijziging.

## <a name="resolution"></a>Oplossing

1. Ga naar het onderdeel overzicht van Hive-onderdelen op de Ambari-Portal en selecteer het tabblad **configuratie** .

2. Ga naar de `Advanced hive-log4j` sectie in **Geavanceerde instellingen** .

3. Stel de `appender.RFA.strategy.action.condition.age` para meter in op een leeftijd van uw keuze. In dit voor beeld wordt de leeftijd ingesteld op 14 dagen: `appender.RFA.strategy.action.condition.age = 14D`

4. Als u geen gerelateerde instellingen ziet, voegt u deze instellingen toe:
    ```
    # automatically delete hive log
    appender.RFA.strategy.action.type = Delete
    appender.RFA.strategy.action.basePath = ${sys:hive.log.dir}
    appender.RFA.strategy.action.condition.type = IfLastModified
    appender.RFA.strategy.action.condition.age = 30D
    appender.RFA.strategy.action.PathConditions.type = IfFileName
    appender.RFA.strategy.action.PathConditions.regex = hive*.*log.*
    ```

5. Instellen `hive.root.logger` op `INFO,RFA` , zoals wordt weer gegeven in het volgende voor beeld. De standaard instelling is `DEBUG` , waardoor de logboeken groot zijn.

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

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees voor meer gedetailleerde informatie [hoe u een ondersteunings aanvraag voor Azure maakt](../../azure-portal/supportability/how-to-create-azure-support-request.md). De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
