---
title: Fout in Ambari UI 502 van Apache in azure HDInsight
description: Apache Ambari UI 502-fout bij het openen van uw Azure HDInsight-cluster
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/05/2019
ms.openlocfilehash: 0b4975984abf4a36f7d61b42f365eeb1b2902abf
ms.sourcegitcommit: 380e3c893dfeed631b4d8f5983c02f978f3188bf
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/08/2020
ms.locfileid: "75750022"
---
# <a name="scenario-apache-ambari-ui-502-error-in-azure-hdinsight"></a>Scenario: Apache Ambari UI 502-fout in azure HDInsight

In dit artikel worden de stappen beschreven voor het oplossen van problemen en mogelijke oplossingen voor problemen bij het werken met Azure HDInsight-clusters.

## <a name="issue"></a>Probleem

Wanneer u probeert toegang te krijgen tot de Apache Ambari-gebruikers interface voor uw HDInsight-cluster, krijgt u een bericht vergelijkbaar met de volgende tekst: "502-webserver heeft een ongeldig antwoord ontvangen terwijl deze als gateway of proxy server fungeert."

## <a name="cause"></a>Oorzaak

Over het algemeen betekent de HTTP 502-status code dat Ambari-server niet correct wordt uitgevoerd op de actieve hoofd knooppunt. Er zijn enkele mogelijke hoofd oorzaken.

## <a name="resolution"></a>Resolutie

In de meeste gevallen kunt u de actieve hoofd knooppunt opnieuw starten om het probleem te verhelpen. Of kies een grotere VM-grootte voor uw hoofd knooppunt.

### <a name="ambari-server-failed-to-start"></a>Ambari-server kan niet worden gestart

U kunt de logboeken van ambari-server controleren om erachter te komen waarom de Ambari-server niet kan worden gestart. Een veelvoorkomende reden is de fout bij de consistentie controle van de data base. U kunt dit vinden in dit logboek bestand: `/var/log/ambari-server/ambari-server-check-database.log`.

Als u wijzigingen in het cluster knooppunt hebt aangebracht, kunt u deze ongedaan maken. Gebruik altijd de Ambari-gebruikers interface om eventuele aan Hadoop/Spark gerelateerde configuraties te wijzigen.

### <a name="ambari-server-taking-100-cpu-utilization"></a>Ambari-server met 100% CPU-gebruik

In zeldzame gevallen hebben we geambarid dat het CPU-gebruik van 100% voortdurend wordt bereikt. Als oplossing kunt u de actieve hoofd knooppunt sshiseren en het Ambari-Server proces beëindigen en opnieuw starten.

```bash
ps -ef | grep AmbariServer
top -p <ambari-server-pid>
kill -9 <ambari-server-pid>
service ambari-server start
```

### <a name="ambari-server-killed-by-oom-killer"></a>Ambari-server afgebroken door oom-Killer

In sommige gevallen wordt uw hoofd knooppunt te weinig geheugen en de Linux oom-Killer begint met het kiezen van processen om af te breken. U kunt deze situatie controleren door te zoeken in de AmbariServer-proces-ID, die niet kan worden gevonden. Kijk vervolgens naar uw `/var/log/syslog`en zoek er ongeveer als volgt uit:

```
Jul 27 15:29:30 xxx-xxxxxx kernel: [874192.703153] java invoked oom-killer: gfp_mask=0x23201ca, order=0, oom_score_adj=0
```

Vervolgens kunt u vaststellen welke processen herinneringen nemen en de hoofd oorzaak proberen.

### <a name="other-issues-with-ambari-server"></a>Andere problemen met de Ambari-server

Zelden de Ambari-server kan de binnenkomende aanvraag niet afhandelen. u kunt meer informatie vinden door de Ambari-server logboeken te raadplegen voor elke fout. Een dergelijk geval is een fout zoals:

```
Error Processing URI: /api/v1/clusters/xxxxxx/host_components - (java.lang.OutOfMemoryError) Java heap space
```

## <a name="next-steps"></a>Volgende stappen

Als u het probleem niet ziet of als u het probleem niet kunt oplossen, gaat u naar een van de volgende kanalen voor meer ondersteuning:

* Krijg antwoorden van Azure-experts via de [ondersteuning van Azure Community](https://azure.microsoft.com/support/community/).

* Maak verbinding met [@AzureSupport](https://twitter.com/azuresupport) -het officiële Microsoft Azure account voor het verbeteren van de gebruikers ervaring door de Azure-community te verbinden met de juiste resources: antwoorden, ondersteuning en experts.

* Als u meer hulp nodig hebt, kunt u een ondersteunings aanvraag indienen via de [Azure Portal](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade/). Selecteer **ondersteuning** in de menu balk of open de hub **Help en ondersteuning** . Lees voor meer gedetailleerde informatie [hoe u een ondersteunings aanvraag voor Azure maakt](https://docs.microsoft.com/azure/azure-supportability/how-to-create-azure-support-request). De toegang tot abonnementen voor abonnements beheer en facturering is inbegrepen bij uw Microsoft Azure-abonnement en technische ondersteuning wordt geleverd via een van de [ondersteunings abonnementen voor Azure](https://azure.microsoft.com/support/plans/).
